Swift 中扩展有两种方式，一种是 `extension` ，一种是 `extension where`。

### 1.30.1. extension 

直接使用这种方式扩展时，所有子类都具有扩展后的成员。如下：

```swift
protocol CusProtocol {}

extension  CusProtocol {
    static func sayHello(){
        print("Hello")
    }
}
```

通过如上方式我们为  CusProtocol 扩展了一个  sayHello() 函数，任意类实现了我们这个 CusProtocol 接口的类都可以调用 sayHello()

### 1.30.2. extension where

```swift
protocol CusProtocol {}

extension  CusProtocol where Self:UIView {
    static func sayHello(){
        print("Hello")
    }
}
```
上述代码中我们也为  CusProtocol 扩展了一个  sayHello() 函数，但也限定了范围：**只有当一个类实现了 CusProtocol 接口，并且这个类同时也是  UIView 的子类时，才可以调用 sayHello() 函数。**

上述代码中 where 关键字后面的 Self 表示当前接口的实现类。
