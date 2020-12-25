[基于 B 站 《好程序员大数据_Mysql核心技术》-12 整理](https://www.bilibili.com/video/BV1ut4y1y7tt?p=12)

# 12 group by 和 having

## 12.1 group by 

某些时候，需要对数据进行分组统计其最大值、最小值、平均值、和、总数等信息，而分组查询都要和聚合函数配合使用。

**在分组查询时，只有分组字段可以写在 select 子句中，其他的非分组字段不应该写在 select 子句中。**

示例:

```sql
# 查询每个部门的部门编号和每个部门的工资和
select deptno, sum(sal) from emp group by deptno;

# 查询每个部门中的每种职位的最高工资、最低工资、工资之和
SELECT deptno,job, MAX(sal),MIN(sal),SUM(sal) FROM emp GROUP BY deptno,job;

# 查询每个部门的部门编号以及部门中的总人数
SELECT deptno ,COUNT(*) FROM emp GROUP BY deptno;

# 查询每个部门的部门编号以及每个部门中工资大于 1500 的人数
SELECT deptno ,COUNT(*) FROM emp  WHERE  sal>1500 GROUP BY deptno ;
```

## 12.2 having

**having 子句** ，只能使用在分组查询子句后面，起到二次过滤的作用。

having 和 where 的区别：

* having 后面可以写聚合函数，where 后面不可以写聚合函数。
* having 是对分组后的数据进行二次过滤，where 则是对分组前的数据做过滤。
	* where 对分组前的数据做过滤，如果么有满足条件的数据，将不再执行分组操作
	* having 则是对分组后的数据进行过滤，即先分组，后过滤。 

示例:

```sql

# 查询部门平均工资大于 1000 的部门编号，以及平均工资
SELECT deptno, AVG(IFNULL(sal,0)) FROM emp GROUP BY deptno HAVING AVG(IFNULL(sal,0))>1000 ;

# 查询每种职位中最高工资大于 1500 的职位、以及该职位的最高工资、平均工资、平均奖金
SELECT job, MAX(sal), AVG(IFNULL(sal,0)), AVG(IFNULL(comm,0)) FROM emp GROUP BY job HAVING MAX(sal)>1500;

```
