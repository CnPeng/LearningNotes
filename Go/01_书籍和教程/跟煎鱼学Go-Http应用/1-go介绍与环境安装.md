# 1. 1-go介绍与环境安装

>2022-01-19 21:24 

## 1.1. 本文目标

* 学会安装 Go。
* 知道什么是 Go。
* 知道什么是 Go modules。
* 了解 Go modules 的小历史。
* 学会简单的使用 Go modules。
* 了解 Gin，并简单跑起一个 Demo。

## 1.2. 准备环节

### 1.2.1. 安装 Go



#### 1.2.1.1. Centos

首先，[根据对应的操作系统选择安装包 下载](https://studygolang.com/dl)，在这里我使用的是 Centos 64 位系统，如下：

```
$ wget https://studygolang.com/dl/golang/go1.13.1.linux-amd64.tar.gz

$ tar -zxvf go1.13.1.linux-amd64.tar.gz

$ mv go/ /usr/local/
```

配置 `/etc/profile`

```
vi /etc/profile
```

添加环境变量 GOROOT 和将 GOBIN 添加到 PATH 中：

```
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
```

配置完毕后，执行命令令其生效：

```
source /etc/profile
```

在控制台输入 `go version`，若输出版本号则安装成功，如下：

```
$ go version
go version go1.13.1 linux/amd64
```

#### 1.2.1.2. MacOS

在 MacOS 上安装 Go 最方便的办法就是使用 brew，安装如下：

```
$ brew install go
```

升级命令如下：

```
$ brew upgrade go
```

注：升级命令你不需要执行，但我想未来你有一天会用到的。

同样在控制台输入 `go version`，若输出版本号则安装成功。

### 1.2.2. 了解 Go

#### 1.2.2.1. 是什么

Go is an open source programming language that makes it easy to build simple, reliable, and efficient software.

上述为官方说明，如果简单来讲，大致为如下几点：

* Go 是编程语言。
* 谷歌爸爸撑腰。
* 语言级高并发。
* 上手快，入门简单。
* 简洁，很有特色。
* 国内使用人群逐年增多。

#### 1.2.2.2. 有什么

那么大家会有些疑问，纠结 Go 本身有什么东西，我们刚刚设置的环境变量又有什么用呢，甚至作为一名老粉，你会纠结 GOPATH 去哪里了，我们一起接着往下看。

##### 1.2.2.2.1. 目录结构

首先，我们在解压的时候会得到一个名为 go 的文件夹，其中包括了所有 Go 语言相关的一些文件，如下：

```
$ tree -L 1 go
go
├── api
├── bin
├── doc
├── lib
├── misc
├── pkg
├── src
├── test
└── ...
```

在这之中包含了很多文件夹和文件，我们来简单说明其中主要文件夹的作用：

目录 | 说明
---|---
api | 用于存放依照 Go 版本顺序的 API 增量列表文件。这里所说的 API 包含公开的变量、常量、函数等。这些 API 增量列表文件用于 Go 语言 API 检查
bin | 用于存放主要的标准命令文件（可执行文件），包含go、godoc、gofmt
blog | 用于存放官方博客中的所有文章
doc | 用于存放标准库的 HTML 格式的程序文档。我们可以通过 godoc 命令启动一个 Web 程序展示这些文档
lib | 用于存放一些特殊的库文件
misc | 用于存放一些辅助类的说明和工具
pkg | 用于存放安装 Go 标准库后的所有归档文件（以 `.a` 结尾的文件）。注意，你会发现其中有名称为`linux_amd64`的文件夹，我们称为平台相关目录。这类文件夹的名称由对应的操作系统和计算架构的名称组合而成。通过`go install`命令，Go 程序会被编译成平台相关的归档文件存放到其中
src | 用于存放 Go自身、Go 标准工具以及标准库的所有源码文件
test | 存放用来测试和验证 Go 本身的所有相关文件

##### 1.2.2.2.2. 环境变量

你可能会疑惑刚刚设置的环境变量是什么，如下：

* GOROOT：Go 的根目录。
* PATH 下增加 `$GOROOT/bin` ： Go的 bin 下会存放可执行文件，我们把他加入 `$PATH` 后，未来拉下来并编译后的二进制文件就可以直接在命令行使用。

那在什么东西都不下载的情况下，`$GOBIN` 下面有什么呢，如下：

```
bin/ $ls
go  gofmt
```

* go：Go 二进制本身。
* gofmt：代码格式化工具。

因此我们刚刚把 `$GOBIN` 加入到 `$PATH` 后，你执行 `go version` 命令后就可以查看到对应的输出结果。

注：MacOS 用 brew 安装的话就不需要。

##### 1.2.2.2.3. 放在哪

你现在知道 Go 是什么了，也知道 Go 的源码摆在哪了，你肯定会想，那我应用代码放哪呢，答案是：**在 Go1.11+ 和开启 Go Modules 的情况下摆哪都**行。

### 1.2.3. 了解 Go Modules

#### 1.2.3.1. 了解历史

在过去，Go 的依赖包管理在工具上混乱且不统一，有 dep，有 glide，有 govendor…甚至还有因为外网的问题，频频导致拉不下来包，很多人苦不堪言，盼着官方给出一个大一统做出表率。

而在 Go modules 正式出来之前还有一个叫 dep 的项目，我们在上面有提到，它是 Go 的一个官方实验性项目，目的也是为了解决 Go 在依赖管理方面的问题，当时社区里面几乎所有的人都认为 dep 肯定就是未来 Go 官方的依赖管理解决方案了。

但是万万没想到，半路杀出个程咬金，Russ Cox 义无反顾地推出了 Go modules，这瞬间导致一石激起千层浪，让社区炸了锅。大家一致认为 Go team 实在是太霸道、太独裁了，连个招呼都不打一声。我记得当时有很多人在网上跟 Russ Cox 口水战，各种依赖管理解决方案的专家都冒出来发表意见，讨论范围甚至一度超出了 Go 语言的圈子触及到了其他语言的领域。

当然，最后，推成功了，**Go modules 已经进入官方工具链中，与 Go 深深结合，以前常说的 GOPATH 终将会失去它原有的作用，而且它还提供了 GOPROXY 间接解决了国内访问外网的问题。**

#### 1.2.3.2. 了解 Russ Cox

在上文中提到的 Russ Cox 是谁呢，他是 Go 这个项目目前代码提交量最多的人，甚至是第二名的两倍还要多（从 2019 年 09 月 30 日前来看）。

Russ Cox 还是 Go 现在的掌舵人（大家应该知道之前 Go 的掌舵人是 Rob Pike，但是听说由于他本人不喜欢特朗普执政所以离开了美国，然后他岁数也挺大的了，所以也正在逐渐交权，不过现在还是在参与 Go 的发展）。

Russ Cox 的个人能力相当强，看问题的角度也很独特，这也就是为什么他刚一提出 Go modules 的概念就能引起那么大范围的响应。虽然是被强推的，但事实也证明当下的 Go modules 表现得确实很优秀，所以这表明一定程度上的 “独裁” 还是可以接受的，至少可以保证一个项目能更加专一地朝着一个方向发展。

#### 1.2.3.3. 初始化行为

在前面我们已经了解到 Go 依赖包管理的历史情况，接下来我们将正式的进入使用，首先你需要有一个你喜欢的目录，例如：
`$ mkdir ~/go-application && cd ~/go-application`，然后执行如下命令：

```
$ mkdir go-gin-example && cd go-gin-example

$ go env -w GO111MODULE=on

$ go env -w GOPROXY=https://goproxy.cn,direct

$ go mod init github.com/EDDYCJY/go-gin-example
go: creating new go.mod: module github.com/EDDYCJY/go-gin-example

$ ls
go.mod
```

命令 | 含义
---|---
`mkdir xxx && cd xxx` | 创建并切换到项目目录里去。
`go env -w GO111MODULE=on` | 打开 Go modules 开关（目前在 Go1.13 中默认值为 auto）。
`go env -w GOPROXY=...` | 设置 GOPROXY 代理，这里主要涉及到两个值，第一个是 **https://goproxy.cn**，它是由七牛云背书的一个强大稳定的 Go 模块代理，可以有效地解决你的外网问题；第二个是 **direct**，它是一个特殊的 fallback 选项，它的作用是用于指示 Go 在拉取模块时遇到错误会回源到模块版本的源地址去抓取（比如 GitHub 等）。
`go mod init [MODULE_PATH]` | 初始化 Go modules，它将会生成 `go.mod` 文件，需要注意的是 MODULE_PATH 填写的是模块引入路径，你可以根据自己的情况修改路径。

在执行了上述步骤后，初始化工作已完成，并且会在目录中生成一个 `go.mod` 文件，我们打开 `go.mod` 文件看看，如下：

```
module github.com/EDDYCJY/go-gin-example

go 1.13
```

默认的 go.mod 文件里主要是两块内容，一个是 **当前的模块路径** 和 **预期的 Go 语言版本**。

#### 1.2.3.4. 基础使用

* go get 命令

命令 | 说明
--- | ---
`go get golang.org/x/text@latest` | 拉取最新的版本(优先择取 tag)：
`go get golang.org/x/text@master` | 拉取 master 分支的最新 commit：
`go get golang.org/x/text@v0.3.2` | 拉取 tag 为 v0.3.2 的 commit：
`go get golang.org/x/text@342b2e` | 拉取 hash 为 342b231 的 commit，最终会被转换为 v0.3.2——即 hash 对应的 commit 
`go get -u` |  更新现有的依赖

* go mod 命令

命令 | 说明
--- | ---
`go mod init` |  生成 go.mod 文件 (Go 1.13 中唯一一个可以生成 go.mod 文件的子命令)
`go mod download` | 下载 go.mod 文件中指明的所有依赖
`go mod tidy` |  整理现有的依赖
`go mod graph` | 查看现有的依赖结构
`go mod edit` |  编辑 go.mod 文件
`go mod vendor` |  导出现有的所有依赖 (事实上 Go modules 正在淡化 Vendor 的概念)
`go mod verify` |  校验一个模块是否被篡改过

这一小节主要是针对 Go modules 的基础使用讲解，还没具体的使用，是希望你能够留个印象，因为在后面章节会不断夹杂 Go modules 的知识点。

注：建议阅读[官方文档 wiki/Modules。](https://github.com/golang/go/wiki/Modules)

## 1.3. 开始 Gin 之旅

### 1.3.1. 是什么

Gin is a HTTP web framework written in Go (Golang). It features a Martini-like API with much better performance – up to 40 times faster. If you need smashing performance, get yourself some Gin.

Gin 是用 Go 开发的一个微框架，类似 Martinier 的 API，重点是小巧、易用、性能好很多，也因为 [httprouter](https://github.com/julienschmidt/httprouter) 的性能提高了 40 倍。

### 1.3.2. 安装

#### 1.3.2.1. 安装

我们回到刚刚创建的 `go-gin-example` 目录下，在命令行下执行如下命令：

```
$ go get -u github.com/gin-gonic/gin

go: downloading golang.org/x/sys v0.0.0-20190222072716-a9d3bda3a223
go: extracting golang.org/x/sys v0.0.0-20190222072716-a9d3bda3a223
go: finding github.com/gin-contrib/sse v0.1.0
go: finding github.com/ugorji/go v1.1.7
go: finding gopkg.in/yaml.v2 v2.2.3
go: finding golang.org/x/sys latest
go: finding github.com/mattn/go-isatty v0.0.9
go: finding github.com/modern-go/concurrent latest
...
```

#### 1.3.2.2. go.sum

这时候你再检查一下 `go-gin-example` 目录，会发现多个了个 `go.sum` 文件，如下：

```
github.com/davecgh/go-spew v1.1.0/go.mod h1:J7Y8YcW...
github.com/davecgh/go-spew v1.1.1/go.mod h1:J7Y8YcW...
github.com/gin-contrib/sse v0.0.0-20190301062529-5545eab6dad3 h1:t8FVkw33L+wilf2QiWkw0UV77qRpcH/JHPKGpKa2E8g=
github.com/gin-contrib/sse v0.0.0-20190301062529-5545eab6dad3/go.mod h1:VJ0WA2...
github.com/gin-contrib/sse v0.1.0 h1:Y/yl/+YNO...
...
```

`go.sum` 文件详细**罗列了当前项目直接或间接依赖的所有模块版本**，并写明了那些模块版本的 SHA-256 哈希值以备 Go 在今后的操作中保证项目所依赖的那些模块版本不会被篡改。

#### 1.3.2.3. go.mod

既然我们下载了依赖包，`go.mod` 文件会不会有所改变呢，我们再去看看，如下：

```
module github.com/EDDYCJY/go-gin-example

go 1.13

require (
        github.com/gin-contrib/sse v0.1.0 // indirect
        github.com/gin-gonic/gin v1.4.0 // indirect
        github.com/golang/protobuf v1.3.2 // indirect
        github.com/json-iterator/go v1.1.7 // indirect
        github.com/mattn/go-isatty v0.0.9 // indirect
        github.com/ugorji/go v1.1.7 // indirect
        golang.org/x/sys v0.0.0-20190927073244-c990c680b611 // indirect
        gopkg.in/yaml.v2 v2.2.3 // indirect
)
```

确确实实发生了改变，那多出来的东西又是什么呢，`go.mod` 文件又保存了什么信息呢?

实际上 go.mod 文件是启用了 Go modules 的项目所必须的最重要的文件，因为它**描述了当前项目（也就是当前模块）的元信息，每一行都以一个动词开头，目前有以下 5 个动词**:

动词|说明
---|---
module | 用于定义当前项目的模块路径。
go | 用于设置预期的 Go 版本。
require | 用于设置一个特定的模块版本。
exclude | 用于从使用中排除一个特定的模块版本。
replace | 用于将一个模块版本替换为另外一个模块版本。

你可能还会疑惑 `indirect` 是什么东西，**`indirect` 的意思是传递依赖，也就是非直接依赖**。

### 1.3.3. 测试

编写一个 `test.go` 文件

```go
package main

import "github.com/gin-gonic/gin"

func main() {
  r := gin.Default()
  r.GET("/ping", func(c *gin.Context) {
    c.JSON(200, gin.H{
      "message": "pong",
    })
  })
  r.Run() // listen and serve on 0.0.0.0:8080
}
```

执行test.go

```
$ go run test.go
...
[GIN-debug] GET    /ping                     --> main.main.func1 (3 handlers)
[GIN-debug] Environment variable PORT is undefined. Using port :8080 by default
[GIN-debug] Listening and serving HTTP on :8080
```

访问 `$HOST:8080/ping`，若返回 `{"message":"pong"}` 则正确

```
curl 127.0.0.1:8080/ping
```

至此，我们的环境安装和初步运行都基本完成了。

> [gin 官方的的更多示例](https://github.com/gin-gonic/examples)

## 1.4. 再想一想

刚刚在执行了命令 `$ go get -u github.com/gin-gonic/gin` 后，我们查看了 `go.mod` 文件，如下：

```
...
require (
        github.com/gin-contrib/sse v0.1.0 // indirect
        github.com/gin-gonic/gin v1.4.0 // indirect
        ...
)
```

你会发现 `go.mod` 里的 `github.com/gin-gonic/gin` 是 `indirect` 模式，这显然不对啊，因为我们的应用程序已经实际的编写了 gin server 代码了，我就想把它调对，怎么办呢，在应用根目录下执行如下命令：

```
$ go mod tidy
```

该命令主要的作用是整理现有的依赖，非常的常用，执行后 `go.mod` 文件内容为：

```
...
require (
        github.com/gin-contrib/sse v0.1.0 // indirect
        github.com/gin-gonic/gin v1.4.0
        ...
)
```

可以看到 `github.com/gin-gonic/gin` 已经变成了直接依赖，调整完毕。

## 1.5. 参考

### 1.5.1. 本系列示例代码

* [go-gin-example](https://github.com/EDDYCJY/go-gin-example)

### 1.5.2. 相关文档

* [Gin](https://github.com/gin-gonic/gin)
* [Gin Web Framework](https://gin-gonic.github.io/gin/)
* [干货满满的 Go Modules 和 goproxy.cn](https://book.eddycjy.com/golang/talk/goproxy-cn.html)
