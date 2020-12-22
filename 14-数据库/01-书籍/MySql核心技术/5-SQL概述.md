[基于 B 站 《好程序员大数据_Mysql核心技术》-5 整理](https://www.bilibili.com/video/BV1ut4y1y7tt?p=5)

# 5.SQL 概述

## 5.1 SQL(结构化查询语言)

`SQL` 全称  Structured Query Language , 即 结构化查询语言。

SQL 是一种标准化的语言，允许我们在数据库上进行操作，如：创建项目、查询内容、更新内容和删除内容等操作。

SQL 最早时被美国国家标准局（ANSI）确定为关系型数据库语言的美国标准。后来被国际化标准组织（ISO）采纳为关系型数据库语言的国际标准。

各种数据库厂商都支持 ISO 标准的 SQL，类似于普通话。各个数据库厂商在标准的基础上，又定义了若干自己的扩展，这就类似于方言。

## 5.2 SQL 语言的分类

种类| 英文全称 | 含义 | 关键字
---|---|---|---
DDL | Data Definition Language | 数据定义语言，用来定义数据库对象（数据库、表、列） | alter、create、drop、truncate 等
DML | Data Manipulation Language| 数据操纵语言，用于修改表中记录 | insert、delete、update 
DQL | Data Query Language | 数据查询语言，用于查询表中记录 | select 
DCL | Data Control Language | 数据控制语言，用于定义访问权限和安全级别 | grant、revoke、alter、user 
TDL | Transaction Control Language | 事务控制语言，用于保证数据的商业逻辑和安全性 | commit、rollback、savepoint 