# 1. mongodb更新内容

* [mongodb 官方文档-英文 ](https://www.mongodb.com/docs/manual/reference/method/js-collection/)
* [菜鸟窝mongodb中文文档](https://www.runoob.com/mongodb/mongodb-update.html)


## 1.1. 添加数据

```sql
db.getCollection("auth_user").insert( {
    _id: "00d04652a8a536eca080d58",
    tel: "15208110934",
    qq: "",
    weixin: "",
    weibo: "",
    "user_name": "",
    password: "31a83335655fcddc4daf63c283e518ed38fc3d87",
    status: NumberInt("1"),
    created: NumberLong("1662269993"),
    deleted: NumberLong("0"),
    "user_type": 2
} );
```


## 1.2. 更新内容

mongodb 中按条件更新内容，含更新单条和批量更新。

```sql
// 更新单条数据
//db.getCollection("auth_user").update( { _id: "23107a8c-c94e-4940-9f1f-62e4b731fa45" }, { $set: { status: NumberInt("1") } } )

// 批量更新数据，重点在末尾的 multi:true。
db.getCollection("auth_user").update( { created: "1656510200" }, { $set: { created: NumberInt("1656510200") } } ,{multi:true})

db.getCollection("auth_user").update( { deleted: "0" }, { $set: { deleted: NumberInt("0") } } ,{multi:true})

db.getCollection("auth_user").update( { status: "1" }, { $set: { status: NumberInt("1") } } ,{multi:true})
```

update 方法中：

* 第一个逗号前的为筛选条件（同 mysql 中的 where），
* 第二个逗号前的内容为更新动作，
* 第二个逗号后的内容表示是否要批量修改，如果不写则默认 false ——仅更新单条满足条件的数据。

## 1.3. 新增/删除列

参考：[https://blog.csdn.net/besmarterbestronger/article/details/101613591](https://blog.csdn.net/besmarterbestronger/article/details/101613591)

* 添加方式1

```sql
// 在 auth_user 表中新增一列 user_type, 并设置默认值为 2——仅第一条数据的默认值
db.auth_user.update({}, {$set:{user_type_b:2}}, false,true)

// 在 auth_user 表中新增一列 user_type, 并设置全部默认值为 2
db.auth_user.update({}, {$set:{user_type_c:2}}, {multi:true})
```

* 添加方式2

```sql
// 添加一列，列名为 user_type， 并指定其默认值为 2——实测仅第一条数据有默认值
db.getCollection('auth_user').update({}, {'$set': { "user_type": 2 }}, false,true)

// 添加一列，列名为 user_type, 并指定该列的所有值均为 2
db.getCollection('auth_user').update({}, {'$set': { "user_type_a": 2 }}, {'multi': true})
```

* 删除列

```sql
// 删除列名为 identify 的第一条数据值
db.getCollection('auth_user').update({}, {'$unset': { "identify": "" }}, false, true)
// 删除列名为 identify 的一整列
db.getCollection('auth_user').update({}, {'$unset': { "identify": "" }},  {'multi': true})
```

```sql
// 修改字段名：
db.getCollection('other').update({}, {'$rename':{'row':'days'}},{multi:true});
```