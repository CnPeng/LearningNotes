java 中将 Float 数值格式化成以逗号间隔的字符串。

下面的示例代码中使用了 kotlin ，但实际也是调用了 java 的 API 

```kotlin
// 如果只要整数，省略末尾的 .00
val df = DecimalFormat("#,###.00")

// 输出结果为：123,456,789.00
val valueSumStr = df.format(123456789.00F)
```