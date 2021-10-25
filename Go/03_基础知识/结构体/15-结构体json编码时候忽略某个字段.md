# 1. 15-结构体json编码时候忽略某个字段

[原文：go语言结构体json编码时候忽略某个字段](https://blog.csdn.net/qq_26039331/article/details/119002391)

* 当结构体中某个字段后面带有 `json:"xx,omitempty"` 时，xx 为我们设定的导出字段名，omitempty 表示如果该值为空，则不进行导出编码。
    * 若为int型，则为0的时候，不被编码；若为string、切片等指针类型的，则为nil的时候，不被编码；若为bool型，则为false的时候，不被编码。

* 当结构体中某个字段后面带有 `json:"-"` 时， 将直接忽略该字段，不进行编码。
* 另外，如果某个字段直接以小写字母开头，则该字段表示私有，也不会被编码。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
	Addr string `json:"addr,omitempty"`
	Tab  bool   `json:"-"`
}

func main() {
	p1 := Person{
		Name: "Jack",
		Age:  22,
		Tab:  false,
	}

	data1, err := json.Marshal(p1)
	if err != nil {
		panic(err)
	}
	
	fmt.Printf("%s\n", data1)

	// ================

	p2 := Person{
		Name: "Jack",
		Age:  22,
		Addr: "cjs",
		Tab:  true,
	}

	data2, err := json.Marshal(p2)
	if err != nil {
		panic(err)
	}

	// p2 则会打印所有
	fmt.Printf("%s\n", data2)
}
```

输出结果：

```go
{"name":"Jack","age":22}
{"name":"Jack","age":22,"addr":"cjs"}

```