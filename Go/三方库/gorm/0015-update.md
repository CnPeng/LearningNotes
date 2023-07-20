# 0015-update

## gorm 在一个语句中不能同时调用多个 update 么？

在 gorm 中，一个语句中是**不能同时调用多个 `update` 的**。

因为 `update` 操作需要修改数据库中的数据，**如果同时调用多个 `update` 操作，会造成数据不一致的问题**。

如果需要同时更新多个字段，可以使用以下两种方式：


### 方式1

使用 `Model` 方法更新整个模型的数据，然后调用 `Save` 方法保存到数据库中，例如：

```go
var user User
db.Model(&user).Update("name", "new_name").Update("age", 20).Save(&user)
```

在这个例子中，我们使用了 `Model` 方法获取到 User 模型，然后调用 `Update` 方法分别更新了模型中的 name 和 age 字段，最后调用 `Save` 方法将更新后的数据保存到数据库中。


### 方式2

使用 `UpdateColumns` 方法更新指定的字段，例如：——CnPeng ——已测试可用

```go
db.Model(&user).UpdateColumns(map[string]interface{}{"name": "new_name", "age": 20})
```

在这个例子中，我们使用了 `UpdateColumns` 方法更新了指定的字段，只更新了 name 和 age 字段，其他字段不受影响。


需要注意的是，如果需要同时更新多个字段，**最好使用第二种方式**，因为**第一种方式会将整个模型的数据读取到内存中，然后再写回到数据库中，对性能有一定的影响**。而第二种方式只更新指定的字段，对性能的影响较小。