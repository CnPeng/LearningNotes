# 1. mongodb更新内容

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