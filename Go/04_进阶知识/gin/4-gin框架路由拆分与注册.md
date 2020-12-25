[原文：《gin框架路由拆分与注册》——李文周](https://www.liwenzhou.com/posts/Go/gin_routes_registry/)

---

本文总结了平时在项目中积累的关于 gin 框架路由拆分与注册的若干方法。

## 4.1 基本的路由注册

下面最基础的 `gin` 路由注册方式，适用于路由条目比较少的简单项目或者项目 demo。

```go
package main

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

func helloHandler(c *gin.Context) {
	c.JSON(http.StatusOK, gin.H{
		"message": "Hello q1mi!",
	})
}

func main() {
	r := gin.Default()
	r.GET("/hello", helloHandler)
	if err := r.Run(); err != nil {
		fmt.Println("startup service failed, err:%v\n", err)
	}
}
```

## 4.2 路由拆分成单独文件或包

当项目的规模增大后就不太适合继续在项目的 `main.go` 文件中去实现路由注册相关逻辑了，我们会倾向于把路由部分的代码都拆分出来，形成一个单独的文件或包。

我们在 `routers.go` 文件中定义并注册路由信息：

```go
package main

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

func helloHandler(c *gin.Context) {
	c.JSON(http.StatusOK, gin.H{
		"message": "Hello q1mi!",
	})
}

func setupRouter() *gin.Engine {
	r := gin.Default()
	r.GET("/hello", helloHandler)
	return r
}
```

此时 `main.go` 中调用上面定义好的 `setupRouter` 函数：

```go
func main() {
	r := setupRouter()
	if err := r.Run(); err != nil {
		fmt.Println("startup service failed, err:%v\n", err)
	}
}
```

此时的目录结构：

```
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers.go
```

把路由部分的代码单独拆分成包的话也是可以的，拆分后的目录结构如下：

```
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers
    └── routers.go
```
    
`routers/routers.go` 需要注意此时 `setupRouter` 需要改成首字母大写：

```go
package routers

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

func helloHandler(c *gin.Context) {
	c.JSON(http.StatusOK, gin.H{
		"message": "Hello q1mi!",
	})
}

// SetupRouter 配置路由信息
func SetupRouter() *gin.Engine {
	r := gin.Default()
	r.GET("/hello", helloHandler)
	return r
}
```

`main.go` 文件内容如下：

```go
package main

import (
	"fmt"
	"gin_demo/routers"
)

func main() {
	r := routers.SetupRouter()
	if err := r.Run(); err != nil {
		fmt.Println("startup service failed, err:%v\n", err)
	}
}
```

## 4.3 路由拆分成多个文件

当我们的业务规模继续膨胀，单独的一个 `routers` 文件或包已经满足不了我们的需求了，

```go
func SetupRouter() *gin.Engine {
	r := gin.Default()
	
	r.GET("/hello", helloHandler)
   	r.GET("/xx1", xxHandler1)
   	...
   	r.GET("/xx30", xxHandler30)
	return r
}
```

因为我们把所有的路由注册都写在一个 `SetupRouter` 函数中的话就会太复杂了。

我们可以分开定义多个路由文件，例如：

```
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers
    ├── blog.go
    └── shop.go
```

`routers/shop.go` 中添加一个 `LoadShop` 的函数，将 shop 相关的路由注册到指定的路由器：

```go
func LoadShop(e *gin.Engine)  {
	e.GET("/hello", helloHandler)
  	e.GET("/goods", goodsHandler)
  	e.GET("/checkout", checkoutHandler)
  	...
}
```

`routers/blog.go` 中添加一个 `LoadBlog` 的函数，将 blog 相关的路由注册到指定的路由器：

```go
func LoadBlog(e *gin.Engine) {
	e.GET("/post", postHandler)
  	e.GET("/comment", commentHandler)
  	...
}
```

在 `main` 函数中实现最终的注册逻辑如下：

```go
func main() {
	r := gin.Default()
	routers.LoadBlog(r)
	routers.LoadShop(r)
	if err := r.Run(); err != nil {
		fmt.Println("startup service failed, err:%v\n", err)
	}
}
```

## 4.4 路由拆分到不同的 APP

有时候项目规模实在太大，那么我们就更倾向于把业务拆分的更详细一些，例如把不同的业务代码拆分成不同的 APP。

因此我们在项目目录下单独定义一个 app 目录，用来存放我们不同业务线的代码文件，这样就很容易进行横向扩展。大致目录结构如下：

```
gin_demo
├── app
│   ├── blog
│   │   ├── handler.go
│   │   └── router.go
│   └── shop
│       ├── handler.go
│       └── router.go
├── go.mod
├── go.sum
├── main.go
└── routers
    └── routers.go
```

其中 `app/blog/router.go` 用来定义 post 相关路由信息，具体内容如下：

```go
func Routers(e *gin.Engine) {
	e.GET("/post", postHandler)
	e.GET("/comment", commentHandler)
}
```

`app/shop/router.go` 用来定义 shop 相关路由信息，具体内容如下：

```go
func Routers(e *gin.Engine) {
	e.GET("/goods", goodsHandler)
	e.GET("/checkout", checkoutHandler)
}
```

`routers/routers.go` 中根据需要定义 `Include` 函数用来注册子 app 中定义的路由，`Init` 函数用来进行路由的初始化操作：

```go
type Option func(*gin.Engine)

var options = []Option{}

// 注册app的路由配置
func Include(opts ...Option) {
	options = append(options, opts...)
}

// 初始化
func Init() *gin.Engine {
	r := gin.Default()
	for _, opt := range options {
		opt(r)
	}
	return r
}
```

`main.go` 中按如下方式先注册子 app 中的路由，然后再进行路由的初始化：

```go
func main() {
	// 加载多个APP的路由配置
	routers.Include(shop.Routers, blog.Routers)
	// 初始化路由
	r := routers.Init()
	if err := r.Run(); err != nil {
		fmt.Println("startup service failed, err:%v\n", err)
	}
}
```

