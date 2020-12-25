2020-01-03

内容为中文版《Effective Java第三版》读书笔记，内容并不完全遵循原书顺序。

## 1、`try-with-resources`

`try-with-resources` 是 Java7 中新增的，可用来替代 `try-catch-finally` 。

### (1)、为什么要有 `try-with-resources`

在 Java 中使用 `InputStream` 、`OutputStream` 时，使用完毕之后需要手动调用 `close` 去关闭，但实际使用中我们极有可能会忘记关闭，从而导致内存泄漏等问题。

另外，在使用 `InputStream` 、`OutputStream` 时，为了保证发生异常时也可以关闭，我们通常会使用 `try-catch-finally` 对代码块进行包括，并在 `finally` 中手动调用 `close`, 示例如下：

```java
class TryWithResources {
    private static final int BUFFER_SIZE = 1024 * 8;

    static void copy(String src, String dst) throws IOException {
        InputStream in = new FileInputStream(src);
        try {
            OutputStream out = new FileOutputStream(dst);
            try {
                byte[] buf = new byte[BUFFER_SIZE];
                int n;
                while ((n = in.read(buf)) >= 0) {
                    out.write(buf, 0, n);
                }
            } finally {
                out.close();
            }
        } finally {
            in.close();
        }
    }
}
```
在上述代码中，如果我们使用了多个需要关闭的资源，那么我们可能就需要写多个 `try-finally`，并需要写多个 `close ()`。这样写起来比较繁琐，而且容易忘记调用 `close()`.

### (2)、使用 `try-with-resources`

使用前提是，**被包裹的资源类必须实现 `AutoCloseable` 接口**。目前，java 类库与三方类库中的很多内容已经实现了该接口。

使用格式是：

```java
try (在这里创建需要被关闭的资源对象) {
	//资源对象的正常处理逻辑
}
```

示例1：

```java
 static void copyV2(String src, String dst) throws IOException {
        try (InputStream in = new FileInputStream(src);
             OutputStream out = new FileOutputStream(dst);
        ) {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0) {
                out.write(buf, 0, n);
            }
        }
    }
```

示例2：

```java
static String firstLineOfFile(String path,String defValue) {
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {
            return br.readLine();
        } catch (IOException e) {
            e.printStackTrace();
            return defValue;
        }
    }
```

在上面两个例子中，代码整体变得更加简洁，而且不需要我们再手动调用 `close()` 函数关闭资源，即便发生了异常也可以实现自动关闭。


##2、
