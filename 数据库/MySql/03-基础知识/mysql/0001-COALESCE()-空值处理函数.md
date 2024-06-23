[原文《sql：函数：COALESCE()》](https://blog.csdn.net/weixin_38750084/article/details/83034294)

## 1.1 COALESCE()函数

> coalesce   [,kəuə'les]  vi. 合并；结合；联合 vt. 使…联合；使…合并

主流数据库系统都支持 `COALESCE()` 函数，这个函数主要用来进行空值处理，其参数格式如下：

```sql 
COALESCE ( expression,value1,value2……,valuen) 
```

* `COALESCE()` 函数的第一个参数 `expression` 为待检测的表达式，而其后的参数个数不定。
* `COALESCE()` 函数将会返回包括 `expression` 在内的所有参数中的第一个非空表达式。
* 如果 `expression` 不为空值则返回 `expression` ；否则判断 `value1`是否是空值，
	* 如果 `value1` 不为空值则返回 `value1` ；否则判断 `value2` 是否是空值，
	* 如果 `value2` 不为空值则返回 `value2`；……以此类推，
	* 如果所有的表达式都为空值，则返回 `NULL`。 


## 1.2 示例

我们将使用 `COALESCE()` 函数完成下面的功能，返回人员的“重要日期”：

如果出生日期不为空则将出生日期做为“重要日期”，如果出生日期为空则判断注册日期是否为空，如果注册日期不为空则将注册日期做为“重要日期”，如果注册日期也为空则将“2008年8月8日”做为“重要日期”。实现此功能的SQL语句如下：
 
* MYSQL、MSSQLServer、DB2: 
 
```sql
SELECT FName,FBirthDay,FRegDay, 
COALESCE(FBirthDay,FRegDay,'2008-08-08')  AS ImportDay  
FROM T_Person 
```

* Oracle: 
 
```sql
SELECT FBirthDay,FRegDay,  
COALESCE(FBirthDay,FRegDay,TO_DATE('2008-08-08', 'YYYY-MM-DD HH24:MI:SS'))  
AS ImportDay  
FROM T_Person 
```

  执行完毕我们就能在输出结果中看到下面的执行结果： 

```sql
FName  FBirthDay  FRegDay  ImportDay 
Tom  1981-03-22 00:00:00  1998-05-01 00:00:00  1981-03-22 00:00:00 
Jim  1987-01-18 00:00:00  1999-08-21 00:00:00  1987-01-18 00:00:00 
Lily  1987-11-08 00:00:00  2001-09-18 00:00:00  1987-11-08 00:00:00 
Kelly  1982-07-12 00:00:00  2000-03-01 00:00:00  1982-07-12 00:00:00 
Sam  1983-02-16 00:00:00  1998-05-01 00:00:00  1983-02-16 00:00:00 
Kerry  <NULL>  1999-03-01 00:00:00  1999-03-01 00:00:00 
Smith  <NULL>  <NULL>  2008-08-08 
BillGates  1972-07-18 00:00:00  1995-06-19 00:00:00  1972-07-18 00:00:00 
```

这里边最关键的就是 Kerry 和 Smith 这两行，可以看到这里的计算逻辑是完全符合我们的需求的。

## 1.3 COALESCE() 的简化版

`COALESCE()` 函数可以用来完成几乎所有的空值处理，不过在很多数据库系统中都提供了它的简化版，这些简化版中只接受两个变量，其参数格式如下： 

数据库|简化函数
---|---
MYSQL | `IFNULL(expression,value)`
MSSQLServer | `ISNULL(expression,value)` 
Oracle |`NVL(expression,value)` 

这几个函数的功能和 `COALESCE(expression,value)`是等价的。

比如 SQL 语句用于返回人员的“重要日期”，如果出生日期不为空则将出生日期做为“重要日期”，如果出生日期为空则返回注册日期的值： 


* MYSQL: 

```sql
SELECT FBirthDay,FRegDay,  
IFNULL(FBirthDay,FRegDay)  AS ImportDay  
FROM T_Person 
```

* MSSQLServer: 

```sql
SELECT FBirthDay,FRegDay,  
ISNULL(FBirthDay,FRegDay)  AS ImportDay  
FROM T_Person 
```

* Oracle: 

```sql
SELECT FBirthDay,FRegDay,  
NVL(FBirthDay,FRegDay)  AS ImportDay  
FROM T_Person 
``` 

原文参考：[https://blog.csdn.net/u010031649/article/details/38781757#commentsedit](https://blog.csdn.net/u010031649/article/details/38781757#commentsedit)