# 1. 关于Go，你可能不注意的7件事

[原文《关于Go，你可能不注意的7件事》](https://tonybai.com/2015/09/17/7-things-you-may-not-pay-attation-to-in-go/)

> 注意：
> 1、原文发布时间较早，可能会与 Go 最新的 API 有出入。
> 2、本文实验环境：Go 1.5 darwin_amd64。
> 3、文中相关代码在 [experiments/details-in-go/](https://github.com/bigwhite/experiments/tree/master/details-in-go) 仓库中

Go以简洁著称，但简洁中不乏值得玩味的小细节。这些小细节不如 goroutine、interface 和 channel 那样"高大上"，但它们却对理解 Go 语言有着重要的作用。

这里想挑出一些和大家一起通过详实的例子来逐一展开和理解。本文内容较为基础，适合初学者，高手可飘过。



## 1.1. 源文件字符集和字符集编码

**Go 源码文件默认采用 Unicode 字符集**，Unicode 码点(code point)和内存中字节序列（byte sequence）的变换实现使用了UTF-8：一种变长多字节编码，同时也是一种事实字符集编码标准，为Linux、MacOSX 上的默认字符集编码，因此使用 Linux 或 MacOSX 进行 Go 程序开发，你会省去很多字符集转换方面的烦恼。

但如果你是在 Windows 上使用 默认编辑器编辑 Go 源码文本，当你编译以下代码时会遇到编译错误：

```go
//hello.go
package main

import "fmt"

func main() {
    fmt.Println("中国人")
}
```

编译结果：

```
$ go build hello.go
# 2. command-line-arguments
hello.go:6 illegal UTF-8 sequence d6 d0
hello.go:6 illegal UTF-8 sequence b9
hello.go:6 illegal UTF-8 sequence fa c8
hello.go:6 illegal UTF-8 sequence cb 22
hello.go:6 newline in string
hello.go:7 syntax error: unexpected }, expected )
```

这是因为 Windows 默认采用的是 CP936 字符集编码，也就是 GBK 编码，“中国人”三个字的内存字节序列为：

“d0d6    fab9    cbc8    000a” （通过 iconv 转换，然后用 od -x 查看）

这个字节序列并非 utf-8 字节序列，Go编译器因此无法识别。要想通过编译，需要将该源文件转换为 UTF-8 编码格式。

字符集编码对字符和字符串字面值(Literal)影响最大，在 Go 中对于字符串我们可以有三种写法：

* 字面值

```go
var s = "中国人"
```

* 码点表示法

```go
var s1 = "\u4e2d\u56fd\u4eba"
//这种也可以
var s2 = "\U00004e2d\U000056fd\U00004eba"
```

* 字节序列表示法（二进制表示法）

```go
var s3 = "\xe4\xb8\xad\xe5\x9b\xbd\xe4\xba\xba"
```

这三种表示法中，**除字面值转换为字节序列存储时根据编辑器保存的源码文件编码格式之外，其他两种均不受编码格式影响**。

我们可以通过逐字节输出来查 看字节序列的内容：

```go
fmt.Println("s byte sequence:")
for i := 0; i < len(s); i++ {
    fmt.Printf("0x%x ", s[i])
}
fmt.Println("")
```

## 1.2. 续行

良好的代码风格一般会要求代码中不能有太长的代码行，否则会影响代码阅读者的体验。

在 C 中有续行符 `\` 专门用于代码续行处理；但在 Go 中没有专属续行符，如何续行需要依据 Go 的语法规则（参见 [Go spec](https://golang.org/ref/spec)）。

Go 与 C 一样，都是以分号 `;` 作为语句结束的标识。

不过大多数情况下，分号无需程序员手工输入，而是由编译器自动识别语句结束位置，并插入 分号。因此续行要选择合法的位置。

下面代码展示了一些合法的续行位置：(别嫌太丑，这里仅仅是展示合法位置的demo)

```go
//details-in-go/2/newline.go
… …
var (
    s = "This is an example about code newline," +
        "for string as right value"
    d = 5 + 4 + 7 +
        4
    a = [...]int{5, 6, 7,
        8}
    m = make(map[string]int,
        100)
    c struct {
        m1     string
        m2, m3 int
        m4     *float64
    }

    f func(int,
        float32) (int,
        error)
)

func foo(int, int) (string, error) {
    return "",
        nil
}

func main() {
    if i := d; i >
        100 {
    }

    var sum int
    for i := 0; i < 100; i = i +
        1 {
        sum += i
    }

    foo(1,
        6)

    var i int
    fmt.Printf("%s, %d\n",
        "this is a demo"+
            " of fmt Printf",
        i)
}
```

实际编码中，我们可能经常遇到的是 `fmt.Printf` 系列方法中 `format string` 太长的情况，但由于 Go 不支持相邻字符串自动连接(concatenate)，只能通过`+`来连接fmt字符串，且 `+` 必须放在前一行末尾。另外 Gofmt 工具会自动调整一些不合理的续行处理，主要针对 for, if 等控制语句。

## 1.3. Method Set（方法集）

Method Set 是 Go 语法中一个重要的隐式概念，在为 interface 变量做动态类型赋值、embeding struct/interface、type alias、method expression 时都会用到Method Set这个重要概念。

### 1.3.1. interface的Method Set

根据 Go spec，interface 类型的 Method Set 就是其 interface（An interface type specifies a method set called its interface）。

```go
type I interface {
    Method1()
    Method2()
}
```

I 的 Method Set 包含的就是其 literal 中的两个方法：Method1 和 Method2。

我们可以通过 reflect 来获取 interface 类型的 Method Set：

```go
//details-in-go/3/interfacemethodset.go
package main

import (
    "fmt"
    "reflect"
)

type I interface {
    Method1()
    Method2()
}

func main() {
    var i *I
    elemType := reflect.TypeOf(i).Elem()
    n := elemType.NumMethod()
    for i := 0; i < n; i++ {
        fmt.Println(elemType.Method(i).Name)
    }
}
```

运行结果：

```
$go run interfacemethodset.go
Method1
Method2
```

### 1.3.2. 除 interface type 外的类型的 Method Set（方法集）

* 对于非 interface type 的类型 T，其 Method Set 为所有 receiver 为 T 类型的方法组成；
* 而类型 `*T` 的 Method Set 则包含所有 receiver 为 `T` 和 `*T` 类型的方法。

```go
// details-in-go/3/othertypemethodset.go
package main

import "./utils"

type T struct {
}

func (t T) Method1() {
}

func (t *T) Method2() {
}

func (t *T) Method3() {
}

func main() {
    var t T
    utils.DumpMethodSet(&t)

    var pt *T
    utils.DumpMethodSet(&pt)
}
```

utils.DumpMethodSet 的函数定义如下：

```go
package utils

import (
	"fmt"
	"reflect"
)

// To dump method set of type T, you should pass a pointer to T
// to DumpMethodSet，include interface type.
//
// e.g.
// for interface type I:
//   utils.DumpMethodSet((*I)(nil))
//
// for non-interface type T:
//   var t T
//   utils.DumpMethodSet(&t)
//
// for non-interface type *T:
//   var pt = &T{}
//   utils.DumpMethodSet(&pt)
//
func DumpMethodSet(i interface{}) {
	v := reflect.TypeOf(i)
	elemTyp := v.Elem()

	n := elemTyp.NumMethod()
	if n == 0 {
		fmt.Printf("%s's method set is empty!\n", elemTyp)
		return
	}

	fmt.Printf("%s's method sets:\n", elemTyp)
	for j := 0; j < n; j++ {
		fmt.Println("\t", elemTyp.Method(j).Name)
	}
	fmt.Printf("\n")
}
```

我们要 dump 出 `T` 和 `*T` 各自的 Method Set，运行结果如下：

```
$go run othertypemethodset.go
main.T's method sets:
     Method1

*main.T's method sets:
     Method1
     Method2
     Method3
```

可以看出类型 T 的 Method set 仅包含一个 receiver 类型为T的方法：Method1，

而 `*T` 的 Method Set 则包含了 T 的 Method Set 以及所有 receiver 类型为 `*T` 的 Method。

如果此时我们有一个 interface type 如下：

```go
type I interface {
    Method1()
    Method2()
}
```

那下面哪个赋值语句合法呢？

合不合法完全依赖于右值类型是否实现了 interface type I的所有方法，即右值类型的 Method Set 是否包含了 I 的 所有方法。

```go
var t T
var pt *T

var i I = t
var i I = pt
```

编译错误告诉我们：

```
var i I = t // cannot use t (type T) as type I in assignment:
     T does not implement I (Method2 method has pointer receiver)
```

* T 的 Method Set 中只有 Method1 一个方法，没有实现 I 接口中的 Method2，因此不能用 t 赋值给 i；
* 而 `*T` 实现了 I 的所有接口，赋值合法。

不过 Method set 校验仅限于在赋值给 interface 变量时进行。无论是 T 还是`*T` 类型的方法集中的方法，对于 T 或 `*T` 类型变量都是可见且可以调用的，如下面代码 都是合法的：

```go
pt.Method1()
t.Method3()
```

因为 Go 编译器会自动为你的代码做 receiver 转换：

```go
pt.Method1() <=> (*pt).Method1()
t.Method3() <=> (&t).Method3()
```

很多人纠结于 method 定义时 receiver 的类型（T or `*T`），个人觉得有两点考虑：

* 效率
    * Go方法调用receiver是以传值的形式传入方法中的。如果类型size较大，以value形式传入消耗较大，这时指针类型就是首选。

2) 是否赋值给interface变量、以什么形式赋值
   就像本节所描述的，由于T和*T的Method Set可能不同，我们在设计Method receiver type时需要考虑在interface赋值时通过对Method set的校验。

### 1.3.3. embeding type 的 Method Set

#### 1.3.3.1. interface embeding

我们先来看看 interface 类型 embeding。例子如下：

```go
//details-in-go/3/embedinginterface.go
package main

import "./utils"

type I1 interface {
    I1Method1()
    I1Method2()
}
type I2 interface {
    I2Method()
}

type I3 interface {
    I1
    I2
}

func main() {
    utils.DumpMethodSet((*I1)(nil))
    utils.DumpMethodSet((*I2)(nil))
    utils.DumpMethodSet((*I3)(nil))
}
```

```
$go run embedinginterface.go
main.I1's method sets:
     I1Method1
     I1Method2

main.I2's method sets:
     I2Method

main.I3's method sets:
     I1Method1
     I1Method2
     I2Method
```

可以看出嵌入 interface type 的 interface type I3 的 Method Set 包含了被嵌入的 interface type：I1 和 I2 的 Method Set。

很多情况下，我们 Go 的 interface type 中仅包含有少量方法，常常仅是一个 Method，通过 interface type embeding 来定义一个新 interface，这是 Go 的一个惯用法，比如我们常用的 io 包中的 Reader, Write r以及 ReadWriter 接口：

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

type ReadWriter interface {
    Reader
    Writer
}
```

#### 1.3.3.2. struct embeding interface

在 struct 中嵌入 interface type 后，struct 的 Method Se t中将包含 interface 的 Method Set：

```go
type T struct {
    I1
}

func (T) Method1() {

}

… …
func main() {
    … …
    var t T
    utils.DumpMethodSet(&t)
    var pt = &T{
        I1: I1Impl{},
    }
    utils.DumpMethodSet(&pt)
}

```

输出结果与预期一致：

```
main.T's method sets:
     I1Method1
     I1Method2
     Method1

*main.T's method sets:
     I1Method1
     I1Method2
     Method1
```

#### 1.3.3.3. struct embeding struct

在 struct 中 embeding struct 提供了一种“继承”的手段，外部的 Struct 可以“继承”嵌入 struct 的所有方法（无论 receiver 是 T 还是 `*T` 类型）实现，但 Method Set 可能会略有不同。看下面例子：

```go
//details-in-go/3/embedingstructinstruct.go
package main

import "./utils"

type T struct {
}

func (T) InstMethod1OfT() {

}

func (T) InstMethod2OfT() {

}

func (*T) PtrMethodOfT() {

}

type S struct {
}

func (S) InstMethodOfS() {

}

func (*S) PtrMethodOfS() {
}

type C struct {
    T
    *S
}

func main() {
    var c = C{S: &S{}}
    utils.DumpMethodSet(&c)
    var pc = &C{S: &S{}}
    utils.DumpMethodSet(&pc)

    c.InstMethod1OfT()
    c.PtrMethodOfT()
    c.InstMethodOfS()
    c.PtrMethodOfS()

    pc.InstMethod1OfT()
    pc.PtrMethodOfT()
    pc.InstMethodOfS()
    pc.PtrMethodOfS()
}
```

```
$go run embedingstructinstruct.go
main.C's method sets:
     InstMethod1OfT
     InstMethod2OfT
     InstMethodOfS
     PtrMethodOfS

*main.C's method sets:
     InstMethod1OfT
     InstMethod2OfT
     InstMethodOfS
     PtrMethodOfS
     PtrMethodOfT
```

可以看出：

* **类型 C 的 Method Set = T 的 Method Set + `*S` 的 Method Set**
* **类型 `*C` 的 Method Set = `*T` 的 Method Set + `*S` 的 Method Set**

同时通过例子可以看出，无论是 T 还是`*S` 的方法，C 或 `*C` 类型变量均可调用（编译器甜头），不会被局限在 Method Set 中。

### 1.3.4. alias type 的 Method Set

Go 支持为已有类型定义 alias type，如：

```go
type MyInterface I
type Mystruct T
```

对于 alias type 的 Method Set 是如何定义的呢？我们看下面例子：

```go
//details-in-go/3/aliastypemethodset.go
package main

import "./utils"

type I interface {
    IMethod1()
    IMethod2()
}

type T struct {
}

func (T) InstMethod() {

}
func (*T) PtrMethod() {

}

type MyInterface I
type MyStruct T

func main() {
    utils.DumpMethodSet((*I)(nil))

    var t T
    utils.DumpMethodSet(&t)
    var pt = &T{}
    utils.DumpMethodSet(&pt)

    utils.DumpMethodSet((*MyInterface)(nil))

    var m MyStruct
    utils.DumpMethodSet(&m)
    var pm = &MyStruct{}
    utils.DumpMethodSet(&pm)
}
```

```
$go run aliastypemethodset.go
main.I's method sets:
     IMethod1
     IMethod2

main.T's method sets:
     InstMethod

*main.T's method sets:
     InstMethod
     PtrMethod

main.MyInterface's method sets:
     IMethod1
     IMethod2

main.MyStruct's method set is empty!
*main.MyStruct's method set is empty!
```

从例子的结果上来看，Go 对于 interface 和 struct 的 alias type 给出了“不一致”的结果：

* MyInterface 的 Method Set 与接口类型 I Method Set 一致；
* 而 MyStruct 并未得到 T 的哪怕一个 Method，MyStruct 的Method Set 为空。

## 1.4. Method Type、Method Expression、Method Value

Go 中没有 class，方法与对象通过 receiver 联系在一起，我们可以为任何非 built in 类型定义 method：

```go
type T struct {
    a int
}

func (t T) Get() int       { return t.a }
func (t *T) Set(a int) int { t.a = a; return t.a }
```

在 C++ 等 OO 语言中，对象在调用方法时，编译器会自动在方法的第一个参数中传入 this/self 指针，而对于 Go 来 说，receiver 也是同样道理，将 T 的 method 转换为普通 function 定义：

```go
func Get(t T) int       { return t.a }
func Set(t *T, a int) int { t.a = a; return t.a }
```

这种 function 形式被称为 Method Type，也可以称为 Method 的signature。

Method 的一般使用方式如下：

```go
var t T
t.Get()
t.Set(1)
```

不过我们也可以像普通 function 那样使用它，根据上面的 Method Type定义：

```go
var t T
T.Get(t)
(*T).Set(&t, 1)
```

这种以直接以类型名 T 调用方法 M 的表达方法称为 Method Expression。

* 类型 T 只能调用 T 的 Method Set 中的方法；
* 同理`*T` 只能调用 `*T` 的 Method Set 中的方法。

上述例子中 T 的 Method Set 中只有 Get，因此 T.Get 是合法的。但 T.Set 则不合法：

```go
T.Set(2) //invalid method expression T.Set (needs pointer receiver: (*T).Set)
```

我们只能使用 `(*T).Set(&t, 11)`。

这样看来 Method Expression 有些类似于 C++ 中的 static 方法(以该类的某个对象实例作为第一个参数)。

另外 Method express 自身类型就是一个普通 function，可以作为右值赋值给一个函数类型的变量：

```go
f1 := (*T).Set //函数类型：func (t *T, int)int
f2 := T.Get //函数类型：func(t T)int
f1(&t, 3)
fmt.Println(f2(t))
```

Go 中还定义了一种与 Method 有关的语法：**如果一个表达式 t 具有静态类型 T，M 是 T 的 Method Set 中的一个方法，那么 t.M 即为Method Value。注意这里是 t.M 而不是 T.M。**

```go
f3 := (&t).Set //函数类型：func(int)int
f3(4)
f4 := t.Get//函数类型：func()int
fmt.Println(f4())
```

可以看出，Method value 与 Method Expression 不同之处在于，Method value 绑定了 T 对象实例，它的函数原型并不包含 Method Expression 函数原型中的第一个参数。完整例子参见：details-in-go/4/methodexpressionandmethodvalue.go。

## 1.5. for range“坑”大阅兵

for range 的引入提升了 Go 的表达能力，但 for range 显然不是”免费的午餐“，在享用这个美味前，需要搞清楚 for range 的一些坑。

### 1.5.1. iteration variable 重用

for range 的 idiomatic 的使用方式是使用 short variable declaration（即`:=`）形式在 for expression 中声明 iteration variable，但需要注意的是**这些 variable 在每次循环体中都会被重用，而不是重新声明。**

```go
//details-in-go/5/iterationvariable.go
… …
    var m = [...]int{1, 2, 3, 4, 5}

    for i, v := range m {
        go func() {
            time.Sleep(time.Second * 3)
            fmt.Println(i, v)
        }()
    }

    time.Sleep(time.Second * 10)
… …
```

在我的 Mac 上，输出结果如下：

```
$go run iterationvariable.go
4 5
4 5
4 5
4 5
4 5
```

各个 goroutine 中输出的 i,v 值都是 for range 循环结束后的 i, v 最终值，而不是各个 goroutine 启动时的 i, v 值。一个可行的 fix 方法：

```go
    for i, v := range m {
        go func(i, v int) {
            time.Sleep(time.Second * 3)
            fmt.Println(i, v)
        }(i, v)
    }
```

### 1.5.2. range expression 副本参与 iteration

range 后面接受的表达式的类型包括：array,  pointer to array, slice, string, map 和 channel(有读权限的)。我们以 array 为例来看一个简单的例子：

```go
//details-in-go/5/arrayrangeexpression.go
func arrayRangeExpression() {
    var a = [5]int{1, 2, 3, 4, 5}
    var r [5]int

    fmt.Println("a = ", a)

    for i, v := range a {
        if i == 0 {
            a[1] = 12
            a[2] = 13
        }
        r[i] = v
    }

    fmt.Println("r = ", r)
}
```

我们期待输出结果：

a =  [1 2 3 4 5]
r =  [1 12 13 4 5]
a =  [1 12 13 4 5]

但实际输出结果却是：

a =  [1 2 3 4 5]
r =  [1 2 3 4 5]
a =  [1 12 13 4 5]

我们原以为在第一次 iteration，也就是 i = 0 时，我们对 a 的修改(a[1] = 12，a[2] = 13)会在第二次、第三次循环中被v取出，但结果却是v取出的依旧是 a 被修改前的值：2和3。

这就是 for range 的一个不大不小的坑：**range expression副本参与循环**。

**也就是说在上面这个例子里，真正参与循环的是 a 的副本，而不是真正的 a，** 伪代码如 下：

```go
    for i, v := range a' {//a' is copy from a
        if i == 0 {
            a[1] = 12
            a[2] = 13
        }
        r[i] = v
    }
```

Go 中的数组在内部表示为连续的字节序列，虽然长度是 Go 数组类型的一部分，但长度并不包含的数组的内部表示中，而是由编译器在编译期计算出来。

这个例子中，**对 range 表达式的拷贝，即对一个数组的拷贝，`a'` 则是 Go 临时分配的连续字节序列，与 a 完全不是一块内存**。因此无论 a 被 如何修改，其副本`a'`依旧保持原值，并且参与循环的是`a'`，因此 v 从 `a'` 中取出的仍旧是 a 的原值，而非修改后的值。

我们再来试试 pointer to array：

```go
func pointerToArrayRangeExpression() {
    var a = [5]int{1, 2, 3, 4, 5}
    var r [5]int

    fmt.Println("pointerToArrayRangeExpression result:")
    fmt.Println("a = ", a)

    for i, v := range &a {
        if i == 0 {
            a[1] = 12
            a[2] = 13
        }

        r[i] = v
    }

    fmt.Println("r = ", r)
    fmt.Println("a = ", a)
    fmt.Println("")
}

```

这回的输出结果如下：

```
pointerToArrayRangeExpression result:
a =  [1 2 3 4 5]
r =  [1 12 13 4 5]
a =  [1 12 13 4 5]
```

 我们看到这次 r 数组的值与最终 a被修改后的值一致了。

 这个例子中我们使用了 `*[5]int` 作为 range 表达式，其副本依旧是一个指向原数组 a 的指针，因此后续所有循环中均是`&a`指向的原数组亲自参与的，因此 v 能从 &a 指向的原数组中取出 a 修改后的值。

idiomatic go 建议我们尽可能的用 slice 替换掉 array 的使用，这里用 slice 能否实现预期的目标呢？我们来试试：

```go
func sliceRangeExpression() {
    var a = [5]int{1, 2, 3, 4, 5}
    var r [5]int

    fmt.Println("sliceRangeExpression result:")
    fmt.Println("a = ", a)

    // 此处的 a[:] 标识对数组 a 进行切片
    for i, v := range a[:] {
        if i == 0 {
            a[1] = 12
            a[2] = 13
        }

        r[i] = v
    }

    fmt.Println("r = ", r)
    fmt.Println("a = ", a)
    fmt.Println("")
}
```

```
pointerToArrayRangeExpression result:
a =  [1 2 3 4 5]
r =  [1 12 13 4 5]
a =  [1 12 13 4 5]
```

显然用 slice 也能实现预期要求。我们可以分析一下 slice 是如何做到的。

**slice 在 go 的内部表示为一个 struct，由 `(*T, len, cap)` 组成**，其中，

* `*T`指向 slice 对应的 underlying array 的指针，
* len 是 slice 当前长度，
* cap 为 slice 的最大容量。

**当 range 进行 expression 复制时，它实际上复制的是一个 slice，也就是那个 struct。副本 struct 中的 `*T` 依旧指向原 slice 对应的 array**，为此对 slice 的修改都反映到 underlying array a 上去了，v 从副本 struct 中 `*T` 指向的 underlying array 中获取数组元素，也就得到了被修改后的元素值。

**slice 与 array 还有一个不同点，就是其 len 在运行时可以被改变，而 array 的 len 是一个常量，不可改变。**

那么 len 变化的 slice 对 for range 有何影响呢？我们继续看一个例子：

```go
func sliceLenChangeRangeExpression() {
    var a = []int{1, 2, 3, 4, 5}
    var r = make([]int, 0)

    fmt.Println("sliceLenChangeRangeExpression result:")
    fmt.Println("a = ", a)

    for i, v := range a {
        if i == 0 {
            a = append(a, 6, 7)
        }

        r = append(r, v)
    }

    fmt.Println("r = ", r)
    fmt.Println("a = ", a)
}
```

输出结果：

```go
a =  [1 2 3 4 5]
r =  [1 2 3 4 5]
a =  [1 2 3 4 5 6 7]
```

在这个例子中，原 slice a 在 for range 过程中被附加了两个元素 6 和 7，其 len 由5增加到7，但这对于 r 却没有产生影响。

这里的原因就在于 a 的副本 a' 的内部表示 struct 中的 len 字段并没有改变，依旧是 5，因此 for range 只会循环 5 次，也就只获取 a 对应的 underlying 数组的前 5 个元素。

range 的副本行为会带来一些性能上的消耗，尤其是当 range expression 的类型为数组时，range 需要复制整个数组；

而当 range expression 类型为 pointer to array 或 slice 时，这个消耗将小得多，仅仅需要复制一个指针或一个 slice 的内部表示（一个struct）即可。我们可以通过 benchmark test 来看一下三种情况的消耗情况对比：

对于元素个数为100 的 int 数组或 slice，测试结果如下：

```go
//details-in-go/5/arraybenchmark
go test -bench=.
testing: warning: no tests to run
PASS
BenchmarkArrayRangeLoop-4             20000000           116 ns/op
BenchmarkPointerToArrayRangeLoop-4    20000000            64.5 ns/op
BenchmarkSliceRangeLoop-4             20000000            70.9 ns/op
```

可以看到 range expression 类型为 slice 或 pointer to array 的性能相近，消耗都近乎是数组类型的 1/2。

### 1.5.3. 其他 range expression 类型

**对于 range 后面的其他表达式类型，比如 string, map, channel，for range 依旧会制作副本**。

#### 1.5.3.1. string

对 string 来说，由于 **string 的内部表示为 `struct {*byte, len)`，并且 string 本身是 immutable 的**，因此其行为和消耗与 slice expression 类似。

不过 for range **对于 string 来说，每次循环的单位是 rune(code point的值)，而不是 byte，index 为迭代字符码点的第一个字节的 position**：

```go
    var s = "中国人"

    for i, v := range s {
        fmt.Printf("%d %s 0x%x\n", i, string(v), v)
    }
```

输出结果：

```
0 中 0x4e2d
3 国 0x56fd
6 人 0x4eba
```

**如果 s 中存在非法 utf8 字节序列，那么 v 将返回 0xFFFD 这个特殊值**，并且在接下来一轮循环中，v 将仅前进一个字节：

```go
//byte sequence of s: 0xe4 0xb8 0xad 0xe5 0x9b 0xbd 0xe4 0xba 0xba
    var sl = []byte{0xe4, 0xb8, 0xad, 0xe5, 0x9b, 0xbd, 0xe4, 0xba, 0xba}
    for _, v := range sl {
        fmt.Printf("0x%x ", v)
    }
    fmt.Println("\n")

    sl[3] = 0xd0
    sl[4] = 0xd6
    sl[5] = 0xb9

    for i, v := range string(sl) {
        fmt.Printf("%d %x\n", i, v)
    }
```

输出结果：

```
0xe4 0xb8 0xad 0xe5 0x9b 0xbd 0xe4 0xba 0xba

0 4e2d
3 fffd
4 5b9
6 4eba
```

以上例子源码在 details-in-go/5/stringrangeexpression.go 中可以找到。

#### 1.5.3.2. map

* 对于 map 来说，**map 内部表示为一个指针，指针副本也指向真实 map，因此 for range 操作均操作的是源 map。**

for range 不保证每次迭代的元素次序，对于下面代码：

```go
 var m = map[string]int{
        "tony": 21,
        "tom":  22,
        "jim":  23,
    }

    for k, v := range m {
        fmt.Println(k, v)
    }
```

输出结果可能是：

```
tom 22
jim 23
tony 21
```

也可能是：

```
tony 21
tom 22
jim 23
```

或其他可能。

* **如果 map 中的某项在循环到达前被在循环体中删除了，那么它将不会被 iteration variable 获取到。**

```go
  counter := 0
    for k, v := range m {
        if counter == 0 {
            delete(m, "tony")
        }
        counter++
        fmt.Println(k, v)
    }
    fmt.Println("counter is ", counter)
```

反复运行多次，我们得到的两个结果：

```
tony 21
tom 22
jim 23
counter is  3
```

```
tom 22
jim 23
counter is  2
```

* **如果在循环体中新创建一个map元素项，那该项元素可能出现在后续循环中，也可能不出现**：

```go
    m["tony"] = 21
    counter = 0

    for k, v := range m {
        if counter == 0 {
            m["lucy"] = 24
        }
        counter++
        fmt.Println(k, v)
    }
    fmt.Println("counter is ", counter)
```

执行结果：

```
tony 21
tom 22
jim 23
lucy 24
counter is  4
```

or

```
tony 21
tom 22
jim 23
counter is  3
```

以上代码可以在 details-in-go/5/maprangeexpression.go 中可以找到。

#### 1.5.3.3. channel

对于 channel 来说，**channe l内部表示为一个指针，channel 的指针副本也指向真实 channel**。

for range 最终以阻塞读的方式阻塞在 channel expression 上（即便是 buffered channel，当 channel 中无数据时，for range 也会阻塞在 channel 上），直到 channel 关闭：

```go
//details-in-go/5/channelrangeexpression.go
func main() {
    var c = make(chan int)

    go func() {
        time.Sleep(time.Second * 3)
        c <- 1
        c <- 2
        c <- 3
        close(c)
    }()

    for v := range c {
        fmt.Println(v)
    }
}
```

运行结果：

```
1
2
3
```

**如果 channel 变量为 nil，则 for range 将永远阻塞。**

## 1.6. select求值

golang 引入的 select 为我们提供了一种在多个 channel 间实现“多路复用”的一种机制。

select 的运行机制这里不赘述，但 select 的 case expression 的求值顺序我们倒是要通过一个例子来了解一下：

```go
// details-in-go/6/select.go
func takeARecvChannel() chan int {
    fmt.Println("invoke takeARecvChannel")
    c := make(chan int)

    go func() {
        time.Sleep(3 * time.Second)
        c <- 1
    }()

    return c
}

func getAStorageArr() *[5]int {
    fmt.Println("invoke getAStorageArr")
    var a [5]int
    return &a
}

func takeASendChannel() chan int {
    fmt.Println("invoke takeASendChannel")
    return make(chan int)
}

func getANumToChannel() int {
    fmt.Println("invoke getANumToChannel")
    return 2
}

func main() {
    select {
    //recv channels
    case (getAStorageArr())[0] = <-takeARecvChannel():
        fmt.Println("recv something from a recv channel")

        //send channels
    case takeASendChannel() <- getANumToChannel():
        fmt.Println("send something to a send channel")
    }
}
```

运行结果：

```
$go run select.go
invoke takeARecvChannel
invoke takeASendChannel
invoke getANumToChannel

invoke getAStorageArr
recv something from a recv channel
```

通过例子我们可以看出：

1) select 执行开始时，首先所有 case expression 的表达式都会被求值一遍，按语法先后次序。

```
invoke takeARecvChannel
invoke takeASendChannel
invoke getANumToChannel
```

例外的是 recv channel 的位于赋值等号左边的表达式（这里是：(getAStorageArr())[0]）不会被求值。

2) **如果选择要执行的 case 是一个 recv channel，那么它的赋值等号左边的表达式会被求值**：如例子中当 goroutine 3s 后向 recv chan 写入一个 int 值后，select 选择了 recv channel 执行，此时对 = 左侧的表达式 (getAStorageArr())[0] 开始求值，输出“invoke getAStorageArr”。

