# 0001-testing中需要context时怎么处理

在 Golang 的 `testing` 包中，通常情况下不需要使用 `context.Context`。因为测试用例的执行时间比较短，不会出现超时等问题。

但是如果你确实需要在测试中使用 `context.Context`，可以通过以下方式处理：

在测试函数中传入 `context.Context` 参数

```go
Copyfunc TestMyFunction(t *testing.T) {
    ctx := context.Background()
    // ... some code ...
}
```

使用 `context.WithTimeout` 等方法创建带有超时控制的 Context：

```go
Copyfunc TestMyFunction(t *testing.T) {
    ctx, cancel := context.WithTimeout(context.Background(), time.Second)
    defer cancel()

    // ... some code ...
}
```

使用 `t.Cleanup` 方法在测试结束后取消 Context


```go
Copyfunc TestMyFunction(t *testing.T) {
    ctx, cancel := context.WithTimeout(context.Background(), time.Second)
    defer cancel()

    t.Cleanup(func() {
        // 在测试结束后取消 Context
        cancel()
    })

    // ... some code ...
}
```

以上就是在 Golang 的 `testing` 包中使用 `context.Context` 的常见方式。