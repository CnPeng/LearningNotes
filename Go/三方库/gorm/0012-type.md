# 1. 1002-type标签的取值


## 1.1. Gorm 标签中 type 的取值有：

```
bigint
binary
bit
blob
bool
boolean
char
date
datetime
decimal
double
enum
float
geometry
geometrycollection
int
integer
json
linestring
longblob
longtext
mediumblob
mediumint
mediumtext
multilinestring
multipoint
multipolygon
numeric
nvarchar
point
polygon
real
set
smallint
string
text
time
timestamp
tinyblob
tinyint
tinytext
varbinary
varchar
```

这些类型对应着 MySQL 数据库支持的数据类型。

## 1.2. 指定数据类型为tinyint，并限定数据值

[官方链接：检查约束](https://gorm.io/zh_CN/docs/constraints.html)

可以使用 Gorm 标签中的 `type` 和 `check` 来指定数据类型为 `tinyint` 并限定数据值。

例如，以下示例将在数据库中创建一个名为 `status` 的 `tinyint` 列，并将其限制为只接受 0 或 1 的值：

```go
type User struct {
    ID     uint
    Name   string
    Status int8 `gorm:"type:tinyint;check:status in (0,1)"`
}
```