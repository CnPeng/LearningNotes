# 1. 3-Gin搭建BlogAPI's-2

> 2022-01-19-23:35 

[点击查看原文](https://eddycjy.com/posts/go/gin/2018-02-12-api-02/)

## 1.1. 涉及知识点

* [Gin](https://github.com/gin-gonic/gin)：Golang 的一个微框架，性能极佳。
* [beego-validation](https://github.com/astaxie/beego/tree/master/validation)：本节采用的 beego 的表单验证库，[中文文档](https://beego.me/docs/mvc/controller/validation.md)。
* [gorm](https://github.com/jinzhu/gorm)，对开发人员友好的 ORM 框架，[英文文档](http://gorm.io/docs/)
* [com](https://github.com/Unknwon/com)，一个小而美的工具包。

## 1.2. 本文目标

完成博客的标签类接口定义和编写

## 1.3. 定义接口

本节正是编写标签的逻辑，我们想一想，一般接口为增删改查是基础的，那么我们定义一下接口吧！

* 获取标签列表：`GET("/tags”)`
* 新建标签：`POST("/tags”)`
* 更新指定标签：`PUT("/tags/:id”)`
* 删除指定标签：`DELETE("/tags/:id”)`

## 1.4. 编写路由空壳

开始编写路由文件逻辑，在 `routers` 下新建 `api` 目录，我们当前是第一个 API 大版本，因此在 `api` 下新建 `v1` 目录，再新建 `tag.go` 文件，写入内容：

```go
package v1

import (
    "github.com/gin-gonic/gin"
)

//获取多个文章标签
func GetTags(c *gin.Context) {
}

//新增文章标签
func AddTag(c *gin.Context) {
}

//修改文章标签
func EditTag(c *gin.Context) {
}

//删除文章标签
func DeleteTag(c *gin.Context) {
}
```

## 1.5. 注册路由

### 1.5.1. 注册路由

我们打开 `routers` 下的 `router.go` 文件，修改文件内容为：

```go
package routers

import (
    "github.com/gin-gonic/gin"

    "gin-blog/routers/api/v1"
    "gin-blog/pkg/setting"
)

func InitRouter() *gin.Engine {
    gin.SetMode(setting.RunMode)

    r := gin.New()

    r.Use(gin.Logger())

    r.Use(gin.Recovery())

    apiv1 := r.Group("/api/v1")
    {
        //获取标签列表
        apiv1.GET("/tags", v1.GetTags)
        //新建标签
        apiv1.POST("/tags", v1.AddTag)
        //更新指定标签
        apiv1.PUT("/tags/:id", v1.EditTag)
        //删除指定标签
        apiv1.DELETE("/tags/:id", v1.DeleteTag)
    }

    return r
}
```

当前目录结构：

```cmd
gin-blog/
├── conf
│   └── app.ini
├── main.go
├── middleware
├── models
│   └── models.go
├── pkg
│   ├── e
│   │   ├── code.go
│   │   └── msg.go
│   ├── setting
│   │   └── setting.go
│   └── util
│       └── pagination.go
├── routers
│   ├── api
│   │   └── v1
│   │       └── tag.go
│   └── router.go
├── runtime
```

### 1.5.2. 检验路由是否注册成功

回到命令行，执行 `go run main.go`，检查路由规则是否注册成功。

```cmd
$ go run main.go
[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /api/v1/tags              --> gin-blog/routers/api/v1.GetTags (3 handlers)
[GIN-debug] POST   /api/v1/tags              --> gin-blog/routers/api/v1.AddTag (3 handlers)
[GIN-debug] PUT    /api/v1/tags/:id          --> gin-blog/routers/api/v1.EditTag (3 handlers)
[GIN-debug] DELETE /api/v1/tags/:id          --> gin-blog/routers/api/v1.DeleteTag (3 handlers)
```

运行成功，那么我们愉快的开始编写我们的接口吧！

## 1.6. 添加 validation 依赖

首先我们要拉取 `validation` 的依赖包，在后面的接口里会使用到表单验证

```cmd
$ go get -u github.com/astaxie/beego/validation
```

## 1.7. 编写标签列表逻辑

### 1.7.1. 编写 models 逻辑

创建 `models` 目录下的 `tag.go` ，写入文件内容：

```go
package models

type Tag struct {
    Model

    Name string `json:"name"`
    CreatedBy string `json:"created_by"`
    ModifiedBy string `json:"modified_by"`
    State int `json:"state"`
}

func GetTags(pageNum int, pageSize int, maps interface {}) (tags []Tag) {
    db.Where(maps).Offset(pageNum).Limit(pageSize).Find(&tags)

    return
}

func GetTagTotal(maps interface {}) (count int){
    db.Model(&Tag{}).Where(maps).Count(&count)

    return
}
```

我们创建了一个 `Tag struct{}` ，用于 Gorm 的使用。并给予了附属属性 `json` ，这样子在 `c.JSON` 的时候就会自动转换格式，非常的便利

可能会有的初学者看到 return，而后面没有跟着变量，会不理解；其实你可以看到在函数末端，我们已经显示声明了返回值，这个变量在函数体内也可以直接使用，因为他在一开始就被声明了

有人会疑惑 `db` 是哪里来的：**因为在同个 `models` 包下，因此 `db *gorm.DB` 是可以直接使用的**

### 1.7.2. 编写路由逻辑

打开 `routers` 目录下 `v1` 版本的 `tag.go`，我们先编写获取标签列表的接口

修改文件内容：

```go
package v1

import (
    "net/http"

    "github.com/gin-gonic/gin"
    //"github.com/astaxie/beego/validation"
    "github.com/Unknwon/com"

    "gin-blog/pkg/e"
    "gin-blog/models"
    "gin-blog/pkg/util"
    "gin-blog/pkg/setting"
)

//获取多个文章标签
func GetTags(c *gin.Context) {
    name := c.Query("name")

    maps := make(map[string]interface{})
    data := make(map[string]interface{})

    if name != "" {
        maps["name"] = name
    }

    var state int = -1
    if arg := c.Query("state"); arg != "" {
        state = com.StrTo(arg).MustInt()
        maps["state"] = state
    }

    code := e.SUCCESS

    data["lists"] = models.GetTags(util.GetPage(c), setting.PageSize, maps)
    data["total"] = models.GetTagTotal(maps)

    c.JSON(http.StatusOK, gin.H{
        "code" : code,
        "msg" : e.GetMsg(code),
        "data" : data,
    })
}

//新增文章标签
func AddTag(c *gin.Context) {
}

//修改文章标签
func EditTag(c *gin.Context) {
}

//删除文章标签
func DeleteTag(c *gin.Context) {
}
```

方法/变量  | 说明
---|---
`c.Query` | 可用于获取 `?name=test&state=1` 这类 URL 参数，而 **`c.DefaultQuery` 则支持设置一个默认值**
`code` 变量 | 使用了`e` 模块的错误编码，这正是先前规划好的错误码，方便排错和识别记录
`util.GetPage` | 保证了各接口的 page 处理是一致的
`c *gin.Context` | 是 Gin 很重要的组成部分，可以理解为上下文，它允许我们在中间件之间传递变量、管理流、验证请求的 JSON 和呈现 JSON 响应

在本机执行 `curl 127.0.0.1:8000/api/v1/tags`，正确的返回值为 `{"code":200,"data":{"lists":[],"total":0},"msg":"ok"}`，若存在问题请结合 gin 结果进行排错。

在获取标签列表接口中，我们可以根据 name、state、page 来筛选查询条件，分页的步长可通过 `app.ini` 进行配置，以 lists、total 的组合返回达到分页效果。

## 1.8. 编写新增标签的逻辑

### 1.8.1. 编写 models 逻辑

接下来我们编写新增标签的接口

打开 `models` 目录下的 `tag.go` ，修改文件（增加 2 个方法）：

```go
...
func ExistTagByName(name string) bool {
    var tag Tag
    db.Select("id").Where("name = ?", name).First(&tag)
    if tag.ID > 0 {
        return true
    }

    return false
}

func AddTag(name string, state int, createdBy string) bool{
    db.Create(&Tag {
        Name : name,
        State : state,
        CreatedBy : createdBy,
    })

    return true
}
...
```

### 1.8.2. 编写路由逻辑

打开 `routers` 目录下的 `tag.go`，修改文件（变动 `AddTag` 方法）：

```go
package v1

import (
    "log"
    "net/http"

    "github.com/gin-gonic/gin"
    "github.com/astaxie/beego/validation"
    "github.com/Unknwon/com"

    "gin-blog/pkg/e"
    "gin-blog/models"
    "gin-blog/pkg/util"
    "gin-blog/pkg/setting"
)

...

//新增文章标签
func AddTag(c *gin.Context) {
    name := c.Query("name")
    state := com.StrTo(c.DefaultQuery("state", "0")).MustInt()
    createdBy := c.Query("created_by")

    valid := validation.Validation{}
    valid.Required(name, "name").Message("名称不能为空")
    valid.MaxSize(name, 100, "name").Message("名称最长为100字符")
    valid.Required(createdBy, "created_by").Message("创建人不能为空")
    valid.MaxSize(createdBy, 100, "created_by").Message("创建人最长为100字符")
    valid.Range(state, 0, 1, "state").Message("状态只允许0或1")

    code := e.INVALID_PARAMS
    if ! valid.HasErrors() {
        if ! models.ExistTagByName(name) {
            code = e.SUCCESS
            models.AddTag(name, state, createdBy)
        } else {
            code = e.ERROR_EXIST_TAG
        }
    }

    c.JSON(http.StatusOK, gin.H{
        "code" : code,
        "msg" : e.GetMsg(code),
        "data" : make(map[string]string),
    })
}
...
```

用 Postman 用 POST 访问 `http://127.0.0.1:8000/api/v1/tags?name=1&state=1&created_by=test`，查看 code 是否返回 200 `及blog_tag` 表中是否有值，有值则正确。

## 1.9. 编写 models callbacks

### 1.9.1. 编写 callbacks

但是这个时候大家会发现，我明明新增了标签，但 `created_on` 居然没有值，那么修改标签的时候 `modified_on` 会不会也存在这个问题？

为了解决这个问题，我们需要打开 `models` 目录下的 `tag.go` 文件，修改文件内容（修改包引用和增加 2 个方法）：

```go
package models

import (
    "time"

    "github.com/jinzhu/gorm"
)

...

func (tag *Tag) BeforeCreate(scope *gorm.Scope) error {
    scope.SetColumn("CreatedOn", time.Now().Unix())

    return nil
}

func (tag *Tag) BeforeUpdate(scope *gorm.Scope) error {
    scope.SetColumn("ModifiedOn", time.Now().Unix())

    return nil
}
```

重启服务，再用 Postman 用 POST 访问 `http://127.0.0.1:8000/api/v1/tags?name=2&state=1&created_by=test`，发现 `created_on` 已经有值了！

### 1.9.2. 本节知识点

在前述几段代码中，涉及到知识点：

这属于 `gorm` 的 `Callbacks` ，可以将回调方法定义为模型结构的指针，**在创建、更新、查询、删除时将被调用**，如果任何回调返回错误， `gorm` 将停止未来操作并回滚所有更改。

gorm 所支持的回调方法：

类别|方法
---|---
创建 | BeforeSave、AfterSave、BeforeCreate、AfterCreate
更新 | BeforeSave、AfterSave、BeforeUpdate、AfterUpdate
删除 | BeforeDelete、AfterDelete
查询 | AfterFind

## 1.10. 编写其余的逻辑

### 1.10.1. 编写路由逻辑

接下来，我们一口气把剩余的两个接口（EditTag、DeleteTag）完成吧

打开 `routers` 目录下 `v1` 版本的 `tag.go` 文件，修改内容：

```go
...

//修改文章标签
func EditTag(c *gin.Context) {
    id := com.StrTo(c.Param("id")).MustInt()
    name := c.Query("name")
    modifiedBy := c.Query("modified_by")

    valid := validation.Validation{}
    var state int = -1
    if arg := c.Query("state"); arg != "" {
        state = com.StrTo(arg).MustInt()
        valid.Range(state, 0, 1, "state").Message("状态只允许0或1")
    }
    valid.Required(id, "id").Message("ID不能为空")
    valid.Required(modifiedBy, "modified_by").Message("修改人不能为空")
    valid.MaxSize(modifiedBy, 100, "modified_by").Message("修改人最长为100字符")
    valid.MaxSize(name, 100, "name").Message("名称最长为100字符")

    code := e.INVALID_PARAMS
    if ! valid.HasErrors() {
        code = e.SUCCESS
        if models.ExistTagByID(id) {
            data := make(map[string]interface{})
            data["modified_by"] = modifiedBy
            if name != "" {
                data["name"] = name
            }
            if state != -1 {
                data["state"] = state
            }

            models.EditTag(id, data)
        } else {
            code = e.ERROR_NOT_EXIST_TAG
        }
    }

    c.JSON(http.StatusOK, gin.H{
        "code" : code,
        "msg" : e.GetMsg(code),
        "data" : make(map[string]string),
    })
}

//删除文章标签
func DeleteTag(c *gin.Context) {
    id := com.StrTo(c.Param("id")).MustInt()

    valid := validation.Validation{}
    valid.Min(id, 1, "id").Message("ID必须大于0")

    code := e.INVALID_PARAMS
    if ! valid.HasErrors() {
        code = e.SUCCESS
        if models.ExistTagByID(id) {
            models.DeleteTag(id)
        } else {
            code = e.ERROR_NOT_EXIST_TAG
        }
    }

    c.JSON(http.StatusOK, gin.H{
        "code" : code,
        "msg" : e.GetMsg(code),
        "data" : make(map[string]string),
    })
}
```

### 1.10.2. 编写其余的 models 逻辑

打开 `models` 下的 `tag.go` ，修改文件内容：

```go
...

func ExistTagByID(id int) bool {
    var tag Tag
    db.Select("id").Where("id = ?", id).First(&tag)
    if tag.ID > 0 {
        return true
    }

    return false
}

func DeleteTag(id int) bool {
    db.Where("id = ?", id).Delete(&Tag{})

    return true
}

func EditTag(id int, data interface {}) bool {
    db.Model(&Tag{}).Where("id = ?", id).Updates(data)

    return true
}
...
```

## 1.11. 验证功能

重启服务，用 Postman 不同请求方式分别进行访问：

* PUT 方式访问 `http://127.0.0.1:8000/api/v1/tags/1?name=edit1&state=0&modified_by=edit1` ，查看 code 是否返回 200
* DELETE 方式访问 `http://127.0.0.1:8000/api/v1/tags/1` ，查看 code 是否返回 200

至此，Tag 的 API’s 完成，下一节我们将开始 Article 的 API’s 编写！

## 1.12. 参考

[本系列示例代码 ：go-gin-example](https://github.com/EDDYCJY/go-gin-example)

## 1.13. CnPeng 补充

以下内容基于原文的评论内容补充。

* 如果想在服务运行时自动将 models 中的 struct 映射到数据库中，需要在 `models.go` 文件中增加 `db.AutoMigrate(&Tag{})`
* 可以将 `BeforeCreate` 和 `BeforeUpdate` 等回调函数定义在 `Model.go` 中，而不是定义在某个 struct 中，这样可以减少后续的模板代码 
* 在测试上面的修改和删除等路由时，如果不使用 PostMan，也可以直接使用 `curl` 命令（参数较多的情况下还是建议优先选择 PostMan ），示例如下：

```cmd
#获取标签列表
curl 127.0.0.1:8000/api/v1/tags

#新建标签
curl -X POST --data "" http://127.0.0.1:8000/api/v1/tags\?name\=2\&state\=1\&created_by\=test

#更新指定标签
curl -X PUT --data "" http://127.0.0.1:8000/api/v1/tags/1\?name\=edit1\&state\=0\&modified_by\=edit1

#删除指定标签
curl -X DELETE http://127.0.0.1:8000/api/v1/tags/1
```

* 注册路由时，需要将 `gin.SetMode(setting.RunMode)` 放到 `r:=gin.New()` 之前，否则即便修改了运行模式也还是会出现 `Running in "debug" mode. `的提示：

```go
func InitRouter() *gin.Engine {
    gin.SetMode(setting.RunMode)

    r := gin.New()

    ...
}
```