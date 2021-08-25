# 1. 12-omitempty关键字

[原文：《Golang 的 “omitempty” 关键字略解》](https://old-panda.com/2019/12/11/golang-omitempty/)

## 1.1. 使用场景和基本用法

熟悉 Golang 的朋友对于 json 和 struct 之间的转换一定不陌生，为了将代码中的结构体与 json 数据解耦，通常我们会在结构体的 field 类型后加上解释说明，例如在表示一个地址的时候， json 数据如下所示

```json
{
    "street": "200 Larkin St",
    "city": "San Francisco",
    "state": "CA",
    "zipcode": "94102"
}
```

与之相对应的 Golang 结构体表示定义如下

```go
type address struct {
	Street  string `json:"street"`  // 街道
	Ste     string `json:"suite"`   // 单元（可以不存在）
	City    string `json:"city"`    // 城市
	State   string `json:"state"`   // 州/省
	Zipcode string `json:"zipcode"` // 邮编
}
```

这样无论代码中的变量如何改变，我们都能成功将 json 数据解析出来，获得正确的街道，城市等信息，到目前为止一切正常。

但如果我们想要将地址结构体恢复成 json 格式时，问题就来了。比方说我们用下面这段代码读取了地址 json ，然后根据业务逻辑处理了之后恢复成正常的 json 打印出来

```go
func main() {
  data := `{
		"street": "200 Larkin St",
		"city": "San Francisco",
		"state": "CA",
		"zipcode": "94102"
	}`
	addr := new(address)
	json.Unmarshal([]byte(data), &addr)

    // 处理了一番 addr 变量...

	addressBytes, _ := json.MarshalIndent(addr, "", "    ")
	fmt.Printf("%s\n", string(addressBytes))
}
```

可以得到运行结果

```json
{
    "street": "200 Larkin St",
    "suite": "",
    "city": "San Francisco",
    "state": "CA",
    "zipcode": "94102"
}
```

多了一行 `"suite": "",` ，而这则信息在原本的 json 数据中是没有的（在美国的地址中，如果不是群租公寓或者共享办公楼， suite 这一条不存在很正常，人们直接用街道门牌号来表示地址就足够了），但我们更希望的是，在一个地址有 suite 号码的时候输出，不存在 suite 的时候就不输出，幸运的是，我们可以在 Golang 的结构体定义中添加 `omitempty` 关键字，来表示这条信息如果没有提供，在序列化成 json 的时候就不要包含其默认值。稍作修改，地址结构体就变成了

```go
type address struct {
	Street  string `json:"street"`
	Ste     string `json:"suite,omitempty"`
	City    string `json:"city"`
	State   string `json:"state"`
	Zipcode string `json:"zipcode"`
}
```

重新运行，即可得到正确的结果。

## 1.2. 陷阱

### 1.2.1. 陷阱1-嵌套结构体

带来方便的同时，使用 `omitempty` 也有些小陷阱，一个是**该关键字无法忽略掉嵌套结构体**。

还是拿地址类型说事，这回我们想要往地址结构体中加一个新 field 来表示经纬度，如果缺乏相关的数据，暂时可以忽略。新的结构体定义如下所示

```go
type address struct {
	Street     string     `json:"street"`
	Ste        string     `json:"suite,omitempty"`
	City       string     `json:"city"`
	State      string     `json:"state"`
	Zipcode    string     `json:"zipcode"`
	Coordinate coordinate `json:"coordinate,omitempty"`
}

type coordinate struct {
	Lat float64 `json:"latitude"`
	Lng float64 `json:"longitude"`
}
```

读入原来的地址数据，处理后序列化输出，我们就会发现即使加上了 omitempty 关键字，输出的 json 还是带上了一个空的坐标信息

```json
{
    "street": "200 Larkin St",
    "city": "San Francisco",
    "state": "CA",
    "zipcode": "94102",
    "coordinate": {
        "latitude": 0,
        "longitude": 0
    }
}
```

为了达到我们想要的效果，**`可以把坐标定义为指针类型`**，这样 Golang 就能知道一个指针的“空值”是多少了，否则面对一个我们自定义的结构， Golang 是猜不出我们想要的空值的。

于是有了如下的结构体定义

```go
type address struct {
	Street     string      `json:"street"`
	Ste        string      `json:"suite,omitempty"`
	City       string      `json:"city"`
	State      string      `json:"state"`
	Zipcode    string      `json:"zipcode"`
	Coordinate *coordinate `json:"coordinate,omitempty"`
}

type coordinate struct {
	Lat float64 `json:"latitude"`
	Lng float64 `json:"longitude"`
}
```

相应的输出为

```json
{
    "street": "200 Larkin St",
    "city": "San Francisco",
    "state": "CA",
    "zipcode": "94102"
}
```

### 1.2.2. 陷阱2-默认值

另一个“陷阱”是，**对于用 omitempty 定义的 field ，如果给它赋的值恰好等于默认空值的话，在转为 json 之后也不会输出这个 field **。

比如说上面定义的经纬度坐标结构体，如果我们将经纬度两个 field 都加上 omitempty

```go
type coordinate struct {
	Lat float64 `json:"latitude,omitempty"`
	Lng float64 `json:"longitude,omitempty"`
}
```

然后我们对非洲几内亚湾的“原点坐标”非常感兴趣，于是编写了如下代码

```go
func main() {
	cData := `{
		"latitude": 0.0,
		"longitude": 0.0
	}`
	c := new(coordinate)
	json.Unmarshal([]byte(cData), &c)

        // 具体处理逻辑...

	coordinateBytes, _ := json.MarshalIndent(c, "", "    ")
	fmt.Printf("%s\n", string(coordinateBytes))
}
```

最终我们得到了一个

```json
{}
```

这个坐标消失不见了！但我们的设想是，如果一个地点没有经纬度信息，则悬空，这没有问题，但对于“原点坐标”，我们在确切知道它的经纬度的情况下，（0.0, 0.0）仍然被忽略了。

**正确的写法也是将结构体内的定义改为指针**.

```go
type coordinate struct {
	Lat *float64 `json:"latitude,omitempty"`
	Lng *float64 `json:"longitude,omitempty"`
}
```

这样空值就从 float64 的 0.0 变为了指针类型的 nil ，我们就能看到正确的经纬度输出。

```json
{
    "latitude": 0,
    "longitude": 0
}
```