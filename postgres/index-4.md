# postgresSQL索引(四)--索引和order by
oder by是用于sql排序的语句。
order by查询中加入索引可以不用单独执行排序，而得到正确的排序，原因在于在执行查询是，索引按照特定的顺序查询，从而避免了单调的一次查询的开销。
在所有的索引类别中，postgresSQL只有B-tree索引支持能按序生成行，其他索引返回无序的结果，然后执行单独的排序。
但是如果查询表的很大部分数据，单独的排序很可能比运用索引快，因为结果无序的时候，是按照硬盘中存储的顺序查询，这样可以减少硬盘的访问，还记得第一篇的内容吗？索引只有在大数据获取少量数据的时候非常有效。一个重要情况是ORDER BY 组合 LIMIT n:单独索引只会操作识别的前n行数据，如果加上索引的话，前n行可以直接获取，而不扫描其余的内容。

思考一下：很多需要按时间排序的分页查询是不是符合这种场景。

建立索引可以指定索引的顺序
```sql
CREATE INDEX test_idx ON test (id DESC NULLS LAST);
```
在单列索引中定义索引的排序作用不是很明显，但是在多列索引中效果就不一样了。例如2列的组合索引ON (x,y)我们能满足ORDER BY x,y 或者ORDER BY x DESC, ORDER BY y DESC，但是如果我们经常查询的条件是 ORDER BY x ASC, y DESC,这就没办法利用索引的优势了，但是如果索引定义为 (x ASC, y DESC) 或者(x DESC, y ASC)那么效率就会大大提高了。

综上，特别指定索引顺序的索引，对特定的排序有非常大的作用。


 ------------------分割线---------------------

 索引系列
* [postgreSQL 索引(一)索引介绍](http://icefocus.github.io/blog/postgres/index_1)
* [postgreSQL 索引（二）类型介绍](http://icefocus.github.io/blog/postgres/index_2)
* [postgresSQL索引（三）联合索引--多列索引及多列复合索引和多列单独索引的选择](http://icefocus.github.io/blog/postgres/index_3)
* [postgresSQL索引(四)--索引和order by](http://icefocus.github.io/blog/postgres/index_4)

 ------------------分割线---------------------
 
如果觉得写的好，请关注我们的公众号

![qrcode_for_gh_f9cc78dcbc68_258.jpg](https://upload-images.jianshu.io/upload_images/8155989-d7cfe697173859ed.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)