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


#### (2)、go clean

清除当前源码包和关联源码包内部编译生成的文件

其他内容参考原书

#### (3)、go fmt

格式化代码

其他内容参考原书

#### (4)、go get

动态获取远程代码包。

其他内容参考原书

#### (5)、go install

编译+安装

其他内容参考原书

#### (6)、go test

会自动读取源码目录下面名为 `*_test.go` 的文件

其他内容参考原书


#### (7)、go tool

`go tool` 下面下载聚集了很多命令，这里我们只介绍两个，fix和vet

* `go tool fix`  用来修复以前老版本的代码到新版本，例如 go1 之前老版本的代码转化到 go1 ,例如API的变化 
* `go tool vet directory|files` 用来分析当前目录的代码是否都是正确的代码,例如是不是调用`fmt.Printf` 里面的参数不正确，例如函数里面提前 return 了然后出现了无用代码之类的。


#### (8)、go generate

从 Go1.4 开始才设计的，用于在编译前自动化生成某类代码。 `go generate` 和 `go build` 是完全不一样 的命令，通过分析源码中特殊的注释，然后执行相应的命令。这些命令都是很明确的，没有任何的依赖在里 面。而且大家在用这个之前心里面一定要有一个理念，这个 `go generate` 是给你用的，不是给使用你这个包的人 用的，是方便你来生成一些代码的。

其他内容参考原书

#### (9)、go doc

在 Go1.2 版本之前还支持 `go doc` 命令，但是之后全部已到了 `godoc` 这个命令下，需要这样安装
`go get golang.org/x/tools/cmd/godoc`

如何查看相应 package 的文档呢? 例如 builtin 包，那么执行 `godoc builtin`, 如果是 http包，那么执行 `godoc net/http`. 查看某一个包里面的函数，执行 `godoc fmt Printf` 也可以查看相应的代码，执行 `godoc -src fmt Printf`

通过命令在终端中执行 `godoc -http=:端口号` 比如 `godoc -http=:8080` 。然后在浏览器中打开 `127.0.0.1:8080` ，你将会看到一个 golang.org 的本地 copy 版本，通过它你可以查询 pkg 文档等其它内容。如果你设置了 GOPATH，在 pkg 目录下，不但会列出标准包的文档，还会列出你本地 GOPATH 中所有项目的相关文档，这对于经常被墙的 用户来说是一个不错的选择

#### (10)、其他命令

```go
`go version` 查看go当前的版本
`go env` 查看当前go的环境变量
`go list` 列出当前全部安装的package 
`go run` 编译并运行Go程序
```


### 4、Go 开发工具

略

直接使用 Visual Studio Code 即可

---

## 二、Go 语言基础

### 1、Hello Go

#### (1)、程序

hello.go

```go
package main

import "fmt"

func main() {
	fmt.Printf("Hello GO \n")
}
```

在终端中切换到当前 hello.go 所在目录，然后输入 `go run hello.go` 之后就会在终端中输出 `Hello Go`

#### (2)、详解

讲解了包的概念、入口包 main、fmt 包等。

具体内容略

#### (3)、结论

Go 使用 package ( 和 Python 的模块类似)来组织代码。 `main.main()` 函数(这个函数位于主包)是每一个独立的可运行程序的入口点。Go 使用 `UTF-8` 字符串和标识符 (因为 `UTF-8的` 发明者也就是 Go 的发明者)，所以它天生支持多语言。

### 2、Go 基础

#### (1)、变量

* 定义单个变量： `var 变量名 变量类型`
* 同时定义多个变量：`var 变量名1, 变量名2 变量类型`
* 定义的同时赋值：`var 变量名 类型名 = 变量值`
* 同时初始化多个变量（类型推导）：`var 变量名1, 变量名2, 变量名3 = 值1, 值2, 值3`
* 短声明方式（使用了 `:=`, **只能用在函数内部**）：`变量名1, 变量名2, 变量名3 := 值1, 值2, 值3`


`_` (下划线) 是个特殊的变量名，任何赋予它的值都会被丢弃，如：`_ , b = 34, 35`，`34` 就会被丢弃，`35` 会把值赋给 b  

在 Go 中声明的变量必须被使用，否则，会在编译期报错。

#### (2)、常量

就是在程序编译阶段就确定下来，程序运行时不能被改变的值。

Go 中，常量可以有 数字、布尔值、字符串等类型

格式为：`const 常量名 = 常量值`

如果有需要也可以显示指明常量的类型：`const Pi float32 = 3.1415926`

下面时一些常量声明的例子：

```go
const Pi = 3.1415926
const i = 10000
const MaxThread = 10
const prefix = "astaxie_"
```

#### (3)、内置基础类型

##### A: bool -- 布尔类型

布尔类型用 `bool` 表示，取值 `true` 或 `false`, 默认 `false`

```go
var isActive bool // 全局变量声明
var enabled, disabled = true, false // 忽略类型的声明 

func test() {
	var available bool // 一般声明 
	available = true // 赋值操作
	
	valid := false // 简短声明 
}
```

##### B: 数值类型

**整数** 类型有无符号和带符号两种。

Go 同时支持 `int` 和 `uint` ，这两种类型的长度相同，但具体长度取决于不同编译器的实现。

Go里面也有直接定义好位数的类型: `rune , int8 , int16 , int32 , int64` 和 `byte , uint8 ,uint16 , uint32 , uint64` 。

其中 `rune` 是 `int32` 的别称， `byte` 是 `uint8` 的别称。 

需要注意的一点是，**这些类型的变量之间不允许互相赋值或操作，不然会在编译时引起编译器报错**。 

如下的代码会产生错误: `invalid operation: a + b (mismatched types int8 and int32)`

```go
   var a int8
   var b int32
   c:=a + b
```  
 
另外，尽管 int 的长度是 32 bit, 但 int 与 int32 并不可以互用。

**浮点数** 的类型有 `float32` 和 `float64` 两种(没有 float 类型)，默认是 `float64` 。

Go还支持复数。它的默认类型是 `complex128` (64位实数+64位虚数)。如果需要小一些 的，也有 `complex64` (32位实数+32位虚数)。

**复数** 的形式为 `RE + IMi` ，其中 RE 是实数部分， IM 是虚数部分，而 最后的 `i` 是虚数单位。下面是一个使用复数的例子: 
```go package main

import (
	"fmt"
)

func main() {
	var c = 5 + 6i
	fmt.Printf("c 的类型为：%T\n", c)
	fmt.Printf("c 的值为：%v\n", c)
	fmt.Println("c 的实部为：", real(c), "虚部为：", imag(c))
}
```

运行结果：

```go
[Running] go run "/Users/cnpeng/go/src/GoWeb_CN/chapter2/2_base/complex.go"
c 的类型为：complex128
c 的值为：(5+6i)
c 的实部为： 5 虚部为： 6
```

##### C: 字符串

Go中的字符串都是采用 UTF-8 字符集编码。

字符串是用一对双引号 `" "` 或 反引号 `` （反引号就是键盘左上角，英文状态下的 ~ 键）括起来定义，使用 string 表示。

```go
var frenchHello string // 声明变量为字符串的一般方法
var emptyString string = "" // 声明了一个字符串变量，初始化为空字符串 

func test() {
	no, yes, maybe := "no", "yes", "maybe" // 简短声明，同时声明多个变量 	japaneseHello := "Konichiwa" // 同上
	frenchHello = "Bonjour" // 常规赋值
	
	// 以角标的形式获取字符串中的单个字符
	s := "hello"
	fmt.Println(s[0])
}
```

在 Go 中字符串本身是不可变的 (**但字符串对应的变量的值是可以被改变的**)，例如下面的代码编译时会报错: `cannot assign to s[0]`
```govar s string = "hello"
s[0] = 'c'
```

但如果真的想要修改怎么办呢? 下面的代码可以实现:

```gos := "hello"
c := []byte(s) // 将字符串 s 转换为 []byte 类型 
c[0] = 'c'
s2 := string(c) // 再转换回 string 类型 
fmt.Printf("%s\n", s2)
```
Go中可以使用 + 操作符来连接两个字符串:

```gos := "hello,"
m := " world"
a := s + m
fmt.Printf("%s\n", a)
```

修改字符串也可写为:——字符串变量的值可以被修改：

```gos := "hello"
s = "c" + s[1:] // 字符串虽不能更改，但可进行切片操作 
fmt.Printf("%s\n", s)```可以通过 反引号 ` 来声明多行字符串: (CnPeng 一定要注意，不是单引号)

```gom := `hello
    world`
