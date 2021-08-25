# 1. 10-iota

[原文地址](https://www.cnblogs.com/ghj1976/p/4224346.html?utm_campaign=studygolang.com&utm_medium=studygolang.com&utm_source=studygolang.com)

iota 是 golang 语言的常量计数器,只能在常量的表达式中使用。

## 1.1. iota 使用规则

* iota 在 const 关键字出现时将被重置为 0 (const内部的第一行之前)，
* const 中每新增一行常量声明将使 iota 计数一次( iota 可理解为 const 语句块中的行索引)。

使用 iota 能简化定义，在定义枚举时很有用。

## 1.2. 使用示例

### 1.2.1. iota只能在常量的表达式中使用。

```go
fmt.Println(iota) 
```

编译错误： undefined: iota

### 1.2.2. 每次 const 出现时，都会让 iota 初始化为0.

```go
const a = iota // a=0

const (
  b = iota     //b=0
  c            //c=1
)
```
### 1.2.3. 自定义类型

自增长常量经常包含一个自定义枚举类型，允许你依靠编译器完成自增设置。

```go
type Stereotype int

const (
    TypicalNoob Stereotype = iota // 0
    TypicalHipster                // 1
    TypicalUnixWizard             // 2
    TypicalStartupFounder         // 3
)
```

### 1.2.4. 可跳过的值

```go
type AudioOutput int

const (
    OutMute AudioOutput = iota // 0
    OutMono                    // 1
    OutStereo                  // 2
    _                          // 此处 iota 的值被赋给了 _ ，即抛弃该值
    _
    OutSurround                // 5
)
```

### 1.2.5. 位掩码表达式

```go
type Allergen int

const (
    IgEggs Allergen = 1 << iota     // 1 << 0 which is 00000001
    IgChocolate                     // 1 << 1 which is 00000010
    IgNuts                          // 1 << 2 which is 00000100
    IgStrawberries                  // 1 << 3 which is 00001000
    IgShellfish                     // 1 << 4 which is 00010000
)
```

这个工作是因为当你在一个 const 组中仅仅有一个标示符在一行的时候，它将使用增长的 iota 取得前面的表达式并且再运用它。在 Go 语言的 spec 中， 这就是所谓的隐性重复最后一个非空的表达式列表。

如果你对鸡蛋，巧克力和海鲜过敏，把这些 bits 翻转到 “on” 的位置（从左到右映射 bits）。然后你将得到一个 bit 值 00010011，它对应十进制的 19。

```go
fmt.Println(IgEggs | IgChocolate | IgShellfish)  // 19
```

### 1.2.6. 定义数量级

```go
type ByteSize float64

const (
    _           = iota                   // ignore first value by assigning to blank identifier
    KB ByteSize = 1 << (10 * iota)       // 1 << (10*1)
    MB                                   // 1 << (10*2)
    GB                                   // 1 << (10*3)
    TB                                   // 1 << (10*4)
    PB                                   // 1 << (10*5)
    EB                                   // 1 << (10*6)
    ZB                                   // 1 << (10*7)
    YB                                   // 1 << (10*8)
)
```

### 1.2.7. 定义在一行的情况

```go
const (
    Apple, Banana = iota + 1, iota + 2
    Cherimoya, Durian
    Elderberry, Fig
)
```

iota 在下一行增长，而不是立即取得它的引用。

```go
// Apple: 1
// Banana: 2
// Cherimoya: 2
// Durian: 3
// Elderberry: 3
// Fig: 4
```

### 1.2.8. 中间插队

```go
const (
    i = iota
    j = 3.14
    k = iota
    l
)
```
那么打印出来的结果是 

```
i=0,
j=3.14,
k=2,
l=3
```

## 1.3. 参考资料：

* [iota: Golang 中优雅的常量](http://segmentfault.com/blog/yexiaobai/1190000000656284)
* [老虞学GoLang笔记-常量](http://www.cnblogs.com/howDo/archive/2013/04/15/golang-constant.html)
* [初识golang，变量，常量，iota的学习](http://my.oschina.net/u/698121/blog/156235)