## 1.7. panic 的 recover过程

Go 没有提供“try-catch-finally”这样的异常处理设施，而仅仅提供了 panic 和 recover，其中 recover 还要结合 defer 使用。最初这也是被一些人诟病的点。但和错误码返回值一样，渐渐的大家似乎适应了这些，征讨之声渐稀，即便有也是排在“缺少generics” 之后了。

### 1.7.1. panicking

在没有 recover 的时候，一旦 panic 发生，panic 会按既定顺序结束当前进程，这一过程称为 panicking。下面的例子模拟了这一过程：

```go
//details-in-go/7/panicking.go
… …
func foo() {
    defer func() {
        fmt.Println("foo defer func invoked")
    }()
    fmt.Println("foo invoked")

    bar()
    fmt.Println("do something after bar in foo")
}

func bar() {
    defer func() {
        fmt.Println("bar defer func invoked")
    }()
    fmt.Println("bar invoked")

    zoo()
    fmt.Println("do something after zoo in bar")
}

func zoo() {
    defer func() {
        fmt.Println("zoo defer func invoked")
    }()

    fmt.Println("zoo invoked")
    panic("runtime exception")
}

func main() {
    foo()
}
```

执行结果：

```
$go run panicking.go
foo invoked
bar invoked
zoo invoked
zoo defer func invoked
bar defer func invoked
foo defer func invoked
panic: runtime exception

goroutine 1 [running]:
… …
exit status 2
```

