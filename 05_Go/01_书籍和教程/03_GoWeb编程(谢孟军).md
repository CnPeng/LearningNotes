《Go Web 编程--谢孟军》读书笔记

## 一、Go 环境变量

### 1、Go 安装

略


### 2、GOPATH 与工作空间

#### (1)、GOPATH 设置

参考 新加坡版的 《Go Web 编程》中的附录

GOPATH 允许多个目录，当有多个目录时，Windows 使用逗号分隔，Linux 使用冒号分隔，当有多个 GOPATH 时，默认会将 `go get` 的内容放在第一个目录下。

GOPATH 约定包含三个子目录：

* src : 存放源代码（ 比如 .go、.c、.h、.s 等 ）
* pkg : 存放编译后生成的文件（ 比如 .a ）
* bin : 存放编译后生成的可执行文件（ 为了方便，可以把此目录加入到 `PATH` 变量中，如果有多个 gopath, 那么使用 `${GOPATH//://bin:}/bin` 添加所有的 bin 目录）


#### (2)、代码目录结构规划

略

#### (3)、编译应用

有两种方式：

* 方式1：进入对应的应用包目录，然后执行 `go install`  
* 方式2：在任意的目录下执行：`go install xxx` (xxx 表示完整的包路径)

`go build` 是编译，`go install` 是编译并安装


#### (4)、获取并使用远程包

获取：`go get github.com/xxx/xxx`

使用：`import "github.com/xxx/xxx"`

> `go get` 本质上可以理解为先将远程包拷贝到 src 目录，然后自动执行了 `go install`

#### (5)、程序的整体结构

略

### 3、GO 命令

完整 go 命令：

```go
CnPeng:go cnpeng$ go
Go is a tool for managing Go source code.

Usage:

        go <command> [arguments]

The commands are:

        bug         start a bug report
        build       compile packages and dependencies
        clean       remove object files and cached files
        doc         show documentation for package or symbol
        env         print Go environment information
        fix         update packages to use new APIs
        fmt         gofmt (reformat) package sources
        generate    generate Go files by processing source
        get         download and install packages and dependencies
        install     compile and install packages and dependencies
        list        list packages or modules
        mod         module maintenance
        run         compile and run Go program
        test        test packages
        tool        run specified go tool
        version     print Go version
        vet         report likely mistakes in packages

Use "go help <command>" for more information about a command.

Additional help topics:

        buildmode   build modes
        c           calling between Go and C
        cache       build and test caching
        environment environment variables
        filetype    file types
        go.mod      the go.mod file
        gopath      GOPATH environment variable
        gopath-get  legacy GOPATH go get
        goproxy     module proxy protocol
        importpath  import path syntax
        modules     modules, module versions, and more
        module-get  module-aware go get
        packages    package lists and patterns
        testflag    testing flags
        testfunc    testing functions

Use "go help <topic>" for more information about that topic.
```

#### (1)、go build

##### A: 基本使用

这个命令主要用于编译代码。在包的编译过程中，若有必要，会同时编译与之相关联的包。

* 普通包，执行 `go build` 之后，它不会产生任何文件。如果需要在 `$GOPATH/pkg` 下生成相应的文件, 需要执行 `go install` 。

* main 包，执行 `go build` 之后，会在当前目录下生成一个可执行文件。如果需要在 `$GOPATH/bin` 下生成相应的文件，需要执行 `go install` ，或者使用 `go build -o 路径/a.exe` 

* `go build` 命令默认会编译当前目录下的所有go文件; `go build xx.go` 可以单独编译指定的 xx.go 文件

* `go build` 编译包(package)后默认输出文件为 `包名.exe`, 我们可以指定编译输出的文件名，示例： `go build -o xxx.exe`. (默认情况是的 package 名(非 main 包)，或者是第一个源文件的文件名(main包); package 名在 Go 语言规范中指代码中 “package” 后使用的名称，此名称可以与文件夹名不同。默认生成的可执行文件名是文件夹名。)

* `go build` 会忽略目录下以 `_` 或 `.` 开头的 go 文件。(这种实际是隐藏文件)

* 如果源代码针对不同的操作系统需要不同的处理，那么可以根据不同的操作系统后缀来命名文件。比如，有一个读取数组的程序，它对于不同的操作系统可能有如下几个源文件: `array_linux.go array_darwin.go` 、`array_windows.go array_freebsd.go`. `go build` 的时候会选择性地编译以系统名结尾的文件(Linux、Darwin、Windows、Freebsd)。


##### B: 参数的介绍

* `-o` 指定输出的文件名，可以带上路径，例如 `go build -o a/b/c`
* `-i` 安装相应的包，表示：编译之后执行 `go install`
* `-a` 更新全部已经是最新的包的，但是对标准包不适用
* `-n` 把需要执行的编译命令打印出来，但是不执行，这样就可以很容易的知道底层是如何运行的 
* `-p n` 指定可以并行可运行的编译数目，默认是 CPU 数目
* `-race` 开启编译的时候自动检测数据竞争的情况，目前只支持 64 位的机器 
* `-v` 打印出来我们正在编译的包名* `-work` 打印出来编译时的临时文件夹名称，如果已经存在就不要删除 
* `-x` 打印出来执行的命令，其实就是和 -n 的结果类似，只是这个会执行
* -ccflags 'arg list' 传递参数给5c, 6c, 8c 调用
* -compiler name 指定相应的编译器，gccgo还是gc
* -gccgoflags 'arg list' 传递参数给gccgo编译连接调用
* -gcflags 'arg list' 传递参数给5g, 6g, 8g 调用
* -installsuffix suffix 为了和默认的安装包区别开来，采用这个前缀来重新安装那些依赖的包， -race 的 时候默认已经是 -installsuffix race ,大家可以通过 -n 命令来验证
* -ldflags 'flag list' 传递参数给5l, 6l, 8l 调用
* -tags 'tag list' 设置在编译的时候可以适配的那些tag，详细的tag限制参考里面的 Build Constraints

#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 4、Go 开发工具

#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

---

## 二、Go 语言基础

### 1、

#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、


### 2、

#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 3、

#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 4、

#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

---

## 三、

### 1、

#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、


### 2、

#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 3、

#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 4、

#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

---

