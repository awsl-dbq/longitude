# Longitude
Longitude Project for TiDB Hackathon 2022 !

## Team Name
Cat Commando
## Team Slogan
someone coding, someone run!
## Project Name
longitude

## Notice
Sorry, I didn't notice the rules changed this year.
Coding ahead is not allowed this year.
Sadly, I have already do some coding in National Holiday.
To show the fairness, I need to update my RFC to new one before the Hackathon coding begin.

To not change too much, I treat the origin RFC 's future plan as my new RFC's starts.  

## Introduction (outdated)
Currently, TiDB do not support any Geo feature.To break this, Cat-Commando will try to add some Geo feature for TiDB.

The plan show as follows:
  1. add GeoJSON datatype
  2. add some geographic analysis functions based on GeoJSON
  3. build a demo

Okay, Fine, Here we Go!



## Design (outdated)
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
mysql> INSERT INTO city_geo_table (id,position) VALUES (1, '{"type": "Feature","geometry": {"type": "Point","coordinates": [125.6, 10.1]},"properties": {"name": "Dinagat Islands"}}');
Query OK, 1 row affected (0.01 sec)

```
4. select data
```
mysql> select * from city_geo_table;
+----+-------------------------------------------+
| id | position                                  |
+----+-------------------------------------------+
|  1 | {"type": "Feature","geometry": {"type": "Point","coordinates": [125.6, 10.1]},"properties": {"name": "Dinagat Islands"}} |
+----+-------------------------------------------+
1 row in set (0.00 sec)
```


### geographic analysis functions based on GeoJSON  (outdated)

1. GeoJSON_TYPE
```
mysql> select id,GeoJSON_TYPE(position) as 'geotype' from city_geo_table;
+----+-------------------------------------------+
| id | geotype                                  |
+----+-------------------------------------------+
|  1 | "Point"                                    |
+----+-------------------------------------------+
1 row in set (0.00 sec)
```

2. GeoJSON_buffer
```
select id,GeoJSON_Buffer(position,10) from city_geo_table;
```
  
3. GeoJSON_union
union by geotype
```
select GeoJSON_TYPE(position) as 'geotype',GeoJSON_union(position) from city_geo_table
group by geotype
```
4. GeoJSON_intersect
```
select GeoJSON_TYPE(position) as 'geotype',GeoJSON_intersect(position) from city_geo_table
group by geotype
```
### Demo Show
To show on Hackathon online.

## Why GeoJSON? (outdated)
- JSON is already supported by TiDB! GeoJSON stands on shoulders of it!
- GeoJSON is string. It's much more common and easy to process. If you already have GeoData in your system, it's easy to migrate.
- JSON functions is also available for GeoJSON.

## Future Plan ,also Introduction of my new RFC
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

## Design
### What's JSON Schema?
JSON Schema is a vocabulary that allows you to annotate and validate JSON documents.
### Benefits
- Describes your existing data format(s).
- Provides clear human- and machine- readable documentation.
- Validates data which is useful for:
    - Automated testing.
    - Ensuring quality of client submitted data.


## Target
Add JSON Schema support for TiDB's JSON datatype.

So you can validate JSON data with it and ensuring  data quality in database level.

### Demo Show
To show on Hackathon online.