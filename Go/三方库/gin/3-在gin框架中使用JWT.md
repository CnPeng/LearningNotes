[原文：《在gin框架中使用JWT》——李文周](https://www.liwenzhou.com/posts/Go/jwt_in_gin/)

> CnPeng 建议先阅读 [阮一峰的《JWT入门教程》](2-JSONWebToken入门教程.md)，再阅读本文


---

## 3.1 JWT 介绍

### 3.1.1 JWT 介绍

`JWT` 全称 `JSON Web Token` **是一种跨域认证解决方案**，属于一个开放的标准，它规定了一种 `Token` 实现方式，目前**多用于`前后端分离项目`和`OAuth2.0业务`场景**下。

### 3.1.2 为什么需要 JWT

在之前的一些 web 项目中，我们通常使用的是 `Cookie-Session` 模式实现用户认证。相关流程大致如下：

* 用户在浏览器端填写用户名和密码，并发送给服务端
* 服务端对用户名和密码校验通过后，会生成一份保存当前用户相关信息的 `session` 数据和一个与之对应的标识（通常称为 `session_id` ）
* 服务端返回响应时将上一步的 `session_id` 写入用户浏览器的 `Cookie`
* 后续用户来自该浏览器的每次请求都会自动携带包含 `session_id` 的 `Cookie`
* 服务端通过请求中的 `session_id` 就能找到之前保存的该用户那份 `session` 数据，从而获取该用户的相关信息。

这种方案依赖于客户端（浏览器）保存 `Cookie`，并且需要在服务端存储用户的 `session` 数据。

在移动互联网时代，我们的用户可能使用浏览器也可能使用 APP 来访问我们的服务，我们的 web 应用可能是前后端分开部署在不同的端口，有时候我们还需要支持第三方登录，这样 `Cookie-Session`的模式就有些力不从心了。

`JWT` 就是一种基于 `Token` 的轻量级认证模式，服务端认证通过后，会生成一个 `JSON` 对象，经过签名后得到一个 `Token（令牌）`再发回给用户，用户后续请求只需要带上这个 `Token`，服务端解密之后就能获取该用户的相关信息了。

想要连接JWT的原理，推荐大家阅读：[阮一峰的《JWT入门教程》](2-JSONWebToken入门教程.md)

## 3.2 生成和解析 JWT

我们在这里直接使用 `jwt-go` 这个库来实现我们生成 `JWT` 和解析 `JWT` 的功能。

### 3.2.1 定义需求

我们需要定制自己的需求来决定 `JWT` 中保存哪些数据，比如我们规定在 `JWT` 中要存储 `username` 信息，那么我们就定义一个 `MyClaims` 结构体如下：

```go
// MyClaims 自定义声明结构体并内嵌jwt.StandardClaims
// jwt 包自带的 jwt.StandardClaims 只包含了官方字段
// 我们这里需要额外记录一个 username 字段，所以要自定义结构体
// 如果想要保存更多信息，都可以添加到这个结构体中
type MyClaims struct {
	Username string `json:"username"`
	jwt.StandardClaims
}
```

然后我们定义 `JWT` 的过期时间，这里以 2 小时为例：

```go
const TokenExpireDuration = time.Hour * 2
```

接下来还需要定义 `Secret`：

```go
var MySecret = []byte("夏天夏天悄悄过去")
```

### 3.2.2 生成 JWT

```go
// GenToken 生成JWT
func GenToken(username string) (string, error) {
	// 创建一个我们自己的声明
	c := MyClaims{
		"username", // 自定义字段
		jwt.StandardClaims{
			ExpiresAt: time.Now().Add(TokenExpireDuration).Unix(), // 过期时间
			Issuer:    "my-project",                               // 签发人
		},
	}
	
	// 使用指定的签名方法创建签名对象
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, c)
	
	// 使用指定的secret签名并获得完整的编码后的字符串token
	return token.SignedString(MySecret)
}
```

### 3.2.3 解析JWT

```go
// ParseToken 解析JWT
func ParseToken(tokenString string) (*MyClaims, error) {
	// 解析token
	token, err := jwt.ParseWithClaims(tokenString, &MyClaims{}, func(token *jwt.Token) (i interface{}, err error) {
		return MySecret, nil
	})
	if err != nil {
		return nil, err
	}
	if claims, ok := token.Claims.(*MyClaims); ok && token.Valid { // 校验token
		return claims, nil
	}
	return nil, errors.New("invalid token")
}
```

### 3.3 在 gin 框架中使用 JWT

首先我们注册一条路由 `/auth`，对外提供获取 `Token` 的渠道：

```go
r.POST("/auth", authHandler)
```

我们的 authHandler 定义如下：

```go
func authHandler(c *gin.Context) {
	
	// 用户发送用户名和密码过来
	var user UserInfo
	err := c.ShouldBind(&user)
	if err != nil {
		c.JSON(http.StatusOK, gin.H{
			"code": 2001,
			"msg":  "无效的参数",
		})
		return
	}
	
	// 校验用户名和密码是否正确
	if user.Username == "q1mi" && user.Password == "q1mi123" {
		// 生成Token
		tokenString, _ := GenToken(user.Username)
		c.JSON(http.StatusOK, gin.H{
			"code": 2000,
			"msg":  "success",
			"data": gin.H{"token": tokenString},
		})
		return
	}
	
	c.JSON(http.StatusOK, gin.H{
		"code": 2002,
		"msg":  "鉴权失败",
	})
	return
}
```

用户通过上面的接口获取 `Token` 之后，后续就会携带着 `Token` 再来请求我们的其他接口，这个时候就需要对这些请求的 `Token` 进行校验操作了，很显然我们应该实现一个检验 `Token` 的中间件，具体实现如下：

```go
// JWTAuthMiddleware 基于JWT的认证中间件
func JWTAuthMiddleware() func(c *gin.Context) {
	return func(c *gin.Context) {
		// 客户端携带 Token 有三种方式 1.放在请求头 2.放在请求体 3.放在URI
		// 这里假设 Token 放在 Header 的 Authorization 中，并使用 Bearer 开头
		// 这里的具体实现方式要依据你的实际业务情况决定
		authHeader := c.Request.Header.Get("Authorization")
		if authHeader == "" {
			c.JSON(http.StatusOK, gin.H{
				"code": 2003,
				"msg":  "请求头中auth为空",
			})
			c.Abort()
			return
		}
		
		// 按空格分割
		parts := strings.SplitN(authHeader, " ", 2)
		if !(len(parts) == 2 && parts[0] == "Bearer") {
			c.JSON(http.StatusOK, gin.H{
				"code": 2004,
				"msg":  "请求头中auth格式有误",
			})
			c.Abort()
			return
		}
		
		// parts[1]是获取到的 tokenString，我们使用之前定义好的解析 JWT 的函数来解析它
		mc, err := ParseToken(parts[1])
		if err != nil {
			c.JSON(http.StatusOK, gin.H{
				"code": 2005,
				"msg":  "无效的Token",
			})
			c.Abort()
			return
		}
		
		// 将当前请求的 username 信息保存到请求的上下文 c 上
		c.Set("username", mc.Username)
		c.Next() // 后续的处理函数可以用过c.Get("username")来获取当前请求的用户信息
	}
}
```

注册一个 `/home` 路由，发个请求验证一下吧。

```go
r.GET("/home", JWTAuthMiddleware(), homeHandler)

func homeHandler(c *gin.Context) {
	username := c.MustGet("username").(string)
	c.JSON(http.StatusOK, gin.H{
		"code": 2000,
		"msg":  "success",
		"data": gin.H{"username": username},
	})
}
```

如果不想自己实现上述功能，你也可以使用 Github 上别人封装好的包，比如 [`https://github.com/appleboy/gin-jwt`](https://github.com/appleboy/gin-jwt)。