```    
 
使用反引号括起的字符串为 Raw 字符串，即字符串在代码中的形式就是打印时的形式，它没有字符转义，换行也将原样输出。比如，上面声明的 m 执行打印操作后会得到如下结果：

```go
hello
	world
```

##### D: 错误类型

Go 内置有一个 `error` 类型，专门用来处理错误信息，Go 的 `package` 里面还专门有一个`errors` 包来处理错误:

```goerr := errors.New("emit macho dwarf: elf header corrupted")
if err != nil {
    fmt.Print(err)
}
```

##### E: Go 数据底层的存储

下面这张图来源于 Russ Cox Blog 中一篇介绍 Go 数据结构 的文章，大家可以看到这些基础类型底层都是分配了一块内存，然后存储了相应的值。

![AcGtx0.png](https://s2.ax1x.com/2019/04/03/AcGtx0.png)
 

#### (4)、一些技巧

##### A: 分组声明

在 Go 语言中，同时声明多个常量、变量，或者导入多个包时，可采用分组的方式进行声明。

比如下面的代码：

```go
import "fmt"
import "os"

const i = 100
const pi = 3.1415
const prefix = "Go_"

var i int
var pi float32
var prefix string
```

可以分组写成如下形式：

```go
import(
    "fmt"
	 "os" 
)

const(
	i = 100
	pi = 3.1415
    prefix = "Go_"
)

var(
	i int
	pi float32
	prefix string
)
```

##### B: 枚举

Go 里面有一个关键字 `iota` ，这个关键字用来声明 `enum` 的时候采用，它默认开始值是 0，`const` 中每增加一行加 1:

```go
func main() {
	const (
		a = iota
		b = iota
		c = iota
		// 常量声明省略值时，默认和之前一个值的字面相同。这里隐式地说 d = iota，因此 d == 3。
		// 其实上面 b 和 c 可同样不用"= iota
		d
	)

	// 每遇到一个const关键字，iota 就会重置，此时 e == 0
	const e = iota

	const (
		//f=0,g=0,h=0 iota 在同一行值相同
		f, g, h = iota, iota, iota
	)

	const (
		i = iota
		j = "B"
		//k=2
		k = iota
		//l=3,m=3,n=3
		l, m, n = iota, iota, iota
		x       = iota
	)

	fmt.Println(a, b, c, d)
	fmt.Println(e)
	fmt.Println(f, g, h)
	fmt.Println(i, j, k, l, m, n, x)
}
```

运行结果如下：

```go
0 1 2 3
0
0 0 0
0 B 2 3 3 3 4
```

##### C: Go 程序设计的一些规则

Go之所以会那么简洁，是因为它有一些默认的行为: 

* 大写字母开头的变量表示 public，是公用变量; 小写字母开头表示私有变量。 
* 大写字母开头的函数也是一样，相当于 class 中的带 public 关键词的公有函数;小写字母开头的就是有 private 关键词的私有函数。


#### (5)、array、slice、map

##### A: array

array 就是数组，它的定义方式如下:

```govar arr [n]type```在 `arr [n]type` 中，arr 表示数组名称， n 表示数组的长度， type 表示存储元素的类型。


对数组的操作和其它语言类似，都是通过 `[]` 来 进行读取或赋值:

```go
// 声明了一个int类型的数组var arr [10]int 
// 数组下标是从0开始的
arr[0] = 42 
arr[1] = 13 

// 获取数据，返回42
fmt.Printf("The first element is %d\n", arr[0]) 
//返回未赋值的最后一个元素，默认返回0
fmt.Printf("The last element is %d\n", arr[9]) 
```
由于**长度也是数组类型的一部分**，因此 `[3]int` 与 `[4]int` 是不同的类型，数组也就不能改变长度。

**数组之间的赋值是值的赋值（值传递）**，即当把一个数组作为参数传入函数的时候，传入的其实是该数组的副本，而不是它的指针。如果要使用指针，那么就需要用到后面介绍的 slice 类型了。

也可以在声明数组的同时为元素赋值，而且也可以使用 `:=` 来声明

```go
// 声明了一个长度为3的int数组, 并为元素赋值
var a = [3]int{1, 2, 3} 
// 声明了一个长度为10的int数组，其中前三个元素初始化为1、2、3，其它默认为0 
b := [10]int{1, 2, 3} 
// 可以省略长度而采用`...`的方式，这样Go会自动根据元素个数来计算长度
c := [...]int{4, 5, 6} 
```

Go 支持嵌套数组，即多维数组。比如下面的代码 就声明了一个二维数组:

```go// 声明了一个二维数组，该数组以两个数组作为元素，其中每个数组中又有4个int类型的元素 doubleArray := [2][4]int{[4]int{1, 2, 3, 4}, [4]int{5, 6, 7, 8}}
// 上面的声明可以简化，直接忽略内部的类型, 简化后如下
easyArray := [2][4]int{{1, 2, 3, 4}, {5, 6, 7, 8}}
```

二维数组数组的映射关系如下所示:

![多维数组的映射关系](https://s2.ax1x.com/2019/04/03/AcGHzt.png)

##### B: slice

某些场景下，在初始定义数组时，我们并不知道到底需要多大的数组，因此我们就需要“动态数组”。在 Go 里面这种数据结构叫 `slice`

`slice` 并不是真正意义上的动态数组，而是一个**引用类型**。 `slice` 总是指向一个底层 `array` . `slice` 的声明也可以像 `array` 一样，只是不需要长度。

```go
// 和声明array一样，只是少了长度 
var fslice []int
```接下来我们可以声明一个 slice ，并初始化数据，如下所示:

```goslice := []byte {'a', 'b', 'c', 'd'}
```

`slice` 可以从一个数组或一个已经存在的 `slice` 中再次声明。 

`slice` 通过 `array[i:j]` 来获取，其中 i 是 开始位置， j 是结束位置，但不包含`array[j]` ，它的长度是 j-i 。

```go// 声明一个含有10个元素元素类型为byte的数组
var ar = [10]byte {'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j'}

// 声明两个含有byte的slice 
var a, b []byte

// a从数组的第3个元素开始，并到第五个元素结束。a含有的元素: ar[2]、ar[3]和ar[4]
a = ar[2:5]

// b是数组ar的另一个slice。 b的元素是:ar[3]和ar[4]
b = ar[3:5]
```
注意 slice 和数组在声明时的区别:

* 声明数组时，方括号内写明了数组的长度或使用, 使用 `...` 则会自动计算长度，
* 而声 明 slice 时，方括号内没有任何字符——不需要主动声明长度

它们的数据结构如下所示

![slice 和 array 的对应关系](https://s2.ax1x.com/2019/04/03/AcJvX6.png)

slice有一些简便的操作

* slice 的默认开始位置是 0，`ar[:n]` 等价于 `ar[0:n]`
* slice 的第二个位置默认是数组的长度，`ar[n:]` 等价于 `ar[n:len(ar)]` 
* 如果从一个数组里面直接获取 slice ，可以这样 `ar[:]` ，因为默认起始是0、结束位置是数组的长度，即 等价于 `ar[0:len(ar)]`

下面这个例子展示了更多关于 slice 的操作:

```go
// 声明一个数组
var array = [10]byte{'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j'} 

// 声明两个slice
var aSlice, bSlice []byte

// 演示一些简便操作
// 等价于aSlice = array[0:3] aSlice包含元素: a,b,c 
aSlice = array[:3] 
// 等价于aSlice = array[5:10] aSlice包含元素: f,g,h,i,j
aSlice = array[5:]  
// 等价于aSlice = array[0:10] 这样aSlice包含了全部的元素
aSlice = array[:] 

