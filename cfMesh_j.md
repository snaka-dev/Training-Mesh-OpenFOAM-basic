# OpenFOAMによるOpenFOAMのためのメッシュ生成（はじめの一歩）
## June 15, 2019; OpenCAE勉強会＠富山
### 中川慎二（富山県立大学）[Shinji NAKAGAWA，Toyama Prefectural University]  


## cfMesh

例題ディレクトリ

/home/user/OpenFOAM/OpenFOAM-v1812/modules/cfmesh/tutorials/

snappyHexMeshと同様なメッシュを生成するためには， cartesianMesh コマンドを実行する。その2次元メッシュ用が cartesian2DMesh である。

このほかに，tetrahedralメッシュを作成するための tetMesh コマンド，polyhedralメッシュを作成するための pMesh コマンドが存在する。

### cfMesh の設定ファイル meshDict

cfMesh の設定は，system/meshDict ファイルに記載する。

必須記載事項は次の通りである。
- surfaceFile: 形状を表すファイルを指定する
- maxCellSize: 基準となるセルの大きさを指定する

このほかに，細分化，境界層レイヤーなどを指定できる。

### cfMesh/cartesianMeshの標準例題 elbow_90degree

/home/user/OpenFOAM/OpenFOAM-v1812/modules/cfmesh/tutorials/cartesianMesh/elbow_90degree/

Allrun の内容は次の通りである。

```
#!/bin/sh
cd ${0%/*} || exit 1                        # Run from this directory
. $WM_PROJECT_DIR/bin/tools/RunFunctions    # Tutorial run functions

runApplication cartesianMesh
runApplication checkMesh -constant

#------------------------------------------------------------------------------
```


system/meshDict の内容は次の通りである。

```
surfaceFile "elbow_90degree.stl";
minCellSize 1.0;
maxCellSize 5.0;
boundaryCellSize 3.0;

localRefinement
{
    "ringArea.*"
    {
        cellSize 0.2;
    }
}

boundaryLayers
{
    nLayers 5;
    thicknessRatio 1.1;
    maxFirstLayerThickness 0.5;
}

renameBoundary
{
    defaultName     fixedWalls;
    defaultType     wall;

    newPatchNames
    {
        "inlet.*"
        {
            type    patch;
            newName inlet;
        }

        "outlet.*"
        {
            type    patch;
            newName outlet;
        }
    }
}
```

| <img src="images/cfMesh_elbow_zCenterSlice.png" alt="cfMesh_elbow_zCenterSlice" title="cfMesh_elbow_zCenterSlice" width="400px"> |
| :--------------------------------------: |
|     図 　cfMesh_elbow_zCenterSlice      |


### elbow_90degree 例題を簡単な設定で実行してみる

>
>cp system/meshDict system/meshDict.orig

system/meshDict の内容を，下記の通りとして実行してみる。

```
surfaceFile "elbow_90degree.stl";
maxCellSize 5.0;
boundaryCellSize 3.0;
```


| <img src="images/cfMesh_elbow_simplestSetting.png" alt="cfMesh_elbow_simpleSetting" title="cfMesh_elbow_simpleSetting" width="400px"> |
| :--------------------------------------: |
|     図 　cfMesh_elbow_simpleSetting      |


boundaryCellSize を少し小さくする。

| <img src="images/cfMesh_elbow_simpleSetting01.png" alt="cfMesh_elbow_simpleSetting" title="cfMesh_elbow_simpleSetting" width="400px"> |
| :--------------------------------------: |
|     図 　cfMesh_elbow_simpleSetting      |


局所的細分化の適用例

```
surfaceFile "elbow_90degree.stl";
maxCellSize 5.0;
boundaryCellSize 2.0;
localRefinement
{
    "ringArea.*"
    {
        cellSize 1; //0.5; //0.2;
    }
}
```

| <img src="images/cfMesh_elbow_ringRefinement.png" alt="cfMesh_elbow_cfMesh_elbow_ringRefinement" title="cfMesh_elbow_cfMesh_elbow_ringRefinement" width="400px"> |
| :--------------------------------------: |
|     図 　cfMesh_elbow_cfMesh_elbow_ringRefinement      |


### 新たなメッシュ作成に挑戦

flange.stl をコピーする。

meshDict 内のファイル名を変更する。

flange に適した maxCellSize に変更する。

cartesianMesh を実行する。

作成されたメッシュを確認する。


### 特徴線

surfaceFeatureEdges ユーティリティを使用し，stlファイルから特徴線を抽出し，fmsファイルを作成する。

stlファイルの代わりに，fmsファイルを使ってメッシュを生成する。





## [目次へ戻る](index_j.md)