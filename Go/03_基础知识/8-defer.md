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