// 从slice中获取slice
// aSlice包含元素: d,e,f,g，len=4，
aSlice = array[3:7]
// bSlice 包含aSlice[1], aSlice[2] 也就是含有: e,f
bSlice = aSlice[1:3]
// bSlice 包含 aSlice[0], aSlice[1], aSlice[2] 也就是含有: d,e,f 
bSlice = aSlice[:3]
// 对slice的slice可以在cap范围内扩展，此时bSlice包含:d,e,f,g,h 
bSlice = aSlice[0:5]
// bSlice包含所有aSlice的元素: d,e,f,g
bSlice = aSlice[:]
```

slice 是引用类型，所以当引用改变其中元素的值时，其它的所有引用都会改变该值，例如上面的 aSlice 和 bSlice ，如果修改了 aSlice 中元素的值，那么 bSlice 相对应的值也会改变。

从概念上面来说 slice 像一个结构体，这个结构体包含了三个元素: 

- 一个指针，指向数组中 slice 指定的开始 位置 
- 长度，即 slice 的长度。（使用 len(slice) 可以获取 slice 的长度）
- 最大长度(容量)，也就是 slice 开始位置到数组的最后位置的长度（使用 cap(slice) 可以获取slice 的容量）```goArray_a := [10]byte{'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j'}
Slice_a := Array_a[2:5]
```
上面代码的真正存储结构如下图所示

![slice 对应数组的信息](https://s2.ax1x.com/2019/04/03/AcYPtH.png)

对于 slice 有几个有用的内置函数:

* `len(slice)` : 获取 slice 的长度
* `cap(slice)` : 获取 slice 的最大容量
* `append(slice,newElement)`: 向 slice 里面追加一个或者多个元素，然后返回一个和 slice 一样类型的 slice 
* `copy(dst,src)` : 从源 slice 中复制元素到目标 slice，并且返回复制的元素的个数. 成功拷贝的前提是 dst 的长度不能为 0
* **slice 持有数组的引用**

```govar array [10]int
aSlice := array[2:4]
```
这个例子里面 aSlice 的容量是8 (即 数组长度-起始索引)，在 1.2 之后的版本中还可以可以指定这个容量

```gobSlice = array[2:4:7]
```上面这个的容量就是 7-2 （即 最大容量-起始索引） ，即 5。这样 bSlice 就没办法访问数组的后三个元素。 

另外，还可以通过 `make([]类型，长度，容量)`、`slice := []类型 {元素1，元素2，元素3}` 的形式声明 slice 

##### C: map

map 也就是 Python 中字典的概念，它的格式为 `map[keyType]valueType`

```go	// 1、声明方式1
	var numbers map[string]int
	numbers = make(map[string]int)
	numbers["one"] = 1

	fmt.Println(numbers["one"])

	// 2、声明方式2
	numbers2 := make(map[string]int)
	numbers2["two"] = 2
	fmt.Println(numbers2["two"])

	// 3、声明方式3, 第二个参数表示创建map时请求的内存空间，len 取的是实际元素的长度
	numbers3 := make(map[string]int, 10)
	numbers3["three"] = 3
	fmt.Println(numbers3["three"])
	
	// 4、声明方式4：
	numbers4 := map[string]int{"张三":23, "李四":24}
```

使用map过程中需要注意的几点: 

- map 是无序的，它不能通过 index 获取，而必须通过 key 获取 
- 内置的 len 函 数同样适用于 map ，返回 map 拥有的 key 的数量 
- map和其他基本型别不同，它不是 thread-safe，在多个 go-routine 存取时，必须使用 mutex lock机制


```go// 初始化一个字典
rating := map[string]float32{"C":5, "Go":4.5, "Python":4.5, "C++":2 }

// 根据键取值时有两个返回值，第一个表示值，第二个表示是否存在指定的键。存在为 true, 否则为 false
csharpRating, ok := rating["C#"]

if ok {
    fmt.Println("C# is in the map and its rating is ", csharpRating)
} else {
    fmt.Println("We have no rating associated with C# in the map")
}

// 删除key为C的元素
delete(rating, "C") 
``` map 也是一种引用类型，如果两个 map 同时指向一个底层，那么一个改变，另一个也相应的改变:

```go
m := make(map[string]string)
m["Hello"] = "Bonjour"

// 现在m["hello"]的值已经是Salut了
m1 := m
m1["Hello"] = "Salut" 
```

##### E: make、new 操作

`make` 用于内建类型 ( map、slice 和 channel ) 的内存分配。   

`new` 用于各种类型的内存分配。

内建函数 `new`本质上说跟其它语言中的同名函数功能一样: 分配了零值填充的 T 类型的内存空间，并且 返回其地址，即一个 `*T`类型的值。用 Go 的术语说，它**返回了一个指针**，指向新分配的类型 T 的零值。

内建函数 `make(T,args)` 与 `new(T)` 有着不同的功能，make只能创建 `slice` 、`map`和 `channel`，并且返回一个有 初始值(非零) 的类型，而不是 `*T`。本质来讲，导致这三个类型有所不同的原因是 : 指向数据结构的引用在使用前必须被初始化。

例如，一个 `slice` ，是一个包含指向数据(内部为 `array`) 的指针、长度和容量的三项描述符 ; 在这些项目被初始化之前，`slice` 为 `nil` 。对于 `slice`、`map` 和 `channel` 来说， `make` 初始化了内部的数据结构，并填充适当的值。


#### (6)、零值——默认值

`零值”`，并非是空值，而是一种 **变量未填充前的默认值**，通常为 0。 此处罗列部分类型的 零值：

```go
int 0
int8 0
int32 0
int64 0
uint 0x0
rune 0 //rune的实际类型是 int32 
byte 0x0 // byte的实际类型是 uint8 
float32 0 //长度为 4 byte
float64 0 //长度为 8 byte bool false
string ""
```

### 3、流程和函数

#### (1)、流程控制

Go 中流程控制分三大类:条件判断，循环控制和无条件跳转。

##### A: if

Go 里面 if 条件判断语句中不需要括号

```go
if x > 10 {
    fmt.Println("x is greater than 10")
} else {
    fmt.Println("x is less than 10")
}
```

Go 的 if 还有一个强大的地方就是 : 允许在条件判断语句中声明一个变量，这个变量的作用域只能在该条件逻辑块内:

```go
// 计算获取值x,然后根据x返回的大小，判断是否大于10。 
if x := computedValue(); x > 10 {
    fmt.Println("x is greater than 10")
} else {
    fmt.Println("x is less than 10")
}
//这个地方如果这样调用就编译出错了，因为x是条件里面的变量 , 此处已经超出了 x 的作用域
//fmt.Println(x)
```

多个条件时如下：

```go
if integer == 3 {
    fmt.Println("The integer is equal to 3")
} else if integer < 3 {
    fmt.Println("The integer is less than 3")
} else {
    fmt.Println("The integer is greater than 3")
}
```

##### B: goto

* `goto 标签名` 跳转到指定 标签名 的代码处。执行完之后会退出当前代码块
* 可以应用于任何位置，循环中也可以使用
* 标签名区分大小写

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("1111")

	goto PrintThree

	// 此处不会执行
	fmt.Println("2222")

PrintThree:
	fmt.Println("3333")
}
```

运行结果：

```go
1111
3333
```

##### C: for

基本使用：

```go
package main

import (
	"fmt"
)

func main() {
	sum := 0

	for x := 0; x < 10; x++ {
		sum += x
	}

	// 输出 45
	fmt.Println(sum)
}
```

在循环里面有两个关键操作 `break` 和 `continue` : `break` 操作是跳出当前循环，`continue`是跳过本次循环。

```go
func main() {

	for x := 10; x > 0; x-- {
		if x == 5 {
			break
		}
		fmt.Print(x, "、")
	}

	fmt.Println("")
	for y := 10; y > 0; y-- {
		if y == 5 {
			continue
		}
		fmt.Print(y, "、")
	}
}
```

运行结果：

```go
10、9、8、7、6、
10、9、8、7、6、4、3、2、1、
```

当嵌套过深的时候， `break` 可以配合标签使用，即跳转至标签所指定的位置:

```go
func main() {

out:
	for x := 10; x > 0; x-- {
		if x == 5 {
			continue
		}

		for y := 10; y > 0; y-- {
			if y == 5 {
				break out
			}
			fmt.Println("x =", x, ", y = ", y)
		}
	}
}
```

运行结果：

```go
x = 10 , y =  10
x = 10 , y =  9
x = 10 , y =  8
x = 10 , y =  7
x = 10 , y =  6
```

for 配合 range 可以用于读取 slice 和 map 的数据:

```go
func main() {

	uInfoMap := map[string]string{"姓名": "张三", "年龄": "23"}

	// 注意此处使用了 :=
	for k, v := range uInfoMap {
		fmt.Printf("key = %s, value = %s \n", k, v)
	}
}
```

运行结果：

```go
key = 姓名, value = 张三 
key = 年龄, value = 23 
```

由于 Go 支持 多值返回, 而对于 声明而未被调用 的变量, 编译器会报错, 在这种情况下, 可以使用 `_` 来丢弃不需要的返回值：

```go
func main() {

	uInfoMap := map[string]string{"姓名": "张三", "年龄": "23"}

	// 注意此处使用了 :=
	for _, v := range uInfoMap {
		fmt.Printf("value = %s \n", v)
	}
}
```

##### D: switch

该节内容摘自 [20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md) 中的 switch 

