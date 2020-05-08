# OSM_Bridge_Japan

Bridge data in Japan@OpenStreetMap.

OpenStreetMapにある、日本国内の橋梁の情報を抜き出したデータ。

Download source : https://download.geofabrik.de/asia/japan.html

Date of Data : 2020-05-02T20:59:02Z

Copyright : © OpenStreetMap contributors ( https://www.openstreetmap.org/copyright )

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

* QGIS
    * https://qgis.org/
        * 本来であれば、直接OGRのogr2ogrを使って変換するのがスマート。
            * うまくコードが書けなかった（笑）ので、QGISを使った。


## データ加工の手順

1. ダウンロードしてきた.pbfファイルを、osmconvertを使って.o5mファイルに変換する
```
osmconvert japan-latest.osm.pbf -o=japan-latest.o5m
```

2. 1.で出来た.o5mファイルの中身から"bridge"というタグあるいは値の入ったPOIを抽出する。
```
osmfilter japan-latest.o5m --keep="*bridge*" > bridge_japan20200501.osm
```

3. 2.で出来た.osmファイルをQGISで開いて、それぞれの要素（multilinestrings, multipolygons, other_relations, points）ごとに個別のファイルとしてGeoJSON形式で保存する。linesデータはファイルサイズが大きくなるので、各地域単位で切り出す。この際、CRSはWGS84（EPSG:4326）で保存しておく。
