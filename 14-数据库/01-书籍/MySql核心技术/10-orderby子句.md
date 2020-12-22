[基于 B 站 《好程序员大数据_Mysql核心技术》-10 整理](https://www.bilibili.com/video/BV1ut4y1y7tt?p=10)

# 10. order by 子句

## 10.1 基本语法

`order by` 对于查询出来的数据按照规则进行排序, 通常放在一个查询语句的最后部分。

语法:

```sql
order by colName [asc | desc] [, colName [asc | desc]]
```

上述语句中：`asc` 表示升序，默认就是升序；`desc` 降序。

## 10.2 示例

先创建一个员工表，并添加数据：

```sql
CREATE TABLE emp(	empno INT,				# 员工号	ename VARCHAR(50),	# 姓名	job VARCHAR(50),		# 职位	mgr INT,					# 领导工号	hiredata DATE,			# 入职日期	sal DECIMAL(7,2),		# 薪资	comm DECIMAL(7,2),		# 奖金	deptno INT 				# 部门编号);INSERT INTO emp VALUES(7369,'SMITH','CLERK',7902,'1980-12-17',800,NULL,20);INSERT INTO emp VALUES(7499,'ALLEN','SALESMAN',7698,'1981-02-20',1600,300,30);INSERT INTO emp VALUES(7521,'WARD','SALESMAN',7698,'1981-02-22',1250,500,30);INSERT INTO emp VALUES(7566,'HONES','MANAGER',7839,'1981-04-02',2975,NULL,20);INSERT INTO emp VALUES(7654,'MARTIN','SALESMAN',7698,'1981-0928',1250,1400,30);INSERT INTO emp VALUES(7698,'BLAKE','MANAGER',7839,'1981-05-01',2850,NULL,30);INSERT INTO emp VALUES(7782,'CLERK','MANAGER',7839,'1981-06-09',2450,NULL,10);
INSERT INTO emp VALUES(7788,'ABC','CLERK',7698,'1988-09-02',2450,300,20);INSERT INTO emp VALUES(7839,'DEF','PRESIDENT',NULL,'1988-07-02',5000,NULL,10);
```

示例：

```sql
# 查询员工表中的所有信息，按照工资降序排列
SELECT * FROM emp ORDER BY sal DESC;

# 查询员工表中的所有员工信息，按照工资降序排雷，如果相同，再按照奖金升序排列。
SELECT * FROM emp ORDER BY sal DESC, comm ASC;

# 

```