* switch 语句的每一个 case中，可以省略 break 关键字——默认具有 break 效果
* 使用 fallthrough 表示穿透

（上述两个特点 和 Swift 中的效果一致）

```go
package main

import "fmt"

func main() {

	num := 5
	switch num {
	case 1:
		fmt.Println("输入的是1")
		//末尾的break可以省略
	case 5:
		fmt.Println("输入的是5")
		//fallthrough 表示穿透
		fallthrough
	default:
		fmt.Println("输入的是：", num)
	}
}
```

运行结果：

```go
输入的是5
输入的是： 5
```

* switch 语句也支持初始化一个变量
* case 后面可以跟多个常量，使用 逗号 间隔
* 当 case 后面跟的是表达式时，可以省略 switch 后面的条件。

```go
package main

import "fmt"

func main() {

	// 1、switch 支持初始化一个变量
	switch num := 5; num {
	case 1, 2 , 3 :
		fmt.Println("输入的是1 或 2 或 3 ")
		//末尾的break可以省略
	case 5:
		fmt.Println("输入的是5")
		//fallthrough 表示穿透
		fallthrough
	default:
		fmt.Println("输入的是：", num)
	}

	// 2、如果 case 分支是表达式，可以在 switch 后面省略条件
	score := 99
	switch {
	case score > 90:
		fmt.Println("成绩为：", score, "，优")
	case score > 60:
		fmt.Println("成绩为：", score, ", 及格")
	default:
		fmt.Println("成绩为：", score, ", 不及格")
	}
}
```

运行结果：

```go
输入的是5
输入的是： 5
成绩为： 99 ，优 
```

#### (2)、函数

函数基本格式：

```gofunc funcName(input1 type1, input2 type2) (output1 type1, output2 type2) { 

	//这里是处理逻辑代码
	//可以返回多个值
	return value1, value2
}
```
下面我们来看一个实际应用函数的例子(用来计算Max值)

```go
package main

import "fmt"

// 返回a、b中最大值.
func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}

func main() {
	x, y, z := 3, 4, 5

	maxXy := max(x, y)
	maxXz := max(x, z)
	fmt.Printf("max(%d, %d) = %d\n", x, y, maxXy)
	fmt.Printf("max(%d, %d) = %d\n", x, z, maxXz)
	fmt.Printf("max(%d, %d) = %d\n", y, z, max(y, z))
}
```

##### A: 多个返回值

Go 中的函数允许返回多个值

```go
package main

import "fmt"

//返回值中： 第一个为 max , 第二个为min
func compareNum(a, b int) (int, int) {
	if a > b {
		return a, b
	}
	return b, a
}

func main() {
	max, min := compareNum(3, 5)
	fmt.Println(max, min)
}
```

可以给返回值定义变量——为返回值命名 (推荐这么写)

```go
package main

import "fmt"

//返回值中： 第一个为 max , 第二个为min
func compareNum(a, b int) (max, min int) {
	if a > b {
		max, min = a, b
	}

	max, min = b, a

	// 注意，此处直接写 return 就可以了，后面不需要追加 max ,min
	return
}

func main() {
	max, min := compareNum(3, 5)
	fmt.Println(max, min)
}
```

##### B: 可变参数

该节内容摘自 [20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md) 

* `...类型` 表示不定参数，可以传递不定数量（0个或多个）的 指定类型的数据
* 可变参数只能作为函数的最后一个参数
* 可变参数本质上是一个 slice

```go
package main
import "fmt"

func main() {
	// 可变参数可以传0个
	myFunc1(1)
	// 可变参数也可以传多个
	myFunc1(1, 2)
	myFunc1(1, 2, 3, 4, 5)
}

// 定义可变参数函数；多个参数时，可变参数必须作为最后一个
func myFunc1(a int, args ...int) {
	argsLen := len(args)
	fmt.Println("可变参数的长度为: ", argsLen)

	for i, data := range args {
		fmt.Printf("args[%d]=%d \n", i, data)
	}
}
```

* 把可变参数传递给另一个可变参数的函数时，有两种情况：传递部分参数，传递全部参数。

```go
package main
import "fmt"

func main() {
	myFunc1(1, 2, 3, 4, 5)
}

func myFunc(args ...int) {
	argsLen := len(args)
	fmt.Println("可变参数的长度为: ", argsLen)

	for i, data := range args {
		fmt.Printf("args[%d]=%d \n", i, data)
	}
}

func myFunc1(args1 ...int) {
	// 1、传递全部参数，参数后面追加 三个点(...)
	myFunc(args1...)

	// 2、传递部分参数. args1[:2] 表示取到索引为2（但不包含2）的元素，即 args[0],args[1]
	myFunc(args1[:2]...)

	// 3、传递部分参数. args[2:] 表示从索引为2的元素开始，取到最后一个
	myFunc(args1[2:]...)
}
```
##### C: 传值与传指针

传递参数给函数时，默认是值拷贝（值传递）:

```go
package main

import "fmt"

func add1(a int) int {
	a = a + 1 
	return a  
}

func main() {
	x := 3
	// 输出 3
	fmt.Println("x = ", x)

	x1 := add1(x)
	// 输出 4
	fmt.Println("x+1 = ", x1)

	// 依旧输出 3
	fmt.Println("x = ", x)
}
```

指针就是指变量在内存中的存储地址。示例：

```go
package main

import "fmt"

// *int 表示 int 类型的指针
func add1(a *int) int {

	// *a 表示获取指针对应的值
	*a = *a + 1
	return *a
}

func main() {
	x := 3
	// 输出 3
	fmt.Println("x = ", x)

	// &x 表示获取 x 的指针
	x1 := add1(&x)
	// 输出 4
	fmt.Println("x+1 = ", x1)

	// 输出 4 —— 因为，add1 中传递了指针
	fmt.Println("x = ", x)
}
```

指针传递（指针拷贝）的优点:

* 传指针使得多个函数能操作同一个对象。
* 传指针比较轻量级 (8bytes), 只是传内存地址，我们可以用指针传递体积大的结构体。如果用参数值传递的话, 在每次 copy 上面就会花费相对较多的系统开销(内存和时间)。
* Go 语言中 channel ， slice ， map 这三种类型的实现机制类似指针，所以可以直接传递，而不用取地址后传递指针。(注:**若函数需改变 slice 的长度，则仍需要取地址传递指针**)


##### D: defer

