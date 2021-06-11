# 1. 8-defer

## 1.1. defer基本使用

被 defer 修饰的内容，定义在函数内，在函数将要结束时调用（也就是：先调用没有 defer 的语句，最后调用被 defer 修饰的语句），通常用于释放资源（比如 `defer file.close()`）。

```go
package main

import "fmt"

func main() {
	defer fmt.Println("aaaaaaaa")
	fmt.Println("bbbbbb")
}
```

运行结果：

```
cnpeng$ go run Day1.go 
bbbbbb
aaaaaaaa
```

## 1.2. 多个defer

函数中存在多个 defer 时，遵循 **先进后出** 的原则（即栈的进栈和出栈操作）。

函数运行过程中遇见 defer 修饰的内容之后，会把这些语句及其参数暂存到内存中，等其他非 defer 语句执行完毕之后，再按照 **先进后出** 的顺序依次执行（这其实就是一个进栈和出栈的操作）。

* 示例1：

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

```
cnpeng$ go run Day1.go 
没有被defer修饰的普通语句
cccccc
bbbbbb
aaaaaaaa
```

* 示例2：

如果程序中的某处可能会出现异常，那么定义异常前面的 defer 会被调用。

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

```go
cnpeng$ go run Day1.go 
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

## 1.3. defer和匿名函数

```go
package main

import "fmt"

func main() {
	a := 10

	// 读取到这里时 a 的值为10，然后传递给了arg 。暂存到内存时存储了函数及其参数。后面的 a=20 将不会影响到这里
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

```
cnpeng$ go run Day1.go 
C: a =  20
B: a =  20
A: arg =  10
```

## 1.4. defer与循环

在循环中使用 defer 时需要特别注意，因为只有在函数执行完毕后，这些被延迟的函数才会执行，所以下面的代码极有可能会导致内存泄漏。

因为在 filenames 中的所有文件都被处理之前，没有文件会被关闭，f 对象都被暂存到了内存中，如果 filenames 中的内容特别多时，极有可能会导致内存泄漏/溢出。

```go
for _, filename := range filenames {
    f, err := os.Open(filename)
    if err != nil {
        return err
    }
    defer f.Close()

    // 省略对 f 的处理逻辑
}
```

一种解决方法是将循环体中的 defer 语句移至另外一个函数。在每次循环时，调用这个函数。

```go
for _, filename := range filenames {
    if err := doFile(filename); err != nil {
        return err
    }
}

func doFile(filename string) error {
    f, err := os.Open(filename)
    if err != nil {
        return err
    }
    defer f.Close()

    // 省略对 f 的处理逻辑
}
```

## 1.5. 附录：《5 年 Gopher 都不知道的 defer 细节，你别再掉进坑里！》

### 1.5.1. 什么是 defer

defer 是 Go 语言提供的一种用于注册延迟调用的机制，每一次 defer 都会把函数压入栈中，当前函数返回前再把延迟函数取出并执行。

defer 语句并不会马上执行，而是会进入一个栈，函数 return 前，会按先进后出（FILO）的顺序执行。也就是说最先被定义的 defer 语句最后执行。**先进后出的原因是后面定义的函数可能会依赖前面的资源，自然要先执行；否则，如果前面先执行，那后面函数的依赖就没有了**。

### 1.5.2. 采坑点

使用 defer **最容易采坑的地方是和带命名返回参数的函数一起使用时**。

**defer 语句定义时，对外部变量的引用是有两种方式的，分别是作为函数参数和作为闭包引用：**

* **作为函数参数，则在 defer 定义时就把值传递给 defer，并被缓存起来；**
* **作为闭包引用的话，则会在 defer 函数真正调用时根据整个上下文确定当前的值。**

避免掉坑的关键是要理解这条语句：

`return xxx`

这条语句并不是一个原子指令，经过编译之后，变成了三条指令：


* 1. 返回值 = xxx
* 2. 调用 defer 函数
* 3. 空的 return


其中 1 , 3 步才是 return 语句真正的命令，第 2 步是 defer 定义的语句，这里就有可能会操作返回值。

### 1.5.3. 题目和解析

我们一起来做几个题目：

#### 1.5.3.1. 

```go
func f1() (r int) {
    defer func() {
        r++
    }()
    return 0
}
```

解析：按照前述的编译后的指令拆解过程后，得到如下内容:

```go
func f1() (r int) {

    // 1.赋值
    r = 0

    // 2.闭包引用，返回值被修改
    defer func() {
        r++
    }()

    // 3.空的 return
    return
}
```

defer 是闭包引用，返回值被修改，所以 f() 返回 1。

#### 1.5.3.2. 

```go
func f2() (r int) {
    t := 5
    defer func() {
        t = t + 5
    }()
    return t
}
```

解析：按照前述的编译后的指令拆解过程：

```go
func f2() (r int) {
    t := 5
    // 1.赋值
    r = t

    // 2.闭包引用，但是没有修改返回值 r
    defer func() {
        t = t + 5
    }()

    // 3.空的 return
    return
}
```

第二步没涉及返回值 r 的操作，所以返回 5。

#### 1.5.3.3. 

```go
func f3() (r int) {
    defer func(r int) {
        r = r + 5
    }(r)
    return 1
}
```

解析：按照前述的编译后的指令拆解过程：

```go
func f3() (r int) {

    // 1.赋值
    r = 1

    // 2.r 作为函数参数，不会修改要返回的那个 r 值
    defer func(r int) {
        r = r + 5
    }(r)

    // 3.空的 return
    return
}
```

第二步，**r 是作为函数参数使用，是一份复制，defer 语句里面的 r 和 外面的 r 其实是两个变量，里面变量的改变不会影响外层变量 r**，所以不是返回 6 ，而是返回 1。

### 1.5.4. 题目和解析2

#### 1.5.4.1. 

```go
func increaseA() int {
    var i int
    defer func() {
        i++
    }()
    return i
}
```

解析：

大家可能注意到，函数 increaseA() 是匿名返回值，返回局部变量，同时 defer 函数也会操作这个局部变量。

**对于匿名返回值来说，可以假定有一个变量存储返回值**，比如假定返回值变量为 anony，

上面的返回语句可以拆分成以下过程：

```go
annoy = i
i++
return
```

由于 i 是整型，会将值拷贝给 anony，所以 defer 语句中修改 i 值，对函数返回值不造成影响，所以返回 0 。

#### 1.5.4.2. 

```go
func increaseB() (r int) {
    defer func() {
        r++
    }()
    return r
}
```

解析：按照前述的编译后的指令拆解过程：

```go
func increaseB() (r int) {

    // 1.赋值
    r = 0

    // 2.闭包引用，r++
    defer func() {
        r++
    }()

    // 3.空 return
    return 
}
```

闭包中操作了返回值 r , 所以会返回1 


### 1.5.5. 后记

相关阅读：

* [Golang之轻松化解defer的温柔陷阱](https://segmentfault.com/a/1190000018169295#articleHeader4)
* [Go defer实现原理剖析](https://my.oschina.net/renhc/blog/2870345)
* [golang之defer简介](https://leokongwq.github.io/2016/10/15/golang-defer.html)

