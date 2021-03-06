# postgresSQL索引（三）联合索引--多列索引及多列复合索引和多列单独索引的选择
###多列索引
一个索引可以建立在联合的多列上。例如,你有如下的一个表：
```sql
CREATE TABLE test2 (
	major INT,
	minor INT,
	name VARCHAR
);
```
如果你经常会发生如下的查询：
```sql
SELECT name FROM test2 WHERE major = constant AND minor = constant;
```
那么你建立如下的索引是比较恰当的：
```sql
CREATE INDEX test2_mm_idx ON (major,minor);
```
目前，只有 B-tree, GiST, GIN, 和 BRIN索引类型支持多列索引，默认最多支持32列。  

 **B-tree**类型的多列索引可以用于索引里声明的列的各种组合。但是**包含主列**（声明时最左端的那一列）的查询组合查询效率更高。即各种组合用于查询时，如果组合中包含最声明时最左端的那个列，那么这个查询组合比没有包含主列效率更高。还有一条规则时当主列相等，紧接着的查询条件时任何一列是不相等，第三列任何操作，会导致所有查询表时**索引无效**。例如我们建立的索引为 ON (a,b,c) ,我们的查询条件是：a = 5 AND b >= 42 AND c < 77，如果数据库引擎使用索引查询，索引扫描规则是a=5的行的所有索引值都会被 a = 5 and b >= 42 的查询条件扫描，c<77 的条件会被忽略，所以还要执行一次从结果里的顺序查询，效率很低。这种情况下我们应建立的索引应该是ON (b,c),即在b和c上建立索引，不包括a。如果还是设置3列索引的话，在查询规划器会帮我们优先选择全表索引。另外如果主列用的不是“=”操作，三列索引还是有效的。

 **GiST**可以用于索引里声明的列的各种组合，有主列副列区分，GiST索引在主列只有很少的值的时候效率会比较低。  

 **GIN**索引可以用于索引里声明的列的各种组合，但没有主列副列之分。  

 **BRIN**可以用于索引里声明的列的各种组合，也没有主列副列之分。  

 ***多列索引应该谨慎使用，多列索引的开销比单列索引大，而且索引列一旦超过3列以上查询条件不慎，会引起索引作用不起作用。***

###多列复合索引和多列单独索引的选择（多列复合索引和多列单独索引的区别）
 假设我们有2列，我们的索引组合有3种
-   1、ON (a)
-  2、ON (b)
- 3、ON (a,b)
我们表上索引就是7种选择。
- 1、不加索引。执行全表扫描。在数据量比较大的时候效率很低。
- 2、只加ON (a)。查询条件不包含a时全表扫描；包含a时，先查找符合条件a的行，再在结果上过滤其他条件。
- 3、只加ON (b)。类似a。
- 4、只加ON (a,b)。查询条件为a and b时效率最高；查询条件仅为a时次之；查询条件为b时，较差，但比全盘扫描效果好。
- 5、加ON (a)，ON (b)。查询会根据查询计划有所不同，但基本有2种情况（and条件）
  （1）分别在各自的列上按索引取值然后汇总，
  （2）按其中一个条件查询，另一个条件在结果里过滤。
- 6、加ON (a)，ON (a,b)。累赘了，建议不使用。
- 7、ON (b)，ON (a,b)。这种在查询频率大大超过插入频率的情况下使用，并且b，和ab的查询量大体相当。毕竟插入1条件数据要维护和修改2条索引，开销很大。
- 8、ON (a)，ON (b)，ON (a,b)。累赘了，建议不使用。


 ------------------分割线---------------------

 索引系列
* [postgreSQL 索引(一)索引介绍](http://icefocus.github.io/blog/postgres/index_1)
* [postgreSQL 索引（二）类型介绍](http://icefocus.github.io/blog/postgres/index_2)
* [postgresSQL索引（三）联合索引--多列索引及多列复合索引和多列单独索引的选择](http://icefocus.github.io/blog/postgres/index_3)
* [postgresSQL索引(四)--索引和order by](http://icefocus.github.io/blog/postgres/index_4)

 ------------------分割线---------------------
 
如果觉得写的好，请关注我们的公众号

![qrcode_for_gh_f9cc78dcbc68_258.jpg](https://upload-images.jianshu.io/upload_images/8155989-d7cfe697173859ed.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)