该节内容摘自 [20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

* 被 defer 修饰的内容，定义在函数内，并且只会在函数将要结束时调用（也就是：先调用没有 defer 的语句，最后调用被 defer 修饰的语句）

```go
package main

import "fmt"

func main() {
	defer fmt.Println("aaaaaaaa")
	fmt.Println("bbbbbb")
}
```

运行结果：

```go
bbbbbb
aaaaaaaa
```


* 多个defer的执行顺序 : 先进后出。

函数运行过程中遇见 defer 修饰的内容之后，会把这些语句暂存到内存中，等其他非 defer 语句执行完毕之后，再按照 先进后出 的顺序依次执行（这其实就是一个进栈和出栈的操作）。

```go
package main

import "fmt"

func main() {
	defer fmt.Println("aaaaaaaa")
	defer fmt.Println("bbbbbb")
	defer fmt.Println("cccccc")

	fmt.Println("没有被defer修饰的普通语句")
}
```

运行结果：

```go
没有被defer修饰的普通语句
cccccc
bbbbbb
aaaaaaaa
```

* 如果程序中的某处可能会出现异常，那么定义在异常前面的 defer 会被调用。

定义在异常后面的不会被调用，因为定义在异常后面的内容还没有进栈操作，所以不会出栈。

下面的示例代码中，执行 main 函数时，读取到前两个 defer 时会先暂存到栈中，遇到 calc(2,0) 时出现异常，此时 main 函数将要结束，就会按照出栈顺序执行暂存在内存中的 defer。打印错误日志的操作是在函数结束之后。而第三个 defer 没有入栈，所以函数将要结束时并不会调用它。

```go
package main
import "fmt"

func main() {
	defer fmt.Println("aaaaaaaa")
	defer fmt.Println("bbbbbb")
	calc(2, 0)
	defer fmt.Println("cccccc")
}

func calc(a, b int) {
	fmt.Println(a / b)
}
```

运行结果：

```
bbbbbb
aaaaaaaa
panic: runtime error: integer divide by zero

goroutine 1 [running]:
main.calc(0x2, 0x0)
        /Users/cnpeng/CnPeng/04_Demos/096_Go/ItCast/Day1.go:13 +0xac
main.main()
        /Users/cnpeng/CnPeng/04_Demos/096_Go/ItCast/Day1.go:8 +0xef
exit status 2
```

* defer和匿名函数结合使用

```go
package main

import "fmt"

func main() {
	a := 10

	// 读取到这里时 a 的值为10，然后传递给了arg 。 a=20 将不会影响到这里
	defer func(arg int) {
		// 外部传入的 a 赋值给 arg.
		fmt.Println("A: arg = ", arg)
	}(a)

	// 读取到这里时，只是暂存函数到内存，还没开始引用 a 。只有执行时才会去引用 a
	defer func() {
		// 直接引用外部的 a
		fmt.Println("B: a = ", a)
	}()

	a = 20
	fmt.Println("C: a = ", a)
}
```

运行结果：

```go
cnpeng$ go run Day1.go 
C: a =  20
B: a =  20
A: arg =  10
```
##### E: 函数类型

该节内容摘自 [20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)


* 函数也是一种数据类型，其格式为：`func (参数1类型，参数2类型) 返回值类型` 
* 可以通过 type 关键字为函数起别名
* 为函数类型的变量赋值时，不需要后缀小括号

```go
package main

import "fmt"

func PlusFunc(a, b int) (result int) {
	result = a + b
	return
}

func minus(a int, b int) int {
	return a - b
}

// `func(int,int) int` 表示一个接受两个int值并返回一个int值的函数类型
// 为该函数类型定义别名为 MyFuncType
type MyFuncType func(int, int) int

func main() {
	var myFunc MyFuncType
	// 为函数类型变量赋值时不需要后缀小括号
	myFunc = PlusFunc
	result1 := myFunc(1, 2)

	myFunc = minus
	result2 := myFunc(6, 2)

	fmt.Printf("result1 = %d ,result2 = %d \n", result1, result2)
}
```

运行结果：

```go
result1 = 3 ,result2 = 4
```


##### F: Panic 和 Recover

该节内容摘自 [20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

Go 没有像 Java 那样的异常机制，它不能抛出异常，而是使用了 panic 和 recover 机制。但是，我们在代码中必须尽量少的使用 panic 。

通常情况下，我们可以通过返回一个 error 类型的实例把错误信息暴露出来。

但是，如果遇到不可恢复的错误（比如：数组越界、空指针等）时，就会引发 panic 异常，此时，我们就应该使用 panic 把异常信息暴露出去。

当 panic 异常发生时，程序通常会中断运行，并立即执行在该 goroutine (可以理解成线程) 中被延迟的函数（ 被 defer 修饰的内容 ）。随后，程序崩溃并输出日志信息。日志信息包括 panic value 和 函数调用的堆栈跟踪信息。

不是所有的 panic 异常都来自于运行时，直接调用内置的 panic 函数也会引发 panic 异常， panic 函数接收任意类型数据作为参数：

```go
func panic(v interface{})
```

示例代码：

```go
package main

import (
	"fmt"
)

func test1() {
	fmt.Println("aaaaaa")
}

func test2() {
	panic("人为触发 panic ")
}
func test3() {
	fmt.Println("cccccc")
}
func main() {
	test1()
	test2()
	test3()
}
```

运行结果：

```
cnpeng$ go run day5.go 
aaaaaa
panic: 人为触发 panic 

goroutine 1 [running]:
main.test2(...)
        /Users/cnpeng/go/src/day5/day5.go:12
main.main()
        /Users/cnpeng/go/src/day5/day5.go:19 +0x3e
exit status 2
```

数组越界导致 panic :

```go
package main

import (
	"fmt"
)

func test1() {
	fmt.Println("aaaaaa")
}

func test2(x int) {
	// 定义一个长度为10的int数组
	var arr [10]int
	arr[x] = 666
}
func test3() {
	fmt.Println("cccccc")
}
func main() {
	test1()
	test2(20)
	test3()
}
```

运行结果：

```
cnpeng$ go run day5.go 
aaaaaa
panic: runtime error: index out of range

goroutine 1 [running]:
main.test2(...)
        /Users/cnpeng/go/src/day5/day5.go:14
main.main()
        /Users/cnpeng/go/src/day5/day5.go:21 +0x25
exit status 2
```

* recover 的使用

运行时 panic 异常一旦被引发就会导致程序崩溃。而 recover 函数 则可以使当前程序从 panic 的状态中恢复并重新获取流程控制权。

**注意：recover 只在 defer 调用的函数中有效**

如果  defer 函数 中调用了内置函数 recover , 当该 defer 函数发生 panic 异常时，recover 会使程序从 panic 中恢复而不是直接崩溃，并且 recover 函数会返回 panic value —— 也就是异常信息，如果没有发生 panic ，那么 recover 函数将返回 nil

```go
package main

import (
	"fmt"
)

func test1() {
	fmt.Println("aaaaaa")
}

func test2(x int) {
	defer func() {
		// 只有 panic 发生时，err 才不为 nil ,才执行后面的打印
		if err := recover(); nil != err {
			fmt.Println(err)
		}
	}()

	// 定义一个长度为10的int数组
	var arr [10]int
	arr[x] = 666
}
func test3() {
	fmt.Println("cccccc")
}
func main() {
	test1()
	test2(20)
	test3()
}
```

运行结果：

```
cnpeng$ go run day5.go 
aaaaaa
runtime error: index out of range
cccccc
```


##### G: main 函数 和 init 函数

Go 里面有两个保留的函数：`init`函数（能够应用于所有的 `package` ）和 `main` 函数（只能应用于`package main`）。这两个函数在定义时不能有任何的参数和返回值。

虽然一个 `package` 里面可以写任意多个 `init` 函数，但从可读性、可维护性来说，建议在一个`package` 中每个文件只写一个 `init` 函数。

Go 程序会自动调用 `init()` 和 `main()`，所以你不需要在任何地方调用这两个函数。每个 `package` 中的 `init` 函数都是可选的，但 `package main` 就必须包含一个 `main` 函数。

程序的初始化和执行都起始于 `main` 包。如果 `main` 包还导入了其它的包，那么就会在编译时将它们依次导入。有时一个包会被多个包同时导入，那么它只会被导入一次（例如很多包可能都会用到 `fmt` 包，但它只会被导入一次，因为没有必要导入多次）。当一个包被导入时，如果该包还导入了其它的包，那么会先将其它包导入进来，然后再对这些包中的包级常量和变量进行初始化，接着执行 `init` 函数（如果有的话），依次类推。等所有被导入的包都加载完毕了，就会开始对 `main` 包中的包级常量和变量进行初始化，然后执行 `main` 包中的 `init` 函数（如果存在的话），最后执行 `main` 函数。

 ![main函数引入包初始化流程图](https://images.gitee.com/uploads/images/2019/0215/225841_f7a05a22_930142.png "屏幕截图.png")

##### H: import

* 使用 `package 包名` 在 .go 文件的第一行声明该文件的包名
* 使用 `import ”包名“` 导入其他包，导入某个包之后必须使用，否则不能编译
* 导入多个包时，可以使用下列方式：

```go
import(
	"fmt"
	"os"
)
```

* 导包时可以给包起别名，然后就可以通过别名调用其中的成员。格式：`import 别名 "包名"`

* 还可以忽略包，格式：`import _ "包名" `，表示只单纯的调用该包的 init() 函数，不调用其他内容。

* 导入包时也可以使用 `import . "fmt"` 的形式，然后引用 Printf() 时就不用写前缀 fmt. , 但不推荐使用这种方式，容易导致混乱
* 

### 4、struct 结构体

直接参考：[20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

### 5、面向对象

#### (1)、method 方法

直接参考：[20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md) 中的 《方法介绍》


### 6、interface 接口

**空 interface (`interface{}`)** 不包含任何的 method，正因为如此，所有的类型都实现了空interface。

空 interface 对于描述起不到任何的作用(因为它不包含任何的 method )，但是 空 interface 在我们需要存储任意类型的数值时相当有用，因为它可以存储任意类型的数值。它有点类似于 C 语言的 `void*`类型。

```go// 定义a为空接口
var a interface{}
var i int = 5
s := "Hello world"
// a可以存储任意类型的数值 
a= i
a= s
```
一个函数把 `interface{}` 作为参数，那么他可以接受任意类型的值作为参数; 如果一个函数返回`interface{}`,那么 也就可以返回任意类型的值。


其他内容参考：[20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

### 7、并发

#### (1)、goroutine

参考：[20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

#### (2)、channels

参考：[20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

#### (3)、Buffered Channels

参考：[20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

#### (4)、Range 和 Close

参考：[20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

#### (5)、Select

参考：[20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

#### (6)、超时

参考：[20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

#### (7)、runtime goroutine

参考：[20小时入门学会go语言(黑马)](./01_20小时入门学会go语言(黑马).md)

---

## 三、

### 1、Web 工作方式

普通上网流程的流程原理：

* 输入 网页地址 ，并点击回车键
* 浏览器（客户端）去 请求 DNS 服务器，获取地址对应的 IP 
* 通过 IP 获取 IP 服务器，并建立 TCP 链接
* 浏览器发送 HTTP Request 包到 服务器
* 服务器接收并调用自身服务处理请求包，
* 服务器返回 HTTP Response 包给浏览器（客户端）
* 客户端收到响应包后开始渲染内容
* 客户端接收到全部响应包之后，断开与服务器的 TCP 链接

![用户访问 Web 的原理过程](https://s2.ax1x.com/2019/04/08/A5uU0K.png)

Web 服务器也被称为 HTTP 服务器。


#### (1)、URL 和 DNS 解析

##### A : URL 
URL(Uniform Resource Locator) 统一资源定位符

基本格式为：

```go
scheme://host[:port#]/path/.../[?query-string][#anchor]
```

节点|含义
---|---
scheme      | 指定底层使用的协议(例如：http, https, ftp)
host        | HTTP 服务器的 IP 地址或者域名
port#       | HTTP 服务器的默认端口是80，这种情况下端口号可以省略。<br>如果使用了别的端口，必须指明，例如 http://www.cnblogs.com:8080/
path        |访问资源的路径
query-string| 发送给 http 服务器的数据
anchor      | 锚

##### B: DNS

DNS (Domain Name System) 域名系统, 将主机名或域名转换为实际 IP 地址.

![DNS 工作原理](https://s2.ax1x.com/2019/04/08/A5KngA.png)

![DNS 解析的完整流程](https://s2.ax1x.com/2019/04/08/A5KNgs.png)


#### (2)、HTTP 协议详解

##### A: HTTP 请求包（浏览器信息）

包含三个部分：

* 请求行（ Request Line ）
* 请求头 ( Request Header)
* 请求体 ( Body )

Header 和 Body 之间会有一个空行做间隔使用。

```go
GET /domains/example/ HTTP/1.1		//请求行: 请求方法 请求URI HTTP协议/协议版本
Host：www.iana.org				//服务端的主机名
User-Agent：Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.4 (KHTML, like Gecko) Chrome/22.0.1229.94 Safari/537.4			//浏览器信息
Accept：text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8 //客户端能接收的mine
Accept-Encoding：gzip,deflate,sdch		//是否支持流压缩
Accept-Charset：UTF-8,*;q=0.5		//客户端字符编码集
	//空行,用于分割请求头和消息体
//消息体,请求资源参数,例如POST传递的参数
```

HTTP 与服务器交互的常用四种方式：GET、POST、PUT、DELETE，依次对应 查、改、增、删 四种操作。

GET 与 POST 的区别：

* GET 请求体为空，POST 请求体不为空
* GET 提交数据时，数据放在 URL 之后，以 `?` 分割 URL 和 传输数据，参数之间以 `&` 链接，如：`EditPosts.aspx?name=test1&id=12345`. POST 则把数据放在 请求体 中
* GET 提交的数据有大小限制（因为浏览器对 URL 的长度有限制）；POST 提交的数据没有限制
* GET 会有安全隐患

##### B: HTTP 响应包（服务器信息）

```go
HTTP/1.1 200 OK                     //状态行: HTTP 协议版本、状态码、状态描述
Server: nginx/1.0.8                 //服务器使用的WEB软件名及版本
Date:Date: Tue, 30 Oct 2012 04:14:25 GMT        //发送时间
Content-Type: text/html             //服务器发送信息的类型
Transfer-Encoding: chunked          //表示发送HTTP包是分段发的
Connection: keep-alive              //保持连接状态
Content-Length: 90                  //主体内容长度
//空行 用来分割消息头和主体
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"... //消息体
```

Http 状态码的类别：（状态码由三位数字组成，首尾决定了类别）

格式|类别|含义
---|---|---
1XX | 提示信息 | 表示请求已被成功接收，继续处理
2XX | 成功 	| 表示请求已被成功接收，并处理成功
3XX | 重定向 	| 要完成请求必须进行更进一步的处理
4XX | 客户端错误 | 请求有语法错误或请求无法实现
5XX | 服务器端错误 | 服务器未能实现合法的请求

##### C: HTTP 协议是无状态的

HTTP 协议是无状态的，也就是说，先后打开的两个位于服务器的网页之间没有任何关系。

HTTP/1.1起，默认开启 `Keep-Alive`——保持连接。即，打开网页后，客户端和服务器之间的 TCP 链接不会立即断开；但是，该链接也只会持续一定时间。

#### (3)、请求实例

* 客户端向服务器发起请求后，服务器端返回 html 页面，然后浏览器开始渲染 HTML ：
* 当解析到 HTML DOM 里面的图片连接，css 脚本和 js 脚本的链接时，浏览器会自动发起获取静态资源的 HTTP 请求，获取到所需态资源后，浏览器就会渲染出来，最终将所有资源整合、渲染，完整展现在我们面前的屏幕上。

>网页优化方面有一项措施是减少HTTP请求次数，就是把尽量多的 css 和 js 资源合并在一起，目的是尽量减少网页请求静态资源的次数，提高网页加载速度，同时减缓服务器的压力。

### 2、用 Go 搭建一个 Web 服务器

Go 语言提供了 net/http 包

该包可以很方便的搭建可以运行的Web服务。也可以对 Web 的路由、静态文件、模版、cookie 等数据进行设置和操作。

```go
package main

import (
	"fmt"
	"log"
	"net/http"
	"strings"
)

func sayHello(writer http.ResponseWriter, request *http.Request) {
	// 解析请求体中的参数，默认不解析
	request.ParseForm()
	fmt.Println(request.Form)
	fmt.Println("path : ", request.URL.Path)
	fmt.Println("scheme : ", request.URL.Scheme)
	fmt.Println(request.Form["url_long"])

	for k, v := range request.Form {
		fmt.Println("KEY:", k)
		fmt.Println("VAL:", strings.Join(v, ""))
	}

	// 写出数据到客户端
	fmt.Fprintf(writer, "Hello GO! GO! GO !")
}

func main() {
	// 设置访问的路由及路由对应的处理函数
	http.HandleFunc("/", sayHello)
	// 设置监听端口
	err := http.ListenAndServe(":9090", nil)

	if nil != err {
		log.Fatal("ListenAndServe:", err)
	}
}
```

将上述代码 `build` 并运行对应的 `.exe` 程序（在 VisualCode Studio 中只要点击运行按钮即可）

在浏览器地址栏中输入 `http://localhost:9090` 并回车后，在服务端（VisualCode Studio 中输输出界面）中会得到如下内容：

```go
map[]
path :  /
scheme :  
[]
map[]
path :  /favicon.ico
scheme :  
[]
```

而输入 `http://localhost:9090/?url_long=111&url_long=222` 时，则会在服务端得到如下内容：

```go
[Running] go run "/Users/cnpeng/go/src/GoWeb_CN/chapter3/3_2/FirstWebServer.go"
map[url_long:[111 222]]
path :  /
scheme :  
[111 222]
KEY: url_long
VAL: 111222
map[]
path :  /favicon.ico
scheme :  
[]
```

上述示例，就是一个简易的 Web 服务器。不论上面的哪个地址，都会在客户端（浏览器）中显示我们想要的 ”Hello GO! GO! GO !“

### 3、Go 如何使得 Web 工作

#### (1)、Web 工作方式的几个概念

* Request：用户请求的信息，用来解析用户的请求信息，包括 post、get、cookie、url等信息
* Response：服务器需要反馈给客户端的信息
* Conn：用户的每次请求链接
* Handler：处理请求和生成返回信息的处理逻辑

#### (2)、分析 HTTP 包运行机制

![Go 实现 Web 服务的流程示意](https://s2.ax1x.com/2019/04/08/A5l6PS.png)

上图流程如下：

* 创建 Listen Socket , 监听指定的端口, 等待客户端请求到来。
* Listen Socket 接受客户端的请求, 得到 Client Socket, 接下来通过 Client Socket 与客户端通信。
* 处理客户端的请求, 首先从 Client Socket 读取 HTTP 请求的协议头, 如果是 POST 方法, 还可能要读取客户端提交的数据, 然后交给相应的 handler 处理请求, handler 处理完毕准备好客户端需要的数据, 通过 Client Socket 写给客户端。

上述过程总结起来为如下三点：

- 如何监听端口？
- 如何接收客户端请求？
- 如何分配handler？（即 如何处理并返回数据给客户端）

##### A: 如何监听端口？

前面小节的代码里面我们可以看到，Go 是通过 `ListenAndServe` 函数来监听端口，其内部：初始化一个 server 对象，然后调用 `net.Listen("tcp", addr)` ，也就是底层用 TCP 协议搭建了一个服务，然后监控我们设置的端口。对应源码如下：

```go

// http.server()
func ListenAndServe(addr string, handler Handler) error {
	server := &Server{Addr: addr, Handler: handler}
	return server.ListenAndServe()
}


// server.ListenAndServe()：由 net.Listen() 监听，由 srv.Serve 接收并处理请求
func (srv *Server) ListenAndServe() error {
	if srv.shuttingDown() {
		return ErrServerClosed
	}
	addr := srv.Addr
	if addr == "" {
		addr = ":http"
	}
	ln, err := net.Listen("tcp", addr)
	if err != nil {
		return err
	}
	return srv.Serve(tcpKeepAliveListener{ln.(*net.TCPListener)})
}
```

##### B: 接收客户端请求

```go
// srv.Serve()：遍历接收到的请求，并在协程中处理这些请求
func (srv *Server) Serve(l net.Listener) error {
	if fn := testHookServerServe; fn != nil {
		fn(srv, l) // call hook with unwrapped listener
	}

	l = &onceCloseListener{Listener: l}
	defer l.Close()

	if err := srv.setupHTTP2_Serve(); err != nil {
		return err
	}

	if !srv.trackListener(&l, true) {
		return ErrServerClosed
	}
	defer srv.trackListener(&l, false)

	var tempDelay time.Duration     // how long to sleep on accept failure
	baseCtx := context.Background() // base is always background, per Issue 16220
	ctx := context.WithValue(baseCtx, ServerContextKey, srv)
	for {
		rw, e := l.Accept()
		if e != nil {
			select {
			case <-srv.getDoneChan():
				return ErrServerClosed
			default:
			}
			if ne, ok := e.(net.Error); ok && ne.Temporary() {
				if tempDelay == 0 {
					tempDelay = 5 * time.Millisecond
				} else {
					tempDelay *= 2
				}
				if max := 1 * time.Second; tempDelay > max {
					tempDelay = max
				}
				srv.logf("http: Accept error: %v; retrying in %v", e, tempDelay)
				time.Sleep(tempDelay)
				continue
			}
			return e
		}
		tempDelay = 0
		c := srv.newConn(rw)
		c.setState(c.rwc, StateNew) // before Serve can return
		go c.serve(ctx)
	}
}
```

上述函数内创建了 `for{}`，在循环内部通过 Listener 的 `Accept()` 接收请求，然后为该请求创建 Conn—— `srv.newConn(rw)`，最后在 goroutine 中处理请求—— `go c.serve()`。这体现了高并发，用户的每一次请求都是在一个新的 goroutine 去处理，相互不影响。

##### C: 处理请求并返回数据给客户端

```go
// Serve a new connection.
func (c *conn) serve(ctx context.Context) {
	c.remoteAddr = c.rwc.RemoteAddr().String()
	ctx = context.WithValue(ctx, LocalAddrContextKey, c.rwc.LocalAddr())
	
	// ... 省略代码
	
	// HTTP/1.x from here on.

	ctx, cancelCtx := context.WithCancel(ctx)
	c.cancelCtx = cancelCtx
	defer cancelCtx()

	c.r = &connReader{conn: c}
	c.bufr = newBufioReader(c.r)
	c.bufw = newBufioWriterSize(checkConnErrorWriter{c}, 4<<10)

	for {
		// 此处开始读取请求
		w, err := c.readRequest(ctx)
		if c.r.remain != c.server.initialReadLimitSize() {
			// If we read any bytes off the wire, we're active.
			c.setState(c.rwc, StateActive)
		}
		if err != nil {
			//... 此处为错误处理
		}

		// ... 省略内容

		// HTTP cannot have multiple simultaneous active requests.[*]
		// Until the server replies to this request, it can't read another,
		// so we might as well run the handler in this goroutine.
		// [*] Not strictly true: HTTP pipelining. We could let them all process
		// in parallel even if their responses need to be serialized.
		// But we're not going to implement HTTP pipelining because it
		// was never deployed in the wild and the answer is HTTP/2.
		// 处理和响应。 w 的类型为：* response, w.req 得到的是 * Request
		serverHandler{c.server}.ServeHTTP(w, w.req)
		w.cancelCtx()
		
		//... 省略其他代码
	}
}

// 将请求和响应数据再返回给 我们在 http.ListenAndServe() 中传递的 handler——第二个参数
// 该 handler 需要实现 Handler 接口，但一般情况下我们都是直接传递 nil
func (sh serverHandler) ServeHTTP(rw ResponseWriter, req *Request) {
	// 获取 handler , 
	handler := sh.srv.Handler
	if handler == nil {
		handler = DefaultServeMux
	}
	if req.RequestURI == "*" && req.Method == "OPTIONS" {
		handler = globalOptionsHandler{}
	}
	handler.ServeHTTP(rw, req)
}
```

上述示例代码中：

在 for 循环中先通过 `c.readRequest()` 读取 conn 中的 Request, 然后通过 `serverHandler{c.server}` 获取 serveHandler 结构体，并调用其 `ServeHTTP(w, w.req)` 方法。

在 `ServeHTTP(w, w.req)` 方法中，会通过 `handler := sh.srv.Handler` 获取一个 handler —— 也就是我们在调用函数 `http.ListenAndServe()` 时传递的第二个参数。当该参数为 nil 时，默认为 `handler = DefaultServeMux`。

该 handler 就是一个路由器，它用来匹配 url 跳转到的对应 handle 函数——也就是我们调用的 `http.HandleFunc("/", sayHello)`。该函数的作用就是注册了请求 `/` 时的路由规则，当请求 uri 为 `/`，路由就会转到函数 `sayHello`. 当handler 为 DefaultServeMux 时，`handler.ServeHTTP(rw, req)` 其实就是调用 `sayHello` 本身，最后通过写入 response 的信息反馈到客户端。

![一个 HTTP 链接的处理流程示意图](https://s2.ax1x.com/2019/04/09/A5fJmj.png)

### 4、Go 的 Http 详解

Go 的 http 有两个核心功能：Conn、ServeMux

#### (1)、Conn 的 goroutine

Go 为了实现高并发和高性能, 使用了 goroutines 来处理 Conn 的读写事件, 这样每个请求都能保持独立，相互不会阻塞，可以高效的响应网络事件。

Go 在等待客户端请求里面是这样写的：

```go
// srv.Serve()：遍历接收到的请求，并在协程中处理这些请求
func (srv *Server) Serve(l net.Listener) error {
	for {
		rw, e := l.Accept()
		//... 其他内容省略
	
		c, err := srv.newConn(rw)
		if err != nil {
			continue
		}
		go c.serve()
	}
}
```
上述代码中，服务器会为客户端的每次请求单独创建一个 Conn ，这个 Conn 内保存了该次请求的信息，然后再传递到对应的 handler，该 handler 中便可以读取到相应的 header 信息，这样保证了每个请求的独立性。

#### (2)、ServeMux 的自定义

前面我们已经知道当 `http.ListenAndServe(":9090",nil)` 中第二个参数为 nil 时，底层的 `conn.server` 最终会调用 http 包默认的路由——DefaultServeMux，通过该路由把本次请求的信息传递给后端的处理函数。


##### A: serverHandler 中的 ServeHTTP()

在 `conn.server` 中会调用 `serverHandler{c.server}.ServeHTTP(w, w.req)`, 其内部最终调用的是 Handler 的 `ServeHTTP()`。代码如下：

```go
func (sh serverHandler) ServeHTTP(rw ResponseWriter, req *Request) {
	handler := sh.srv.Handler
	if handler == nil {
		handler = DefaultServeMux
	}
	if req.RequestURI == "*" && req.Method == "OPTIONS" {
		handler = globalOptionsHandler{}
	}
	// 最终调用 Handler 中对 ServeHTTP 的实现
	handler.ServeHTTP(rw, req)
}
```

##### B: DefaultServeMux

当 `sh.srv.Handler` 为 nil 时，会触发 DefaultServeMux 中的 `ServeHTTP()`

DefaultServeMux 的实现如下：

```go
var DefaultServeMux = &defaultServeMux

var defaultServeMux ServeMux

type ServeMux struct {
	//锁，由于请求涉及到并发处理，因此这里需要一个锁机制
	mu sync.RWMutex   
	// 路由规则，一个 string 对应一个 mux 实体，这里的 string 就是注册的路由表达式
	m  map[string]muxEntry  
	// 是否在任意的规则中带有 host 信息
	hosts bool
}

type muxEntry struct {
 	// 是否精确匹配
	explicit bool 
	// 这个路由表达式对应哪个handler 
	h        Handler 
	// 匹配字符串--正则
	pattern  string  
}
```

根据上述代码可知，DefaultServeMux 是 ServeMux 实例的指针变量。

ServeMux 结构体中有一个 `map[string]muxEntry` 类型的属性 `m`, 它内部存储的就是我们通过 `http.HandleFunc("/",sayHello)` 配置的路由及处理函数。

DefaultServeMux 调用的  `ServeHTTP()` 即 ServeMux 的 `ServeHTTP()`:

```go
func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request) {
	if r.RequestURI == "*" {
		if r.ProtoAtLeast(1, 1) {
			w.Header().Set("Connection", "close")
		}
		w.WriteHeader(StatusBadRequest)
		return
	}
	h, _ := mux.Handler(r)
	h.ServeHTTP(w, r)
}
```

上述代码中又通过 `mux.Handler(r)` 获取了最终要执行的 `h`, 内部实现如下: 

```go
func (mux *ServeMux) Handler(r *Request) (h Handler, pattern string) {

	if r.Method == "CONNECT" {
	
		if u, ok := mux.redirectToPathSlash(r.URL.Host, r.URL.Path, r.URL); ok {
			return RedirectHandler(u.String(), StatusMovedPermanently), u.Path
		}

		return mux.handler(r.Host, r.URL.Path)
	}

	host := stripHostPort(r.Host)
	path := cleanPath(r.URL.Path)

	if u, ok := mux.redirectToPathSlash(host, path, r.URL); ok {
		return RedirectHandler(u.String(), StatusMovedPermanently), u.Path
	}

	if path != r.URL.Path {
		_, pattern = mux.handler(host, path)
		url := *r.URL
		url.Path = path
		return RedirectHandler(url.String(), StatusMovedPermanently), pattern
	}

	return mux.handler(host, r.URL.Path)
}
```

上述代码中，在没有重定向的前提下，又会返回 `mux.handler()`, 其实现如下：

```go
func (mux *ServeMux) handler(host, path string) (h Handler, pattern string) {
	mux.mu.RLock()
	defer mux.mu.RUnlock()

	// Host-specific pattern takes precedence over generic ones
	if mux.hosts {
		h, pattern = mux.match(host + path)
	}
	if h == nil {
		h, pattern = mux.match(path)
	}
	if h == nil {
		h, pattern = NotFoundHandler(), ""
	}
	return
}
```

在上述函数中，会触发 `mux.match()` 去判断是否有匹配的 handler:

```go
// Find a handler on a handler map given a path string.
// Most-specific (longest) pattern wins.
func (mux *ServeMux) match(path string) (h Handler, pattern string) {
	// Check for exact match first.
	v, ok := mux.m[path]
	if ok {
		return v.h, v.pattern
	}

	// Check for longest valid match.
	var n = 0
	for k, v := range mux.m {
		if !pathMatch(k, path) {
			continue
		}
		if h == nil || len(k) > n {
			n = len(k)
			h = v.h
			pattern = v.pattern
		}
	}
	return
}
```

上述代码中，会读取 mux 中的 m 属性，m 是一个 map, `mux.m[path]` 会返回 path 对应的 handler. 而这个 m 的初始化实际是在 `http.HandleFunc()` 的底层调用的。

##### C: Handler 和 路由注册

接下来，我们看一下 DefaultServeMux 中的 m 是如何初始化的。

先来看一下 `http.HandleFunc(,)` 的具体实现:

```go
// 1、http.HandleFunc 的具体实现
func HandleFunc(pattern string, handler func(ResponseWriter, *Request)) {
	DefaultServeMux.HandleFunc(pattern, handler)
}

// 2、DefaultServeMux.HandleFunc 的实现
func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request)) {
	if handler == nil {
		panic("http: nil handler")
	}
	
	// HandlerFunc(handler) 构建 Hanlder 对象
	mux.Handle(pattern, HandlerFunc(handler))
}
```

`HandlerFunc(handler)` 的实现：

```go
// 1、HandlerFunc 类型的定义
type HandlerFunc func(ResponseWriter, *Request)

// 2、HandlerFunc 实现了 Handler 接口, 并在 ServeHTTP calls f(w, r) 内部调用 HandlerFunc 本身
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
	f(w, r)
}

// 3、Handler 接口，其实现类会实现 ServeHTTP 方法
type Handler interface {
 	// 路由实现器
	ServeHTTP(ResponseWriter, *Request) 
}
```

`mux.Handle()` 的内部实现如下：

```go
func (mux *ServeMux) Handle(pattern string, handler Handler) {
	mux.mu.Lock()
	defer mux.mu.Unlock()

	//...省略内容

	if mux.m == nil {
		// 初始化 mux 中的 m 属性
		mux.m = make(map[string]muxEntry)
	}
	
	// 为 mux 中的 m 属性添加键值对
	mux.m[pattern] = muxEntry{h: handler, pattern: pattern}

	if pattern[0] != '/' {
		mux.hosts = true
	}
}
```

通过上面的代码可知，`http.HandleFunc(,)` 的作用其实就是初始化了 DefaultMux 中的 m —— 即 路由注册。

##### D: 自定义 Handler

通过前面三个小节，我们了解了整个路由过程。

我们在调用 `http.ListenAndServe(,)`，第二个参数也可以传递一个自定义的 Handler 接口实现: 

```go
package main

import (
	"fmt"
	"net/http"
)

type CusMux struct{}

func (mux CusMux) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	if r.URL.Path == "/" {
		// 打印到控制台
		fmt.Println("hello")
		// 输入到客户端--浏览器
		fmt.Fprintf(w, "Hello GO! GO! GO !")
		return
	}
	http.NotFound(w, r)
	return
}

func main() {
	cusMux := CusMux{}
	http.ListenAndServe(":9090", cusMux)
}
```

执行上述代码，然后我们在浏览器中输入： http://localhost:9090 之后，就会在浏览器上显示我们通过 `fmt.Fprintf(w, "Hello GO! GO! GO !")` 输出的内容。

#### (3)、Go 代码的执行流程

根据前面几个小节的梳理，总结 Go 代码的执行流程如下：

- 首先调用 `http.HandleFunc(,)`

	按顺序做了几件事：

	1 调用了 DefaultServeMux 的 HandleFunc

	2 调用了 DefaultServeMux 的 Handle 

	3 往 DefaultServeMux 的 map[string]muxEntry 中增加对应的 handler 和路由规则

- 其次调用 `http.ListenAndServe(":9090", nil)`

	按顺序做了几件事情：

	1 实例化 Server

	2 调用 Server 的 ListenAndServe()

	3 调用 `net.Listen("tcp", addr)` 监听端口

	4 启动一个 for 循环，在循环体中 Accept 请求

	5 对每个请求实例化一个 Conn，并且开启一个 goroutine 为这个请求进行服务 `go c.serve()`

	6 读取每个请求的内容 `w, err := c.readRequest()`

	7 判断 handler 是否为空，如果没有设置 handler（这个例子就没有设置 handler），handler就设置为 DefaultServeMux

	8 调用 handler 的 ServeHttp

	9 在这个例子中，下面就进入到 `DefaultServeMux.ServeHttp`

	10 根据 request 选择 handler ，并且进入到这个 handler 的 ServeHTTP `mux.handler(r).ServeHTTP(w, r)`

	11 选择 handler：

	A 判断是否有路由能满足这个 request（循环遍历 ServerMux 的 muxEntry）

	B 如果有路由满足，调用这个路由 handler 的 ServeHttp

	C 如果没有路由满足，调用 NotFoundHandler 的 ServeHttp

---


## 四、表单

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、

---


## 五、访问数据库

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、

---


## 六、session 和数据存储

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、

---


## 七、文本处理

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、

---


## 八、Web 服务

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、

---


## 九、安全与加密

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、

---


## 十、国际化和本地化

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、

---


## 十一、错误处理：调试和测试

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、

---


## 十二、部署与维护

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、

---


## 十三、如何设计一个 Web 框架

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、

---


## 十四、扩展 Web 框架

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

### 5、

#### (1)、

#### (2)、

#### (3)、

#### (4)、
#### (5)、
#### (6)、
