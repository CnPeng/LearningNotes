在 Swift 中实现循环/遍历有如下几种方式：

## 1.1 `for-in`

### 1.1.1 遍历区间

#### 1.1.1.1 顺序遍历

```swift
for index in 0 ..< 5 {
    print(index)
}
```

输出：

```
0
1
2
3
4
```

#### 1.1.1.2 逆序遍历

```swift
for index in (0 ... 5).reversed() {
    print(index)
}
```

输出：

```
5
4
3
2
1
0
```

#### 1.1.1.3 嵌套 where 条件

```swift
for i in 1...10 where i%3 == 0{
    print(i)
}
```

输出 ：

```
3
6
9
```

* 使用 `..<` 指定半开区间，使用 `...` 指定闭区间
* 使用 `range.reversed()` 来指定逆序循环
* 如果在循环体中，不需要使用 `index`，则可以用 `_` 替换 `index`

### 1.1.2 遍历数组

#### 1.1.2.1 顺序遍历

```swift
let test = [10,24,33,6,18]
for value in test {
    print(value)
}
```

输出：

```
10
24
33
6
18
```

#### 1.1.2.2 逆序遍历数组

```swift
let test = [10,24,33,6,18]
for value in test.reversed() {
    print(value)
}
```

输出：

```
18
6
33
24
10
```

#### 1.1.2.3 同时遍历数组的下标和值

```swift
let test = [10,24,33,6,18]
for (index,value) in test.enumerated() {
    print(index,value)
}
```

输出：

```
0 10
1 24
2 33
3 6
4 18
```

那么，混合使用 `reversed` 与 `enumerated` 会有怎样的情况?

```swift
test.enumerated().reversed()
```

依次输出 `(4 18)` 至 `(0...10)`

```swift
test.reversed().enumerated()
```

依次输出 `(0 18) ` 至 ` (4...10)`


### 1.1.3 遍历字符串

```swift
var str = "Hello"

func test(){
    for ch in str {
        print(ch)
    }
}
```

输出 ：

```
H
e
l
l
o
```

### 1.1.4 遍历字典

```swift
var userDict:Dictionary<String,Int>=["张三":23,"李四":24,"王五":25]

//遍历方式1：for-in 之间必须用小括号包裹内容
for (key,value) in userDict{
    print("\(key)的年龄为\(value)")
}

//f遍历方式2：因为构建字典时，实际是将键值对构建成了Element对象，所以，elem就是这个对象
for elem in userDict {
    print(elem)
    print("\(elem.key)的年龄为\(elem.value)")
}
```

## 1.2  `Array.forEach` 

```swift
let test = [10,24,33,6,18]
test.forEach { (value) in
    print(value)
}
```

输出：

```
10
24
33
6
18
```

* 使用 `for-in` 循环时，在循环体内部调用 `return` 会直接结束循环
* 使用 `Array.forEach` 循环时，在闭包内调用 `return` 只会结束一次闭包调用

## 1.3 带有步进的遍历

我们可以实现 `Strideable` 协议，也可以使用 `Strideable` 协议中 `stride` 方法直接进行循环操作

### 1.3.1 使用 `stride(from,to,by)` 

顺序循环 0 至 10（不包括10），依次递增 2

```swift
for index in stride(from: 0, to: 10, by: 2) {
    print(index)
}
```

输出：

```
0
2
4
6
8
```

### 1.3.2、使用 `stride(from,through,by)` 

逆序循环，10 至 0（包括0），依次递减 2

```swift
for index in stride(from: 10, through: 0, by: -2) {
    print(index)
}
```

输出：

```
10
8
6
4
2
0
```

### 1.3.3、使用 `Strideable` 协议

通常使用 `stride` 时，我们传递的参数均为 `Int` ，当我们想对自己定义的类型进行循环时，这样的方式并不方便，好在我们可以使用 `Strideable` 协议解决这个问题

首先我们定义一个类，代表素数

```swift
class Prime {
    public var value:Int = 0
    public init(_ v:Int) {
        value = v
    }
}
```

然后实现 `Strideable` 协议。其中：

* `func distance(to other: Prime)`  表示两个素数之间素数的个数
* `func advanced(by n: Int) -> Prime ` 返回第 n 个素数

**注意，此函数需要返回一个新的 `Prime`，不能修改自身的值**

```swift
final class Prime : Strideable {
    //协议
    func distance(to other: Prime) -> Int {
       var count = 0
       for index in value ..< other.value {
           if Prime(index).isPrime() {
               count += 1
            }
       }
       return count
   }
    //协议
   func advanced(by n: Int) -> Prime {
       let result = Prime(value)
       var count = n
       while true {
           result.value += 1
           if result.isPrime() {
               count -= 1
           }
           if count == 0 {
               break
           }
       }
       return result
   }
   
   typealias Stride = Int

   public var value:Int = 0
   public init(_ v:Int) {
       value = v
   }
   
    //判断当前值是否为素数
   private func isPrime()->Bool {
       guard 2 <= value/2 else {
           return true
       }
       return !((2 ... value/2).contains { (v) -> Bool in
           return value % v == 0
       })
   }
}
```

使用 `stride` 输出给定两个素数之间的所有素数

```swift
let start = Prime(2)
let end = Prime(11)
for value in stride(from:start, through: end, by: 1) {
    print(value.value)
}
```

输出：

```
2
3
5
7
11
```

当然，在实际使用中，需要直接继承 `Strideable` 来实现循环的操作少之又少，我们只需要熟练使用其他的方式即可
