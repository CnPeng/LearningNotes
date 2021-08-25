
## 1. 错误示例

下面这种会丢精度！！！

```go
import "fmt"
func main() {
 	var money int
 	money = 1
 	fmt.Println(float64(money) / float64(100))
}
```

## 2. 正确示例


* [go中string、int、float相互转换](https://blog.csdn.net/HYZX_9987/article/details/100172848)
* [Go语言string，int，int64 ,float转换](https://www.cnblogs.com/vdvvdd/archive/2017/07/20/7211122.html)
* [golang float string int 相互转换 保留小数位](https://blog.csdn.net/whatday/article/details/109846374)