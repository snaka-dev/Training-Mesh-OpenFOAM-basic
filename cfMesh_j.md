# OpenFOAMによるOpenFOAMのためのメッシュ生成（はじめの一歩）
## June 15, 2019; OpenCAE勉強会＠富山
### 中川慎二（富山県立大学）[Shinji NAKAGAWA，Toyama Prefectural University]  


## cfMesh

例題ディレクトリ

/home/user/OpenFOAM/OpenFOAM-v1812/modules/cfmesh/tutorials/

snappyHexMeshと同様なメッシュを生成するためには， cartesianMesh コマンドを実行する。その2次元メッシュ用が cartesian2DMesh である。

このほかに，tetrahedralメッシュを作成するための tetMesh コマンド，polyhedralメッシュを作成するための pMesh コマンドが存在する。

