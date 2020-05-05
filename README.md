# OSM_Bridge_Japan

Bridge data in Japan@OpenStreetMap.

OpenStreetMapにある、日本国内の橋梁の情報を抜き出したデータ。

Download source : https://download.geofabrik.de/asia/japan.html

Date of Data : 2020-05-03T20:59:02Z

Copyright : © OpenStreetMap contributors

License : ODbL https://opendatacommons.org/licenses/odbl/

## データのダウンロード
* https://download.geofabrik.de/asia/japan.html
    * ラインデータ以外は日本全土をまとめて作る。japan-latest.osm.pbfから。
    * ラインデータはファイルサイズが大きく、githubにアップロードできない（githubは100MB以内の制限あり）ため、地域別のファイルを落とし、それぞれに作る。
## データ加工に使うツール
* osmconvert
    * https://wiki.openstreetmap.org/wiki/Osmconvert
        * .pbfファイルを.osmや.o5mなどのファイル形式に変換する。
            * この次の手順で使う、osmfilterが.pbfを直接扱えないため。

* osmfilter
    * https://wiki.openstreetmap.org/wiki/Osmfilter
        * OSMのデータから欲しいデータだけを抽出するためのツール。特定のタグや値のみを検索して抽出／逆抽出したりできる。

* osmtogeojson
    * https://github.com/tyrasd/osmtogeojson
        * .osmファイルを構造を保ったまま.geojsonに変換できるツール。JavaScript（node.js）。
        * npm経由でインストール。
        * メモリの割り当てがデフォルトだと少ないので、マニュアルに従って、増やしてあげる必要がある。


## データ加工の手順

1. ダウンロードしてきた.pbfファイルを、osmconvertを使って.o5mファイルに変換する
```
osmconvert japan-latest.osm.pbf -o=japan-latest.o5m
```

2. 1.で出来た.o5mファイルの中身から"bridge"というタグあるいは値の入ったPOIを抽出する。
```
osmfilter japan-latest.o5m --keep="*bridge*" > bridge_japan-latest.osm
```

3. QGISを挟むとレイヤーごとにファイルが別れてしまう（.osmも.geojsonも複数の地物タイプ（point, line etc...）を1つのファイル内に持たせられるのが特徴）のがスマートではないので、.osmファイルの構造を崩さずに直接.geojsonファイルに変換する。
    3.1 メモリの割り当てを16GBにしている。
```
node --max_old_space_size=16384 '/usr/local/bin/osmtogeojson' bridge_japan-latest.osm > bridge_japan-latest.geojson
```
