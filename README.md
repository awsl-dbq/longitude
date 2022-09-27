# Longitude
Longitude Project for TiDB Hackathon 2022 !

## Team Name
Cat Commando
## Team Slogan
someone coding, someone run!
## Project Name
longitude
## Introduction
Currently, TiDB do not support any Geo feature.To break this, Cat-Commando will try to add some Geo feature for TiDB.

The plan show as follows:
    1. add GeoJSON datatype
    2. add some geographic analysis functions based on GeoJSON
    3. build a demo

Okay, Fine, Here we Go!

## Design
### What's GeoJSON?
JSON + Schema(GIS) => GeoJSON

It's a standard data format for GIS data based on JSON.


https://geojson.org/

https://doc.arcgis.com/en/arcgis-online/reference/geojson.htm

Example:
```
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [125.6, 10.1]
  },
  "properties": {
    "name": "Dinagat Islands"
  }
}
```

### add GeoJSON datatype
Expected Input and output:
1. create table
```
mysql> CREATE TABLE city_geo_table (
    ->     id INT PRIMARY KEY,
    ->     position GEOJSON
    -> );
Query OK, 0 rows affected (0.03 sec)

```
2. describe table
```
mysql> desc city_geo_table;
+----------+---------+------+------+---------+-------+
| Field    | Type    | Null | Key  | Default | Extra |
+----------+---------+------+------+---------+-------+
| id       | int(11) | NO   | PRI  | NULL    |       |
| position | geojson | YES  |      | NULL    |       |
+----------+---------+------+------+---------+-------+
2 rows in set (0.00 sec)
```
3. insert data
```
mysql> INSERT INTO city_geo_table (id,position) VALUES (1, '{"name": "Beijing", "lng": 80,"lat":90}');
Query OK, 1 row affected (0.01 sec)

```
4. select data
```
mysql> select * from city_geo_table;
+----+-------------------------------------------+
| id | position                                  |
+----+-------------------------------------------+
|  1 | {"lat": 90, "lng": 80, "name": "Beijing"} |
+----+-------------------------------------------+
1 row in set (0.00 sec)
```


### geographic analysis functions based on GeoJSON

1. GeoJSON_TYPE
```
mysql> select id,GeoJSON_TYPE(position) as 'geotype' from city_geo_table;
+----+-------------------------------------------+
| id | geotype                                  |
+----+-------------------------------------------+
|  1 | point                                    |
+----+-------------------------------------------+
1 row in set (0.00 sec)
```

2. GeoJSON_buffer
    ...
3. GeoJSON_union
    ...
4. GeoJSON_intersect
    ...

### Demo Show
.....

## Why GeoJSON?
....

## Future Plan
JSON is base datatype in Tidb now. GeoJSON is a particular datatype for a particular domain: GIS.
However, we can try to create the domain specific datatype in runtime.

```
create domain GIS with schema 'gis.json.schema'; // geographic information system
=> then we will support GeoJSON datatype in TiDB
create domain HIS with schema 'his.json.schema'; // hospital information system
=> then we will support HisJSON datatype in TiDB
create domain FIS with schema 'fis.json.schema'; // finacial information system
=> then we will support FisJSON datatype in TiDB
```

Of course, only domain specific datatype is not enough. we also need domain related functions.
The UDF plugins may be a good choice.