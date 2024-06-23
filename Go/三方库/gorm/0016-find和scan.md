# 1. 0016-find和scan

## 1.1. Scan 和 Find 比较

在 Gorm 中，`Scan` 和 `Find` 方法主要区别在于它们的使用场景和功能：

### 1.1.1. Scan 

`Scan` 方法主要用于将查询结果直接扫描到一个变量或结构体中。

通**常用于执行原生 SQL 查询**，并且期望**获取单个值或者一行数据**的情况。

示例：

```go
     var result interface{}
     db.Raw("SELECT COUNT(*) FROM users").Scan(&result)
```

在这个例子中，我们执行了一个计数查询，并将结果直接存储到一个接口类型的变量中。

### 1.1.2. Find

`Find` 方法是 Gorm 的主要查询方法之一，它用于根据指定的条件查找记录，并 **将结果映射到`结构体数组`（slice）** 中。

主要用于**获取多条记录**，它可以基于主键、条件或其他约束来查询数据库中的数据。

示例：

```go
     var users []User
     db.Where("status = ?", "active").Find(&users)
```
在这个例子中，我们找到了所有状态为“active”的用户，并将结果填充到了一个 User 结构体切片中。

### 1.1.3. 总结

总结来说，

* `Scan` 方法常用于执行简单的原生 SQL 查询并获取少量数据。（比如：仅根据条件查询 `record_id` , 并导出到字符串切片中。）
*  `Find` 方法则用于通过 Gorm 提供的 ORM 功能进行复杂的模型化查询，以获得多个对象实例。`Find` 还支持软删除（若已配置），即在查询时可以包含被软删除的数据，而不需要额外处理。

## 1.2. 使用场景分析


### 1.2.1. 场景需求

mysql 表中有一个字符串类型的 `record_id` 字段，我们期望将符合条件的 `record_id` 查询出来，并将查询结果赋值给一个字符串切片，在使用 gorm 查询时我们该如何实现？

在 Gorm 中，要查询符合条件的 `record_id` 并将其存储到字符串切片中，可以采用如下方式：

```go
import (
    "gorm.io/gorm"
)

type Record struct {
    RecordID string `gorm:"column:record_id"`
    // 其他字段...
}

func GetRecordIds(db *gorm.DB, condition interface{}) ([]string, error) {
    var recordIds []string

    result := db.Table("your_table_name").Select("record_id").Where(condition).Scan(&recordIds)
    if result.Error != nil {
        return nil, result.Error
    }

    return recordIds, nil
}

// 使用示例：
condition := "status = 'active'"
ids, err := GetRecordIds(db, condition)
if err != nil {
    // 处理错误
} else {
    fmt.Println(ids)
}
```

在上面的代码中：

- 定义了一个名为 `Record` 的结构体，其中包含 `RecordID` 字段。
- 创建了一个函数 `GetRecordIds`，它接受一个数据库连接（`*gorm.DB`）和一个条件（可以根据需要定义为 map、struct 或直接 SQL 条件字符串）。
- 使用 `.Table("your_table_name")` 指定表名。
- 使用 `.Select("record_id")` 只查询 `record_id` 字段。
- 使用 `.Where(condition)` 添加查询条件。
- 使用 `.Scan(&recordIds)` 将查询结果直接扫描到一个字符串切片中。

注意：`Scan` 方法会自动将查询结果中的 `record_id` 字段值放入到提供的字符串切片中。由于 Go 语言中的类型系统特性，Gorm 能够根据查询结果映射到对应类型的变量上。这里的关键是，`Scan` 方法能识别出我们期望接收的是一个字符串数组，并据此解析结果。

### 1.2.2. 实现分析

上述代码中，在将结果赋值给字符串切片时是否可以使用 `Find(&recordids)` 方法？如果可以的话，在这种场景下，`Scan` 和 `Find` 的效率差异有多大？

在 Gorm 中，如果直接将查询结果赋值给一个字符串切片，并且表中只有 `record_id` 字段需要获取时，通常不推荐使用 `Find` 方法。**因为 `Find` 方法主要用于映射到结构体实例的集合上，而这里我们只需要记录ID列表**。

但是，如果 `Record` 结构体仅包含 `RecordID` 字段，理论上也可以创建一个包含 `RecordID` 的空结构体数组，然后通过 `Find` 来实现：

```go
type Record struct {
    RecordID string `gorm:"column:record_id"`
}

func GetRecordIds(db *gorm.DB, condition interface{}) ([]string, error) {
    var records []Record

    result := db.Table("your_table_name").Where(condition).Find(&records)
    if result.Error != nil {
        return nil, result.Error
    }

    recordIds := make([]string, len(records))
    for i, record := range records {
        recordIds[i] = record.RecordID
    }

    return recordIds, nil
}
```

关于效率差异，`Scan` 和 `Find` 在这种场景下的主要区别在于：

- **Scan** 直接将查询结果解析为指定类型的变量（这里是字符串切片），避免了不必要的字段填充和结构体初始化，所以从内存分配和数据处理的角度看，可能会更高效一些。
  
- **Find** 会先将所有列的数据都填充到结构体数组中，然后再从结构体中提取出所需的字段放入字符串切片，这过程中涉及到额外的内存分配和结构体操作，因此在只关注单个字段的情况下，效率相对较低。

然而，在实际应用中，两者的性能差距可能并不显著，除非处理的数据量非常大。同时，Gorm 内部的具体实现细节也可能会影响两者之间的性能对比。对于小规模数据或者大部分情况，选择哪种方法更多地取决于代码的可读性和维护性。