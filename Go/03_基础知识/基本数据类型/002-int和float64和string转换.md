## 1. xx转换为string

### 1.1. int转string

```go
s := strconv.Itoa(i)
// 与上一行代码等效
s := strconv.FormatInt(int64(i), 10)
```
### 1.2. int64转string

```go
i := int64(123)
s := strconv.FormatInt(i, 10)
```

在 `strconv.FormatInt(i, 10)` 中，第二个参数为基数，可选 2~36

注意：对于无符号整形，可以使用 `FormatUint(i uint64, base int)`

### 1.3. float转string

```go
v := 3.1415926535
s1 := strconv.FormatFloat(v, 'E', -1, 32)//float32
s2 := strconv.FormatFloat(v, 'E', -1, 64)//float64

//转换为字符串，并保留3位小数，会自动四舍五入
s_score := fmt.Sprintf("%.3f", f_score)
```

FormatFloat 的定义如下：

```go
// FormatFloat converts the floating-point number f to a string,
// according to the format fmt and precision prec. It rounds the
// result assuming that the original was obtained from a floating-point
// value of bitSize bits (32 for float32, 64 for float64).
//
// The format fmt is one of
// 'b' (-ddddp±ddd, a binary exponent),
// 'e' (-d.dddde±dd, a decimal exponent),
// 'E' (-d.ddddE±dd, a decimal exponent),
// 'f' (-ddd.dddd, no exponent),
// 'g' ('e' for large exponents, 'f' otherwise),
// 'G' ('E' for large exponents, 'f' otherwise),
// 'x' (-0xd.ddddp±ddd, a hexadecimal fraction and binary exponent), or
// 'X' (-0Xd.ddddP±ddd, a hexadecimal fraction and binary exponent).
//
// The precision prec controls the number of digits (excluding the exponent)
// printed by the 'e', 'E', 'f', 'g', 'G', 'x', and 'X' formats.
// For 'e', 'E', 'f', 'x', and 'X', it is the number of digits after the decimal point.
// For 'g' and 'G' it is the maximum number of significant digits (trailing
// zeros are removed).
// The special precision -1 uses the smallest number of digits
// necessary such that ParseFloat will return f exactly.
func FormatFloat(f float64, fmt byte, prec, bitSize int) string {
	return string(genericFtoa(make([]byte, 0, max(prec+4, 24)), f, fmt, prec, bitSize))
}
```

上述注释部分翻译之后如下:

```go
// FormatFloat 将浮点数 f 转换为字符串值
// f：要转换的浮点数
// fmt：格式标记（b、e、E、f、g、G）
// prec：精度（数字部分的长度，不包括指数部分）

// bitSize：指定浮点类型（32:float32、64:float64）

// 格式标记：
// 'b' (-ddddp±ddd，二进制指数)
// 'e' (-d.dddde±dd，十进制指数)
// 'E' (-d.ddddE±dd，十进制指数)
// 'f' (-ddd.dddd，没有指数)
// 'g' ('e':大指数，'f':其它情况)
// 'G' ('E':大指数，'f':其它情况)

// 如果格式标记为 'e'，'E'和'f'，则 prec 表示小数点后的数字位数
// 如果格式标记为 'g'，'G'，则 prec 表示总的数字位数（整数部分+小数部分）
str1 = strconv.FormatFloat(11.34,'E',-1,32)
str2 = strconv.FormatFloat(10.55,'E',-1,64)

//1.134E+01  1.055E+01
fmt.Println(str1,str2)

//解析转换后的string变量str为float
h,_ :=strconv.ParseFloat(str1,32)
//11.34000015258789
fmt.Println(h)

h,_ =strconv.ParseFloat(str2,64)
//10.55
fmt.Println(h)
```



## 2. string转换为xx

### 2.1. string转int

```go
i, err := strconv.Atoi(s)
```

### 2.2. string转int64

```go
i, err := strconv.ParseInt(s, 10, 64)
```

第二个参数为基数（2~36），即表示结果为几进制数据

第三个参数位大小表示期望转换的结果类型，其值可以为0, 8, 16, 32和64，分别对应 int, int8, int16, int32和int64

### 2.3. string转float

```go
s := "3.1415926535"
v1, err := strconv.ParseFloat(v, 32)
v2, err := strconv.ParseFloat(v, 64)
```

## 3. 错误示例

下面这种会丢精度！！！

```go
import "fmt"
func main() {
 	var money int
 	money = 1
 	fmt.Println(float64(money) / float64(100))
}
```

## 4. 参考文章

* [go中string、int、float相互转换](https://blog.csdn.net/HYZX_9987/article/details/100172848)
* [Go语言string，int，int64 ,float转换](https://www.cnblogs.com/vdvvdd/archive/2017/07/20/7211122.html)
* [golang float string int 相互转换 保留小数位](https://blog.csdn.net/whatday/article/details/109846374)