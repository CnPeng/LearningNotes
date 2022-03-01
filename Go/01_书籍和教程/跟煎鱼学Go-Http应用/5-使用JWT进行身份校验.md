# 1. 5-使用JWT进行身份校验

>2022-01-20 21:03

[点击查看原文](https://eddycjy.com/posts/go/gin/2018-02-14-jwt/)

## 1.1. 涉及知识点

JWT

## 1.2. 本文目标

在前面几节中，我们已经基本的完成了 API's 的编写，但是，还存在一些非常严重的问题，例如，我们现在的 API 是可以随意调用的，这显然还不安全全，在本文中我们通过 [jwt-go](https://github.com/dgrijalva/jwt-go)（[GoDoc](https://godoc.org/github.com/dgrijalva/jwt-go)）的方式来简单解决这个问题。

## 1.3. 编写 jwt 工具包

### 1.3.1. 下载依赖包

首先，我们下载 [jwt-go](https://github.com/dgrijalva/jwt-go) 的依赖包，如下：

```go
go get -u github.com/dgrijalva/jwt-go
```

### 1.3.2. 编写 jwt 工具包

我们需要编写一个 jwt 的工具包，我们在 `pkg` 下的 `util` 目录新建 `jwt.go` ，写入文件内容：

```go
package util

import (
	"time"

	jwt "github.com/dgrijalva/jwt-go"

	"github.com/EDDYCJY/go-gin-example/pkg/setting"
)

var jwtSecret = []byte(setting.JwtSecret)

type Claims struct {
	Username string `json:"username"`
	Password string `json:"password"`
	jwt.StandardClaims
}

func GenerateToken(username, password string) (string, error) {
	nowTime := time.Now()
	expireTime := nowTime.Add(3 * time.Hour)

	claims := Claims{
		username,
		password,
		jwt.StandardClaims {
			ExpiresAt : expireTime.Unix(),
			Issuer : "gin-blog",
		},
	}

	tokenClaims := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
	token, err := tokenClaims.SignedString(jwtSecret)

	return token, err
}

func ParseToken(token string) (*Claims, error) {
	tokenClaims, err := jwt.ParseWithClaims(token, &Claims{}, func(token *jwt.Token) (interface{}, error) {
		return jwtSecret, nil
	})

	if tokenClaims != nil {
		if claims, ok := tokenClaims.Claims.(*Claims); ok && tokenClaims.Valid {
			return claims, nil
		}
	}

	return nil, err
}
```

在这个工具包，我们涉及到的主要知识点如下：

知识点 | 说明
---|---
`NewWithClaims(method SigningMethod, claims Claims)` | method 参数对应着 `SigningMethodHMAC struct{}`，其包含 `SigningMethodHS256`、`SigningMethodHS384` 、`SigningMethodHS512` 三种 `crypto.Hash` 方案
`func (t *Token) SignedString(key interface{}) ` | 该方法内部生成签名字符串，再用于获取完整、已签名的 token
`func (p *Parser) ParseWithClaims` | 用于解析鉴权的声明，方法内部主要是具体的解码和校验的过程，最终返回 `*Token`
`func (m MapClaims) Valid()` |  验证基于时间的声明 `exp`, `iat`, `nbf`，注意如果没有任何声明在令牌中，仍然会被认为是有效的。并且对于时区偏差没有计算方法

## 1.4. 编写 gin 中间件

有了 jwt 工具包，接下来我们要编写要用于 Gin 的中间件，我们在 `middleware` 下新建 `jwt` 目录，新建 `jwt.go` 文件，写入内容：

```go
package jwt

import (
	"time"
	"net/http"

	"github.com/gin-gonic/gin"

	"github.com/EDDYCJY/go-gin-example/pkg/util"
	"github.com/EDDYCJY/go-gin-example/pkg/e"
)

func JWT() gin.HandlerFunc {
	return func(c *gin.Context) {
		var code int
		var data interface{}

		code = e.SUCCESS
		token := c.Query("token")
		if token == "" {
			code = e.INVALID_PARAMS
		} else {
			claims, err := util.ParseToken(token)
			if err != nil {
				code = e.ERROR_AUTH_CHECK_TOKEN_FAIL
			} else if time.Now().Unix() > claims.ExpiresAt {
				code = e.ERROR_AUTH_CHECK_TOKEN_TIMEOUT
			}
		}

		if code != e.SUCCESS {
			c.JSON(http.StatusUnauthorized, gin.H{
		        "code" : code,
		        "msg" : e.GetMsg(code),
		        "data" : data,
		    })

		    c.Abort()
		    return
		}

		c.Next()
	}
}
```

## 1.5. 如何获取 Token

那么我们如何调用它呢，我们还要获取Token呢？

### 1.5.1. 新增获取 Token 的 models 逻辑

在 `models` 下新建 `auth.go` 文件，写入内容：

```go
package models

type Auth struct {
	ID int `gorm:"primary_key" json:"id"`
	Username string `json:"username"`
	Password string `json:"password"`
}

func CheckAuth(username, password string) bool {
	var auth Auth
	db.Select("id").Where(Auth{Username : username, Password : password}).First(&auth)
	if auth.ID > 0 {
		return true
	}

	return false
}
```

### 1.5.2. 新增路由逻辑

在 `routers`下的 `api` 目录新建 `auth.go` 文件，写入内容：

```go
package api

import (
	"log"
	"net/http"

	"github.com/gin-gonic/gin"
	"github.com/astaxie/beego/validation"

	"github.com/EDDYCJY/go-gin-example/pkg/e"
	"github.com/EDDYCJY/go-gin-example/pkg/util"
	"github.com/EDDYCJY/go-gin-example/models"
)

type auth struct {
	Username string `valid:"Required; MaxSize(50)"`
	Password string `valid:"Required; MaxSize(50)"`
}

func GetAuth(c *gin.Context) {
	username := c.Query("username")
	password := c.Query("password")

	valid := validation.Validation{}
	a := auth{Username: username, Password: password}
	ok, _ := valid.Valid(&a)

	data := make(map[string]interface{})
	code := e.INVALID_PARAMS
	if ok {
		isExist := models.CheckAuth(username, password)
		if isExist {
			token, err := util.GenerateToken(username, password)
			if err != nil {
				code = e.ERROR_AUTH_TOKEN
			} else {
				data["token"] = token

				code = e.SUCCESS
			}

		} else {
			code = e.ERROR_AUTH
		}
	} else {
		for _, err := range valid.Errors {
            log.Println(err.Key, err.Message)
        }
	}

	c.JSON(http.StatusOK, gin.H{
        "code" : code,
        "msg" : e.GetMsg(code),
        "data" : data,
    })
}
```

### 1.5.3. 注册路由

我们打开 `routers` 目录下的 `router.go` 文件，修改文件内容（新增获取 token 的方法）：

```go
package routers

import (
    "github.com/gin-gonic/gin"

    "github.com/EDDYCJY/go-gin-example/routers/api"
    "github.com/EDDYCJY/go-gin-example/routers/api/v1"
    "github.com/EDDYCJY/go-gin-example/pkg/setting"
)

func InitRouter() *gin.Engine {
    r := gin.New()

    r.Use(gin.Logger())

    r.Use(gin.Recovery())

    gin.SetMode(setting.RunMode)

    r.GET("/auth", api.GetAuth)

    apiv1 := r.Group("/api/v1")
    {
        ...
    }

    return r
}
```

### 1.5.4. 验证 Token

获取 token 的 API 方法就到这里啦，让我们来测试下是否可以正常使用吧！

重启服务后，用 GET 方式访问 `http://127.0.0.1:8000/auth?username=test&password=test123456`，查看返回值是否正确

```json
{
  "code": 200,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6InRlc3QiLCJwYXNzd29yZCI6InRlc3QxMjM0NTYiLCJleHAiOjE1MTg3MjAwMzcsImlzcyI6Imdpbi1ibG9nIn0.-kK0V9E06qTHOzupQM_gHXAGDB3EJtJS4H5TTCyWwW8"
  },
  "msg": "ok"
}
```

我们有了 token 的 API，也调用成功了

## 1.6. 使用 gin 中间件

接下来我们将中间件接入到 Gin 的访问流程中

我们打开 `routers` 目录下的 `router.go` 文件，修改文件内容（新增引用包和中间件引用）

```go
package routers

import (
    "github.com/gin-gonic/gin"

    "github.com/EDDYCJY/go-gin-example/routers/api"
    "github.com/EDDYCJY/go-gin-example/routers/api/v1"
    "github.com/EDDYCJY/go-gin-example/pkg/setting"
    "github.com/EDDYCJY/go-gin-example/middleware/jwt"
)

func InitRouter() *gin.Engine {
    r := gin.New()

    r.Use(gin.Logger())

    r.Use(gin.Recovery())

    gin.SetMode(setting.RunMode)

    r.GET("/auth", api.GetAuth)

    apiv1 := r.Group("/api/v1")

    // 使用中间件
    apiv1.Use(jwt.JWT())

    {
        ...
    }

    return r
}
```

当前目录结构：

```cmd
go-gin-example/
├── conf
│   └── app.ini
├── main.go
├── middleware
│   └── jwt
│       └── jwt.go
├── models
│   ├── article.go
│   ├── auth.go
│   ├── models.go
│   └── tag.go
├── pkg
│   ├── e
│   │   ├── code.go
│   │   └── msg.go
│   ├── setting
│   │   └── setting.go
│   └── util
│       ├── jwt.go
│       └── pagination.go
├── routers
│   ├── api
│   │   ├── auth.go
│   │   └── v1
│   │       ├── article.go
│   │       └── tag.go
│   └── router.go
├── runtime
```

到这里，我们的 JWT 编写就完成啦！

## 1.7. 验证功能

我们来测试一下，再次访问

* http://127.0.0.1:8000/api/v1/articles——未传递 token 
* http://127.0.0.1:8000/api/v1/articles?token=23131——传递假的 token

得到的反馈应该是：

```json
{
  "code": 400,
  "data": null,
  "msg": "请求参数错误"
}

{
  "code": 20001,
  "data": null,
  "msg": "Token鉴权失败"
}
```

要想获取到 data 数据，我们需要先访问 `http://127.0.0.1:8000/auth?username=test&password=test123456`，得到 `token` ：

```json
{
  "code": 200,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6InRlc3QiLCJwYXNzd29yZCI6InRlc3QxMjM0NTYiLCJleHAiOjE1MTg3MjQ2OTMsImlzcyI6Imdpbi1ibG9nIn0.KSBY6TeavV_30kfmP7HWLRYKP5TPEDgHtABe9HCsic4"
  },
  "msg": "ok"
}
```

再用包含 `token` 的 URL 参数去访问我们的应用 API，

访问 `http://127.0.0.1:8000/api/v1/articles?token=eyJhbGci...`，检查接口返回值：

```json
{
  "code": 200,
  "data": {
    "lists": [
      {
        "id": 2,
        "created_on": 1518700920,
        "modified_on": 0,
        "tag_id": 1,
        "tag": {
          "id": 1,
          "created_on": 1518684200,
          "modified_on": 0,
          "name": "tag1",
          "created_by": "",
          "modified_by": "",
          "state": 0
        },
        "content": "test-content",
        "created_by": "test-created",
        "modified_by": "",
        "state": 0
      }
    ],
    "total": 1
  },
  "msg": "ok"
}
```

返回正确，至此我们的 `jwt-go` 在 Gin 中的验证就完成了！

## 1.8. 参考

[本系列示例代码：go-gin-example](https://github.com/EDDYCJY/go-gin-example)

## 1.9. 补充

摘录自原文评论区。

* 用户名和密码不要都直接放在 Claims 里面，这很危险。别人拿到 token 直接 base64 解码就可以看到帐号密码的明文了——不应该把敏感信息放在 jwt 里
* 如果生成 token 时报 `key is of invalid type` 先看一下 `util/jwt.go` 用的 `tokenClaims := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)` 是不是 `HS256` 的，千万不要粗心写成ES256！！！
* 顺带贴一个在 debug 的时候发现的另一个小坑，即 `func (*jwt.Token).SignedString(key interface{})` 中的 `key` 要用 `[]byte`。——[dgrijalva/jwt-go/issues#65](https://github.com/dgrijalva/jwt-go/issues/65)
* jwt 不是普通的 `base64`，推荐用这个解析——解码工具：[https://jwt.io/#debugger](https://jwt.io/#debugger)；参考资料：[https://jwt.io/introduction/](https://jwt.io/introduction/)
