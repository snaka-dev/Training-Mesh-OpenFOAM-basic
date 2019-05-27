# OpenFOAMによるOpenFOAMのためのメッシュ生成（はじめの一歩）
## June 15, 2019; OpenCAE勉強会＠富山
### 中川慎二（富山県立大学）[Shinji NAKAGAWA，Toyama Prefectural University]  


## snappyHexMesh

snappyHexMesh では，複雑な形状に沿ったメッシュを作成できる。用意するものは，形状を表すファイル（STL形式など），ベースとなる六面体メッシュ（blockMeshで作成），設定ファイル（snappyHexMeshDict）である。

snappyHexMeshの設定は，非常に多くの項目が存在する。大きく分類した設定項目は下記である。

- castellatedMesh: スイッチ． 城郭風（凸凹な壁）の作成．
- snap: スイッチ． 凸凹なメッシュを形状に適合させる．
- addLayers: スイッチ． 層状のメッシュを追加する．
- geometry: サブディクショナリ．形状を表す面の定義．
- castellatedMeshControls: サブディクショナリ．城郭風メッシュの作成条件．
- snapControls:  サブディクショナリ．形状適合の条件．
- addLayersControls:  サブディクショナリ．レイヤーセルの作成条件．
- meshQualityControls:  サブディクショナリ．メッシュ品質の判定条件．
- writeFlags: セルに関する情報の出力制御
- mergeTolerance: 許容値．全体をカバーするボックスに対する相対値．

基本的な例題を実行して，何が行なわれているかを確認する。

### snappyHexMesh/flange例題の作業ディレクトリへのコピー

ユーザーの作業ディレクトリ（`$FOAM_RUN`）に，flange例題をコピーする。

GUIで操作する場合には，ファイルマネージャーを起動し，`$FOAM_TUTORIALS/mesh/snappyHexMesh/flange` ディレクトリをコピーし，`$FOAM_RUN` へペーストする。

コマンドラインで操作する場合には，下記を実行する。2つめのコマンドの最後には，スペースとピリオドがあることに注意してください．

> cd $FOAM_RUN
>
> cp -r $FOAM_TUTORIALS/mesh/snappyHexMesh/flange .

### flange例題の実行と確認

まずは，例題をそのまま

ファイルマネージャーで，`$FOAM_RUN/flange`  まで移動する。ファイルマネージャー上で右クリックして，「Open Terminal Here」をクリックして端末を起動する。

端末で，下記コマンドを実行し，OpenFOAM v1812 を有効にする．
> of1812

Allrunスクリプトを実行する。
> ./Allrun

paraFoam を実行し，メッシュを見る。

| <img src="images/flange01.png" alt="mesh from flange tutorial" title="mesh from flange tutorial" width="300px"> |
| :--------------------------------------: |
|       図 　mesh from flange tutorial       |

snappyHexMeshDictに，設定が記載されている。標準のままでは，一部の形状が正確に再現されない。それを確認するために，paraviewのFileメニューからOpenを選択し，$FOAM_RUN/flange/constant/extendedFeatureEdgeMesh/flange_externalEdges.obj ファイルを開く。このファイルには，surfaceFeatureExtract ユーティリティで取り出した特徴線が書かれている。

### flange例題のステップ実行と確認

先ほどの端末で，実行結果を削除するため，Allcleanを実行する。
> ./Allclean

ここから，手作業で一つ一つのコマンドを実行して，何が行なわれたかを確認していく。

まず，形状ファイルのコピーと展開をするため，下記コマンドを実行する。
> cp $FOAM_TUTORIALS/resources/geometry/flange.stl.gz constant/triSurface/
>
> uncompress constant/triSurface/flange.stl.gz 

blockMeshを実行する。
> blockMesh

メッシュを見る。
> paraFoam

Wiremesh表示にした上で，ParaViewのFileメニューからOpenを選択し，constant/triSurface/flange.stlを開く。blockMeshで作成したメッシュの内部に，対象物が入っていることを確認する。

| <img src="images/flange_stl_blockMesh.png" alt="blockMesh and flange.stl" title="blockMesh and flange.stl" width="600px"> |
| :--------------------------------------: |
|  図 　blockMesh and flange.stl   |

ParaView を終了し，surfaceFeatureExtract を実行する。/constantディレクトリの下にファイルが増えることを確認する。

snappyHexMeshDict 105行目付近で，細分化レベルを上げるようにすると，形状の再現性が向上する。修正例は下記となる。

```
    refinementSurfaces
    {
        flange
        {
            // Surface-wise min and max refinement level
            level (2 3); //2);
        }
    }
```

ここまでの準備ができたら，snappyHexMeshを実行する。（-overwriteオプションは使わない。）
> snappyHexMesh

overwriteオプションを使わない場合，castellatedMesh や snap のメッシュ生成過程が保存される。ディレクトリ1には，形状に合わせて，不要なセルを取り除いただけの状態が保存されている。ディレクトリ2には，はみ出したセルを形状に合わせてスナップしたセルが格納される。

作成されたメッシュをparaFoamで確認する。
> paraFoam

| <img src="images/flange_modified01.png" alt="mesh from modified flange tutorial" title="mesh from modified flange tutorial" width="600px"> |
| :--------------------------------------: |
|  図 　mesh from modified flange tutorial   |

#### レイヤー追加操作の追加

先ほど作成したメッシュに，レイヤーを追加する。snappyHexMeshDictのスイッチを操作することで，すでに作成済みのメッシュにレイヤーだけを追加することができる。snappyHexMeshDictの冒頭部分を次のように変更する。

```
// Which of the steps to run
castellatedMesh false; //true;
snap            false; //true;
addLayers       true; //false;

```

レイヤーは，パッチ名を指定して追加する。200行目付近を変更し，flange_patch4だけにレイヤーを追加する。

```
    // Per final patch (so not geometry!) the layer information
    layers
    {
        flange_patch4   //"flange_.*"
        {
            nSurfaceLayers 1;
        }
    }
```

controlDictにおいて，startTime をlatestTime に変更する。これは，最新状態のメッシュを読み込んで，新たなメッシュを作成するため。
```
startFrom    latestTime; //startTime;
```

ここまでの準備ができたら，下記コマンドを実行する。これにより，新たなディレクトリ 3 が生成され，その中にレイヤーが追加されたメッシュが保存されている。
> snappyHexMesh

なお，addLayerを当初から実行するときと，後で追加実行するときとで，作成されるメッシュが異なることがある。後から追加する方が，キレイなメッシュができることがある。


#### パッチ名について

patch名は，geometry欄で指定した名前と，STLファイル内のsolid名から名付けられている．

### その他

複数のファイルを組み合わせて，複数領域を有するメッシュを作成可能である．（$FOAM_TUTORIALS/mesh/snappyHexMesh/snappyMultiRegionHeater/例題）

snappyHexMeshDict内で単純な形状を作成することも可能である．（$FOAM_TUTORIALS/mesh/snappyHexMesh/iglooWithFridges/例題）



## [目次へ戻る](index_j.md)