String 的 `firstIndex(of:)` 拿到的是一个 Index 对象, 要想获取其 int 类型的索引值，需要使用 `distance(from:,to:)` ，该方法接收的是两个 Index 对象。


```swift
  let linkStr = "欢迎您使用泉通APP！我们高度重视每一位用户的隐私。\n1.为了能够更好的服务广大用户，我们会在您使用业务的过程中根据功能需要，申请必要的用户信息，如设备信息、存储信息、通讯录信息等相关权限。\n2.您可以通过阅读《服务使用协议》和《隐私政策》，了解我们申请相关权限的使用情况。\n如果您点击“同意”则视为您同意《服务使用协议》和《隐私政策》的全部内容。"
        
guard let toIndexObj = linkStr.firstIndex(of: "《") else {
    return
}

let firstIndex = linkStr.distance(from:linkStr.startIndex,to:toIndexObj)
```