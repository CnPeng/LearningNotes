# 1. 0014-convert函数

在 MySQ L中，`CONVERT()`函数有两个主要用途：

## 1.1. **转换字符集（Character Set Conversion）**

`CONVERT()`函数可以用于将一个字符串从一个字符集转换为另一个字符集。语法如下：

```sql
   CONVERT(str, USING transcoding_name)
```

   其中：

- `str` 是需要转换的字符串。
- `USING transcoding_name` 指定要转换的目标字符集。

   示例：

```sql
   SELECT CONVERT(_latin1'Müller' USING utf8);
```

   这个例子将 `_latin1` 编码的字符串 `Müller` 转换为 utf8 编码。

## 1.2. **转换数据类型（Data Type Conversion）**

`CONVERT()` 函数也可以用于将一个表达式的值转换为指定的数据类型。语法如下：

```sql
   CONVERT(expr, type)
```

   其中：

- `expr` 是要转换的表达式或字段。
- `type` 是要转换成的目标数据类型，可以是：
    - BINARY 或 BINARY(N)
    - CHAR 或 CHAR(N)
    - DATE
    - DATETIME
    - DECIMAL
    - SIGNED 或 SIGNED INTEGER
    - TIME
    - UNSIGNED 或 UNSIGNED INTEGER
    - 以及其他 MySQL 支持的数据类型

   示例：

```sql
   SELECT CONVERT('2021-08-29', DATE);
```

这个例子将字符串 `2021-08-29` 转换为 `DATE` 类型。

总结来说，`CONVERT()`函数在 MySQL 中扮演着双重角色，既可以进行字符集间的转换，也可以进行不同类型数据间的转换，使得在处理不同字符集之间兼容性问题以及不同数据类型间的数据转换时更为方便。

在实际应用中，合理使用`CONVERT()`函数有助于**避免因字符集不匹配或数据类型不一致导致的问题**。同时要注意，数据类型转换时要遵循 MySQL 的类型转换规则，否则可能会引发错误或丢失精度。

