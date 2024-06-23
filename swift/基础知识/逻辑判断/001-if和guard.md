# 1. 001-if和guard

在 Swift 中，`if let` 和 `guard let` 都是用来处理可选类型的值的。它们的主要区别在于，`if let` 主要用于条件分支的控制，而 `guard let` 则用于提前退出或跳过循环等。
以下是两者的详细说明：

## 1.1. if let

`if let` 语句可以在 if 语句中**引入一个新的临时常量或变量，用于存储可选类型的解包值**。如果可选类型包含一个值，则将其解包并赋给新的临时常量或变量，并执行相应的代码块；否则，代码块将不会被执行。

例如：

```swift
var optionalString: String? = "Hello"
if let unwrappedString = optionalString {
    print(unwrappedString)
} else {
    print("Optional string is nil")
}
```

上述代码中，`optionalString`是一个可选类型，如果它的值不为空，则会通过if let语句进行解包，并将其值赋给`unwrappedString`，并打印出其内容；否则，打印“Optional string is nil”。

## 1.2. guard let

`guard let` 语句与 `if let` 类似，但它通常用于在控制流的开始部分检查某个条件，并在满足条件的情况下继续执行，而在不满足条件的情况下提前退出或跳过循环。

**`guard let` 语句只能出现在函数、方法或闭包的开始部分。**

例如：

```swift
func processOptionalString(_ optionalString: String?) {
    guard let unwrappedString = optionalString else {
        return
    }

    print(unwrappedString)
}
```

上述代码中，`processOptionalString` 函数接收一个可选类型的参数 `optionalString` 。在函数体的开始部分，使用 guard let 语句检查 `optionalString` 是否有值，如果有值则解包赋值给 `unwrappedString`，并继续执行后续代码；否则直接返回，不再执行后续代码。