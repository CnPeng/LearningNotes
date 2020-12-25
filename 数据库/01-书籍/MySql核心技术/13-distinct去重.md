[基于 B 站 《好程序员大数据_Mysql核心技术》-13 整理](https://www.bilibili.com/video/BV1ut4y1y7tt?p=13)

# 13. distinct 去重

distinct 用于过滤掉重复数据（即查询表中有哪些不同数据）。只能放在 select 关键字之后，如 :

```sql
# 查询全部部门编号（即列出所有人的所有部门编号，会有重复数据）
SELECT deptno FROM emp;

 # 查询有哪些部门编号（仅查询部门编号，会去除重复数据）
SELECT DISTINCT deptno FROM emp;
# 这种方式也能实现效果，但是略繁琐
SELECT deptno FROM emp GROUP BY deptno;
```

distinct 后面可以跟多个需要去重的条件，如：

```sql
# 这样就是一个组合条件，deptno 和 sal 只要有一项不重复的就符合条件
SELECT DISTINCT deptno, sal FROM emp;
```