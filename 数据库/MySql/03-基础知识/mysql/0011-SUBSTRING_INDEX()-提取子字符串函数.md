# 1. SUBSTRING_INDEX()-提取子字符串函数

## 1.1. 基本语法

在 MySQL 中， `SUBSTRING_INDEX` 函数用于**从字符串中提取子字符串，基于指定的分隔符和出现的次数**。如果指定的分隔符在字符串中不存在，将返回整个字符串.

函数的语法如下：

```sql
SUBSTRING_INDEX(str, delimiter, count)
```
参数说明：

参数  | 含义
---|---
`str` | 要操作的原始字符串。
`delimiter` | 用于分隔子字符串的分隔符。
`count`  | 要返回的子字符串的数量。<br>如果为正数，则返回左侧的子字符串；<br>如果为负数，则返回右侧的子字符串。


## 1.2. 基础示例

下面是一些示例，以帮助你更好地理解 `SUBSTRING_INDEX` 函数的使用：

* 示例1：获取字符串中的第一个子字符串

```sql
SELECT SUBSTRING_INDEX('www.example.com', '.', 1);
```
结果：'www'

* 示例2：获取字符串中的最后一个子字符串

```sql
SELECT SUBSTRING_INDEX('www.example.com', '.', -1);
```
结果：'com'

* 示例3：获取字符串中的前两个子字符串

```sql
SELECT SUBSTRING_INDEX('www.example.com', '.', 2);
```
结果：'http://www.example'

* 示例4：获取字符串中的最后两个子字符串

```sql
SELECT SUBSTRING_INDEX('www.example.com', '.', -2);
```
结果：'example.com'

请注意，如果指定的分隔符在字符串中不存在，SUBSTRING_INDEX函数将返回整个字符串。因此，在使用该函数时，请确保提供正确的分隔符和计数参数，以获得所需的结果。

## 1.3. 其他示例

假如数据库表中有一列为文件名称，如何统计文件名称中**共有多少个后缀名**以及**每种后缀名的数据有多少**。

假设表名为 files，包含一个名为 filename 的列，其中包含文件名称信息。

```sql
-- 统计后缀名数量
SELECT COUNT(DISTINCT SUBSTRING_INDEX(filename, '.', -1)) AS total_suffixes
FROM files;

-- 统计每种后缀名的数据量
SELECT SUBSTRING_INDEX(filename, '.', -1) AS suffix, COUNT(*) AS count
FROM files
GROUP BY suffix;
```

上述查询首先使用 `SUBSTRING_INDEX` 函数从文件名称中提取后缀部分，即使用点号（`.`）作为分隔符，并将 -1 作为参数传递给 `SUBSTRING_INDEX` 函数的第二个参数。这样可以从文件名的最后一个 `.`开始提取，直到字符串的末尾，从而得到后缀部分。

接下来，使用 `COUNT` 和 `DISTINCT` 关键字统计不同的后缀数量。

然后，使用 `GROUP BY` 语句按照后缀进行分组，并使用 `COUNT` 函数计算每种后缀名的数据量。

执行以上查询后，你将得到一个结果集，其中第一行显示总的后缀名数量，而随后的行将列出每个后缀名及其相应的数据量。

请注意，上述查询假设文件名称中只包含一个后缀名，并且使用点号（`.`）作为后缀名和文件名的分隔符。如果你的实际数据结构不同，可能需要相应调整查询以适应你的情况。

