# 1. 9-清空map

有 2 种方法清空一个 map：

* 遍历 key 并根据 key 删除键值对

```go
func ClearMap1() {
	tempMap := make(map[string]int)
	tempMap["张三"] = 23
	tempMap["李四"] = 24
	fmt.Printf("%v \n", tempMap) //map[张三:23 李四:24]

	for k := range tempMap {
		fmt.Println(k)     // 打印键值对中的 key
		delete(tempMap, k) // 根据 key 删除元素
	}
	fmt.Printf("%v \n", tempMap) //map[]
}
```

* 直接赋一个新值

```go
func ClearMap2() {
	tempMap := make(map[string]int)
	tempMap["张三"] = 23
	tempMap["李四"] = 24
	fmt.Printf("%v \n", tempMap) //map[张三:23 李四:24] 

	tempMap = make(map[string]int)
	fmt.Printf("%v \n", tempMap) //map[]
}
```