从结果可以看出：

* panic 在 zoo 中发生，在 zoo 真正退出前，zoo 中注册的 defer 函数会被逐一执行(FILO)，由于 zoo defer 中没有捕捉 panic，因此 panic 被抛向其 caller：bar。
* 这时对于 bar 而言，其函数体中的 zoo 的调用就好像变成了 panic 调用似的，zoo 有些类似于“黑客帝国3”中里奥被史密斯(panic)感 染似的，也变成了史密斯(panic)。panic 在 bar 中扩展开来，bar 中的defer 也没有捕捉和 recover panic，因此在 bar 中的 defer func 执行完毕后，panic 继续抛给 bar 的 caller: foo；
* 这时对于 foo 而言，bar 就变成了 panic，同理，最终 foo 将 panic 抛给了 main
* main 与上述函数一样，没有 recover，直接异常返回，导致进程异常退出。

### 1.7.2. recover

**recover 只有在 defer 函数中调用才能起到 recover 的作用**，这样 recover 就和 defer 函数有了紧密联系。

我们在 zoo 的 defer 函数中捕捉并 recover 这个panic：

```go
//details-in-go/7/recover.go
… …
func zoo() {
    defer func() {
        fmt.Println("zoo defer func1 invoked")
    }()

    defer func() {
        if x := recover(); x != nil {
            log.Printf("recover panic: %v in zoo recover defer func", x)
        }
    }()

    defer func() {
        fmt.Println("zoo defer func2 invoked")
    }()

    fmt.Println("zoo invoked")
    panic("zoo runtime exception")
}
… …
```

这回的执行结果如下：

```
$go run recover.go
foo invoked
bar invoked
zoo invoked
zoo defer func2 invoked
2015/09/17 16:28:00 recover panic: zoo runtime exception in zoo recover defer func
zoo defer func1 invoked
do something after zoo in bar
bar defer func invoked
do something after bar in foo
foo defer func invoked
```

由于 zoo 在 defer 里恢复了 panic，这样在 zoo 返回后，bar 不会感知到任何异常，将按正常逻辑输出函数执行内容，比如：“do something after zoo in bar”,以此类推。

但若如果在 zoo defer func 中 recover panic 后，又 raise another panic，那么 zoo 对于 bar 来说就又会变成 panic 了。

