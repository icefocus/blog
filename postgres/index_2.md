## postgreSQL 索引（二）类型介绍
pg的索引有如下几类：B-tree, Hash, GiST, SP-GiST,GIN, BRIN.每个索引都使用不同的算法，每种算法都匹配不同的类型数据的查询。CREATE INDEX  命令默认创建适合大多数数据类型的B-tree 索引。

**B-tree 索引**可以应用于相等和值范围的查询，例如：
```sql
<,<=,=,>=,>,
between
in
is null
is not null
```
 也适用 
 ```sql
 like 'foo' 
~ '^foo'  --正则匹配
like 'foo%'
```
但不适用like ‘%foo’。

B-tree也支持排序。虽然他不总是比简单的扫描和排序快，但会非常有用。

**Hash索引**只能处理简单的相等比较， 但查询计划不管是否对已建立哈希索引的列使用等号，会将全部执行哈希索引。
哈希索引常见的的命令如下：
```sql
CREATE INDEX name ON table USING HASH (column);
```
**GiST索引**适用于二维地理位置数据等。
**SP-GiST** 适用于二维点坐标数据的索引等。
**GIN索引**适用于数组等。
**BRIN**(Block Range INdexes)适用于块中查找最大值最小值。

 ------------------分割线---------------------

 索引系列
* [postgreSQL 索引(一)索引介绍](http://icefocus.github.io/blog/postgres/index_1)
* [postgreSQL 索引（二）类型介绍](http://icefocus.github.io/blog/postgres/index_2)
* [postgresSQL索引（三）联合索引--多列索引及多列复合索引和多列单独索引的选择](http://icefocus.github.io/blog/postgres/index_3)
* [postgresSQL索引(四)--索引和order by](http://icefocus.github.io/blog/postgres/index_4)

 ------------------分割线---------------------
 
如果觉得写的好，请关注我们的公众号


![qrcode_for_gh_f9cc78dcbc68_258.jpg](https://upload-images.jianshu.io/upload_images/8155989-d7cfe697173859ed.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
