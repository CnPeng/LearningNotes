# 1. 一个有关Golang变量作用域的坑

原文：[一个有关Golang变量作用域的坑](https://tonybai.com/2015/01/13/a-hole-about-variable-scope-in-golang/#comments)

编写和调试了一段 Golang 代码，但运行结果始终与期望不符，怪异的很。代码Demo如下：

```go
//testpointer.go
package main

import (
        "fmt"
)

var p *int

func foo() (*int, error) {
        var i int = 5
        return &i, nil
}

func bar() {
        //use p
        fmt.Println(*p)
}

func main() {
        p, err := foo()
        if err != nil {
                fmt.Println(err)
                return
        }
        bar()
        fmt.Println(*p)
}
```

这段代码原意是定义一个包内全局变量 p，用 foo() 的返回值对 p 进行初始化，在 bar 中使用 p。

预期结果：bar() 和 main() 中均输出 5。但编译执行后的结果却是：

```
$go run testpointer.go
panic: runtime error: invalid memory address or nil pointer dereference
[signal 0xb code=0x1 addr=0x0 pc=0x20d1]

goroutine 1 [running]:
main.bar()
    /Users/tony/Test/Go/testpointer.go:17 +0xd1
main.main()
    /Users/tony/Test/Go/testpointer.go:26 +0x11c

goroutine 2 [runnable]:
runtime.forcegchelper()
    /usr/local/go/src/runtime/proc.go:90
runtime.goexit()
    /usr/local/go/src/runtime/asm_amd64.s:2232 +0×1

goroutine 3 [runnable]:
runtime.bgsweep()
    /usr/local/go/src/runtime/mgc0.go:82
runtime.goexit()
    /usr/local/go/src/runtime/asm_amd64.s:2232 +0×1

goroutine 4 [runnable]:
runtime.runfinq()
    /usr/local/go/src/runtime/malloc.go:712
runtime.goexit()
    /usr/local/go/src/runtime/asm_amd64.s:2232 +0×1
exit status 2
```

问题在哪里呢？加上些打印语句再看看：

```go
func bar() {
        //use p
        fmt.Printf("%p, %T\n", p, p) //output: 0x14dc80, 0×0, *int
        fmt.Println(*p) //Crash!!!
}

func main() {
        fmt.Printf("%p, %T\n", p, p) //output: 0x14dc80, 0×0, *int
        p, err := foo()
        if err != nil {
                fmt.Println(err)
                return
        }
        fmt.Printf("%p, %T\n", p, p) //output: 0x2081c6020, 0x20818a258, *int
        bar()
        fmt.Println(*p)
}
```

通过打印输出，发现从 foo 函数中返回的 p(0x2081c6020) 与全局变量的 p(0x14dc80) 居然不是一个地址，也就是说不是一个变量。

而且 从 bar() 中的调试输出来看，全局变量 p 在 foo 函数返回时并未被赋值为 foo 中变量 i 的地址，而依然是一个 nil 值，从而导致程序 Crash。

好了，废话不说了，该是揭晓真相的时候了。问题就在于":="。在main这个作用域中，我们使用了

`p, err := foo()`

最初的理解是 golang 会定义新变量 err，p 为初始定义的那个全局变量。

但**实际情况是，对于使用 `:=` 定义的变量，如果新变量 p 与那个同名已定义变量 (这里就是那个全局变量p)`不在一个作用域中时`，那么 golang `会新定义这个变量p`，`遮盖住全局变量p`**，这就是导致这个问题的真凶。

我们将 main 函数改为：

```go
func main() {
        var err error
        p, err = foo()
        if err != nil {
                fmt.Println(err)
                return
        }
        bar()
}

```
则执行结果就完全符合预期了。