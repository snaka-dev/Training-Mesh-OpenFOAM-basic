# OpenFOAMによるOpenFOAMのためのメッシュ生成（はじめの一歩）
## June 15, 2019; OpenCAE勉強会＠富山
### 中川慎二（富山県立大学）[Shinji NAKAGAWA，Toyama Prefectural University]  


## OpenFOAMでのメッシュ生成方法

### 概要

OpenFOAM には，メッシュを生成・操作するための多くのユーティリティが付属している。

比較的単純なメッシュは，blockMeshユーティリティを使用して作成できる。

少し複雑なメッシュは，任意形状のSTLファイルに適合したメッシュが生成可能な snappyHexMesh ユーティリティを使用して生成できる。さらに foamyHexMesh というユーティリティも存在する。foamyHexMesh よりも snappyHexMesh の方が歴史が古く，使用例は多い。

OpenFOAM に付属するものではないが，OpenFOAM のために開発されているメッシュ生成ソースソフトウェア cfMesh も存在する。OpenCFD社がリリースする最近のOpenFOAM（OpenFOAM v1806など）では，cfMesh がOpenFOAMのインストールに含まれている．ただし，標準のユーティリティではないため，ソースコードや例題の格納場所が異なる．

その他にも，多くのオープンソースソフトウェアや，商用ソフトウェアでメッシュを生成することが可能である。

今回の講習では，主に blockMesh および snappyHexMesh の使用方法について学ぶ。


### メッシュ関連例題の確認

例題ディレクトリで，メッシュ作成関連例題を確認する。メッシュ作成に特化した例題は，tutorials/meshにまとめられている。

講習会用仮想マシンの端末を起動し，メッシュ関連例題の一覧を表示した例を次に示す．

``` bash
OpenFOAM installed: of1812, of6
user@user-VirtualBox:~$ of1812 
user@user-VirtualBox:~$ cd $FOAM_TUTORIALS/mesh
user@user-VirtualBox:~/OpenFOAM/OpenFOAM-v1812/tutorials/mesh$ pwd
/home/user/OpenFOAM/OpenFOAM-v1812/tutorials/mesh
user@user-VirtualBox:~/OpenFOAM/OpenFOAM-v1812/tutorials/mesh$ tree -L 2
.
├── blockMesh
│   ├── pipe
│   ├── sphere
│   ├── sphere7
│   └── sphere7ProjectedEdges
├── foamyHexMesh
│   ├── Allrun
│   ├── blob
│   ├── flange
│   ├── mixerVessel
│   ├── simpleShapes
│   └── straightDuctImplicit -> ../../incompressible/porousSimpleFoam/straightDuctImplicit
├── foamyQuadMesh
│   ├── jaggedBoundary
│   ├── OpenCFD
│   └── square
├── moveDynamicMesh
│   ├── relativeMotion
│   ├── SnakeRiverCanyon
│   └── twistingColumn
├── parallel
│   ├── cavity
│   └── filter
├── refineMesh
│   └── refineFieldDirs
├── snappyHexMesh
│   ├── addLayersToFaceZone
│   ├── aerofoilNACA0012_directionalRefinement
│   ├── Allrun
│   ├── flange
│   ├── gap_detection
│   ├── iglooWithFridges -> ../../heatTransfer/buoyantBoussinesqSimpleFoam/iglooWithFridges
│   ├── iglooWithFridgesDirectionalRefinement
│   ├── motorBike -> ../../incompressible/simpleFoam/motorBike
│   ├── motorBike_leakDetection
│   └── snappyMultiRegionHeater -> ../../heatTransfer/chtMultiRegionFoam/snappyMultiRegionHeater
└── stitchMesh
    └── simple-cube1

36 directories, 2 files
```

これ以外にも，様々なソルバ用例題において，多様なメッシュが作成されている。  

OpenFOAM の開発者ではなく，Creative Fields Holding Ltd が開発している cfMesh というライブラリが存在する。OpenFOAM v1812 には，GPL版のcfMeshが同梱されている。そのソースコード等は，$WM_PROJECT_DIR/modules/cfmesh/ に格納されている。

cfMesh にも多くの例題が付属しており，その使い方を知ることができる。例題の一覧を下に示す。

```
user@user-VirtualBox:~/OpenFOAM/OpenFOAM-v1812/modules/cfmesh/tutorials$ pwd
/home/user/OpenFOAM/OpenFOAM-v1812/modules/cfmesh/tutorials
user@user-VirtualBox:~/OpenFOAM/OpenFOAM-v1812/modules/cfmesh/tutorials$ tree -L 2
.
├── Allclean
├── Allrun
├── cartesian2DMesh
│   └── hatOctree
├── cartesianMesh
│   ├── asmoOctree
│   ├── bunnyOctree
│   ├── elbow_90degree
│   ├── intakePortOctree
│   ├── multipleOrifices
│   ├── sawOctree
│   ├── sBendOctree
│   ├── ship5415Octree
│   └── singleOrifice
├── pMesh
│   ├── bunnyPoly
│   └── multipleOrifices
└── tetMesh
    ├── cutCubeOctree
    └── socketOctree

18 directories, 2 files
```
