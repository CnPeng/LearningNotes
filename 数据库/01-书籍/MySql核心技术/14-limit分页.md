[基于 B 站 《好程序员大数据_Mysql核心技术》-13 整理](https://www.bilibili.com/video/BV1ut4y1y7tt?p=13)

# 14.limit 分页

当一页的数据过大时，我们可以进行分页显示操作。需要注意的是，**分页查询时，一般都要先排序，然后再分页。**

语法：`limit m [, n];` , 其中：

* m 表示从第几条记录开始查询
* n 表示要查询的记录数量。
* mysql 查询出来的记录的 index 从 0 开始。
* `limit n` 表示从 0 索引开始查询 n 条记录。

示例：

```sql
# 查询工资最高的三个人
SELECT * FROM emp ORDER BY sal DESC LIMIT 3;
 
# 分页查询
# 从索引 0 开始，查询 5 条数据。
SELECT * FROM emp LIMIT 0,5;
# 从索引 5 开始，查询 5 条数据。SELECT * FROM emp LIMIT 5,5;
```

