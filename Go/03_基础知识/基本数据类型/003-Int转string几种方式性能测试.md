[原文链接](https://blog.csdn.net/flyfreelyit/article/details/79701577)


Go 语言内置 int 转 string 至少有3种方式：

* `fmt.Sprintf("%d",n)`

* `strconv.Itoa(n)`

* `strconv.FormatInt(n,10)`


下面针对这 3 中方式的性能做一下简单的测试：

```go
package gotest

import (
	"fmt"
	"strconv"
	"testing"
)

func BenchmarkSprintf(b *testing.B) {
	n := 10
	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		fmt.Sprintf("%d", n)
	}
}

func BenchmarkItoa(b *testing.B) {
	n := 10
	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		strconv.Itoa(n)
	}
}

func BenchmarkFormatInt(b *testing.B) {
	n := int64(10)
	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		strconv.FormatInt(n, 10)
	}
}
``` 

保存文件为 `int2string_test.go`

执行：

```
go test -v -bench=. int2string_test.go -benchmem
```

结果如下：

```go
goos: darwin
goarch: amd64 
BenchmarkSprintf-8      20000000               114 ns/op              16 B/op          2 allocs/op
BenchmarkItoa-8         200000000                6.33 ns/op            0 B/op          0 allocs/op
BenchmarkFormatInt-8    300000000                4.10 ns/op            0 B/op          0 allocs/op
PASS
ok      command-line-arguments  5.998s
```

总体来说，`strconv.FormatInt()` 效率最高，`fmt.Sprintf()` 效率最低

> Itoa 其实底层就是用 FromatInt 实现的,性能差距可能的来源是多了一层函数调用。


```go
func Itoa(i int) string {
 	return FormatInt(int64(i), 10)
}
```