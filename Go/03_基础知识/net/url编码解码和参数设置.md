# 1. url编码解码和参数设置

[原文：《Golang urlencode与urldecode编码解码》](https://www.cnblogs.com/zxqblogrecord/p/14871666.html)

golang 下可以使用 `net/url` 模块实现 urlencode 和 urldecode 操作。

具体实现的函数为 `url.QueryEscape` 和 `url.QueryUnescape`，

## 1.1. urlencode

### 1.1.1. url编码

代码如下：

```go
package main
import(
    "fmt"
    "net/url"
)
func main()  {
    var urlStr string = "运维之路"

    escapeUrl := url.QueryEscape(urlStr)
    fmt.Println("编码:",escapeUrl)

    enEscapeUrl, _ := url.QueryUnescape(escapeUrl)
    fmt.Println("解码:",enEscapeUrl)
}
```

### 1.1.2. 设置 url 携带的参数

如果涉及到多个参数转码的，这在 get 和 post 请求中都有，比如 `id=100&site=361way.com` 这样的。可以在通过 `url.Values` 进行增加后转码，发下：

```go
package main
import (
    "fmt"
    "net/url"
)
func main() {
    params := url.Values{}
    params.Add("name", "@Rajeev")
    params.Add("phone", "+919999999999")
    fmt.Println(params.Encode())
}
```

输出：

```go
name=%40Rajeev&phone=%2B919999999999
```

同样的，我们通过 url 的其他函数处理操作过后，一个典型的 get 请求的 URL 如下：


```go
package main
import (
    "fmt"
    "net/url"
)

func main() {
    // Let's start with a base url
    baseUrl, err := url.Parse("http://www.mywebsite.com")
    if err != nil {
        fmt.Println("Malformed URL: ", err.Error())
        return
    }

    // Add a Path Segment (Path segment is automatically escaped)
    baseUrl.Path += "path with?reserved characters"

    // 组织参数
    params := url.Values{}
    params.Add("q", "Hello World")
    params.Add("u", "@rajeev")

    // 对参数编码并设置给 url
    baseUrl.RawQuery = params.Encode()
    fmt.Printf("Encoded URL is %q\n", baseUrl.String())
}
```

输出结果

```go
Encoded URL is "http://www.mywebsite.com/path%20with%3Freserved%20characters?q=Hello+World&u=%40rajeev"
```


## 1.2. urldecode

### 1.2.1. url 解码

简单的 url 解码，直接使用 Unescape 就可以了，如下：

```go
package main
import (
    "fmt"
    "log"
    "net/url"
)

func main() {
    encodedValue := "Hell%C3%B6+W%C3%B6rld%40Golang"

    decodedValue, err := url.QueryUnescape(encodedValue)
    if err != nil {
        log.Fatal(err)
        return
    }
    fmt.Println(decodedValue)
}
```

### 1.2.2. 读取 url 中携带的参数

带 form 参数，就像上面提到的 `param1=value1&m2=value2` 这种格式的可以使用 `url.ParseQuery()` 参数解析成 `map[string][]string` 格式，如下：

```go
package main
import (
    "fmt"
    "log"
    "net/url"
)

func main() {
    queryStr := "name=Rajeev%20Singh&phone=%2B9199999999&phone=%2B628888888888"
    params, err := url.ParseQuery(queryStr)

    if err != nil {
        log.Fatal(err)
        return
    }
    fmt.Println("Query Params: ")

    for key, value := range params {
        fmt.Printf("  %v = %v\n", key, value)
    }
}
```

其输出为：

```go
Query Params:
  name = [Rajeev Singh]
  phone = [+9199999999 +628888888888]
```

一个 get 请求类，后面带参数的 URL，可以通过如下方式解析并解码:

```go
package main
import (
    "fmt"
    "log"
    "net/url"
)

func main() {
    u, err := url.Parse("https://www.website.com/person?name=Rajeev%20Singh&phone=%2B919999999999&phone=%2B628888888888")
    if err != nil {
        log.Fatal(err)
        return
    }

    // 获取 schema (如 https) 和 host（如 www.website.com）
    fmt.Println("Scheme: ", u.Scheme)
    fmt.Println("Host: ", u.Host)

    // 获取参数信息。得到 map[string][]string 对象，即 Values 对象
    queries := u.Query()
    fmt.Println("Query Strings: ")
    for key, value := range queries {
        fmt.Printf("  %v = %v\n", key, value)
    }
    fmt.Println("Path: ", u.Path)
}
```

输出如下：

```go
Scheme:  https
Host:  www.website.com
Query Strings:
  phone = [+919999999999 +628888888888]
  name = [Rajeev Singh]
Path:  /person
```

## 1.3. 完整示例：

下面示例代码实现的逻辑是：web 端使用 GET 方式请求服务端的 `GoToThirdPlatform()` 方法对应的路由，路由内部判断参数中的 `target_url` 是否合法，合法则追加参数到 `target_url` 中，并重定向到其对应的页面。

注意：

* 服务端使用了 gin 框架
* platForm 和 domainName 存储在了 `.toml` 格式的配置文件中，`config.GetGlobalConfig()` 是对 [https://github.com/toml-lang/toml](https://github.com/toml-lang/toml) 库中解析函数的包装。
* `errors.New400Error()` 函数也是对 errors 的包装

```go
// ThirdLogin 三方（单点）登录相关的逻辑--我方跳转对方系统
type ThirdLogin struct {
}

// GoToThirdPlatform 通过单点登录跳转到三方系统
func (a *ThirdLogin) GoToThirdPlatform(c *gin.Context) {
	platForm := c.Query("q")

	// 查询三方系统是否支持
	flags := config.GetGlobalConfig().ThirdPlatform.Flags
	flagIndex := util.GetIndexInStrSlice(flags, platForm)
	if flagIndex == -1 {
		ginplus.ResError(c, errors.New400Error("不支持的三方平台"))
		return
	}

	// 获取三方系统标识对应的合法域名
	domainName := ""
	runMode := config.GetGlobalConfig().RunMode
	if runMode == "debug" {
		devUrls := config.GetGlobalConfig().ThirdPlatform.DevUrls
		domainName = devUrls[flagIndex]
	} else {
		releaseUrls := config.GetGlobalConfig().ThirdPlatform.ReleaseUrls
		domainName = releaseUrls[flagIndex]
	}

	// 校验目标 url 中是否包含合法域名
	targetUrl := c.Query("target_url")
	urlObj, err := url.Parse(targetUrl)
	if err != nil {
		ginplus.ResError(c,errors.New400Error("跳转链接有误"))
		return
	}

	if urlObj.Hostname() != domainName {
		ginplus.ResError(c, errors.New400Error("不支持的三方平台——域名有误"))
		return
	}

	switch platForm {
	case "bim":
		a.GoToBim(c, urlObj)
	default:
		ginplus.ResError(c, errors.New400Error("不支持的三方平台"))
	}
}

// GoToBim 跳转到 bim 系统
// @Summary 跳转到 bim 系统
// @Param Authorization header string false "Bearer 用户令牌"
// @Param q query  string true "平台标识，bim--bim系统"
// @Param target_url query string true "跳转目标"
// @Success 200 schema.HTTPStatus
// @Failure 400 schema.HTTPError "{error:{code:0,message:未知的查询类型}}"
// @Failure 401 schema.HTTPError "{error:{code:0,message:未授权}}"
// @Failure 500 schema.HTTPError "{error:{code:0,message:服务器错误}}"
// @Router GET /web/v1/redirect/third?q=bim
func (a *ThirdLogin) GoToBim(c *gin.Context, urlObj *url.URL) {
	sysFlag := "EAC"
	slat := "EACToBIM"
	userID := ginplus.GetUserID(c)
	// userID 转 base64 之后传递给三方。注意：如果后续需要通过UID获取我方数据，不要忘了解析回来
	base64UID:= util.S(userID).EncodeToBase64()

	// 组织跳转需要的参数： token 数据
	sha1Param := append([]byte(sysFlag), base64UID...)
	sha1Re := util.Hash(string(sha1Param), util.HashSha1, false)
	md5Param := append([]byte(sha1Re), slat...)
	//token = md5(sha1( $system_str$user_id)$salt)
	token := util.Hash(string(md5Param), util.HashMd5, false)

	// 拼接重定向路由
	// 读取链接中已经携带的参数
	urlParams := urlObj.Query()
	// 添加路由参数
	urlParams.Add("token", token)
	urlParams.Add("user_id", base64UID)
	////urlParams := url.Values{}
	//urlParams.Add("ie", "UTF-8")
	//urlParams.Add("wd", string(aesUID))

	// 将路由参数设置给 url
	urlObj.RawQuery = urlParams.Encode()

	c.Redirect(302, urlObj.String())
}
```