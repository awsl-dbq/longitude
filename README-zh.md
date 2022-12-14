# longitude
Longitude Project for TiDB Hackathon 2022 !

## 战队名
小宝突击队
## 战队宣言
人和代码,有一个能跑就行!
## 项目名
longitude

## 声明
很抱歉没有注意到今年规则有所改动.
今年不允许提前编码,但我国庆的时候已经写了一些代码.
为了比赛的公平公正,我还是很有必要更新一下RFC,这样在马拉松的时候大家一起开跑,才更开心一点.

但评委老师已经评过一次了,改动不宜过大,我计划以我之前RFC的下一步计划作为我新的RFC.这样也不会额外花费评委老师太多时间.

## 项目介绍 (抢跑作废)
翻翻老帖 https://github.com/pingcap/tidb/issues/6347 
https://github.com/pingcap/tidb/issues/673#issuecomment-251132927
时隔可能也就五六七八年吧,可怜的GISer至今也没等到一个Geo相关的支持. 

小宝突击队决定试试,为Tidb添加地理数据支持.
马拉松如下计划:
1. GeoJSON 数据类型
2. 基于GeoJSON相关的地理分析函数支持
3. 最后,做个简单Demo

突突突,开起我心爱的拖拉机,开run...

## 项目设计 (抢跑作废)
什么是GeoJSON?

JSON + Schema(GIS) => GeoJSON
在地理信息领域的标准JSON数据即GeoJSON.
### 支持GeoJSON 数据类型 (抢跑作废)
预期输出
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
3. insert data with validation
```
; not pass
mysql> INSERT INTO city_geo_table (id,position) VALUES (1, '{"name": "Beijing", "population": 100}');
ERROR 1105 (HY000): GeoJson is  not valid

; pass 

mysql> INSERT INTO city_geo_table (id,position) VALUES (121, '
    '> {
    '>   "type": "Feature",
    '>   "geometry": {
    '>     "type": "Point",
    '>     "coordinates": [125.6, 10.1]
    '>   },
    '>   "properties": {
    '>     "name": "Dinagat Islands"
    '>   }
    '> }
    '> ');
Query OK, 1 row affected (0.08 sec)
```
4. select data
```
mysql> select * from city_geo_table;
+----+-------------------------------------------+
| id | position                                  |
+----+-------------------------------------------+
|  121 | {"geometry": {"coordinates": [125.6, 10.1], "type": "Point"}, "properties": {"name": "Dinagat Islands"}, "type": "Feature"} |
+----+-------------------------------------------+
1 row in set (0.00 sec)
```

### 基于GeoJSON相关的地理分析函数支持 (抢跑作废)
1. GeoJSON_TYPE
```
mysql> select id,GeoJSON_TYPE(position) as 'geotype' from city_geo_table;
+----+-------------------------------------------+
| id | geotype                                  |
+----+-------------------------------------------+
|  121 | "Point"                                    |
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
在介绍时展示


## 设计思路 (抢跑作废)
1. 取舍之道
- JSON 本来TiDB就支持,基于JSON去构建GeoJSON会非常方便
- GeoJSON 说到底是字符串,更通用可维护.现有系统的地理相关数据很方便处理一下迁移到GeoJSON.
- 原有JSON相关函数可复用

## 未来计划, 新RFC的开始
JSON是Tidb目前基础支持的,GeoJSON其实是GIS特定领域的一个数据类型应用.
其实我们可以尝试在运行时通过语句调用,创建特定领域数据类型.
```
create domain GIS with schema 'gis.json.schema'; // geographic information system
=> 系统会支持 GISJSON 类型数据
create domain HIS with schema 'his.json.schema'; // hospital information system
=> 系统会支持 HISJSON 类型数据
create domain FIS with schema 'fis.json.schema'; // finacial information system
=> 系统会支持 FISJSON 类型数据
```

当然,光有数据类型是不够的,关键还要有领域相关函数.这部分功能当然要靠UDF来实现了.


## 设计
### 什么是JSON Schema?
JSON Schema 是一个允许你标注验证JSON文档的文档.
### 好处
- 描述现有数据格式.
- 提供清晰的人机都可读的文档.
- 验证数据,可用于:
    - 自动化测试.
    - 确保数据质量.


## 目标
为TiDB的JSON数据类型,添加 JSON Schema 支持.
这样可以在数据库层面来验证JSON数据.

### Demo 演示
在 Hackathon 线上演示