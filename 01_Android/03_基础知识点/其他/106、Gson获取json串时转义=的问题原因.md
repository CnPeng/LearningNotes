## 一、问题现象


在使用 Gson 将其他类型数据转成 json 字符串时，通常是直接构造一个 Gson, 然后转换，示例如下：

```kotlin
@JvmStatic
fun objToJson(any: Any?): String {
    return Gson().toJson(any)
}
```

但是，这样的话就会出现一种比较特殊的情况：**如果被转换字符串中有 = 或其他符号时，会被转义！！！**

## 二、解决方案

解决方案就是，更换获取 Gson 对象的方法，具体如下：

```kotlin
@JvmStatic
fun objToJson(any: Any?): String {
    return GsonBuilder().disableHtmlEscaping().create().toJson(any)
}
```

`disableHtmlEscaping()` 表示禁用 Html 转义.

## 三、问题原因

直接通过 `Gson()` 获取对象时，其构造方法如下：

```java
  public Gson() {
    this(Excluder.DEFAULT, FieldNamingPolicy.IDENTITY,
        Collections.<Type, InstanceCreator<?>>emptyMap(), DEFAULT_SERIALIZE_NULLS,
        DEFAULT_COMPLEX_MAP_KEYS, DEFAULT_JSON_NON_EXECUTABLE, DEFAULT_ESCAPE_HTML,
        DEFAULT_PRETTY_PRINT, DEFAULT_LENIENT, DEFAULT_SPECIALIZE_FLOAT_VALUES,
        LongSerializationPolicy.DEFAULT, null, DateFormat.DEFAULT, DateFormat.DEFAULT,
        Collections.<TypeAdapterFactory>emptyList(), Collections.<TypeAdapterFactory>emptyList(),
        Collections.<TypeAdapterFactory>emptyList());
  }
```

上述代码中，有一个参数为 `DEFAULT_ESCAPE_HTML`，具体值为 `true` 表示开启 html 转义，所以，在被转换对象中包含 “=” 时就会出现被转义的情况。

当我们调用 `toJson(any)` 时，内部会触发 JsonWriter 中的 `setHtmlSafe(flag)`, 该方法的具体实现如下：

```java
/**
 * Configure this writer to emit JSON that's safe for direct inclusion in HTML
 * and XML documents. This escapes the HTML characters {@code <}, {@code >},
 * {@code &} and {@code =} before writing them to the stream. Without this
 * setting, your XML/HTML encoder should replace these characters with the
 * corresponding escape sequences.
 */
public final void setHtmlSafe(boolean htmlSafe) {
  this.htmlSafe = htmlSafe;
}
```

其方法描述中已经说明了，如果传入的值为 true , 会对 `<、>、&、=` 进行转义。

由于 Gson 类中定义了 `final boolean htmlSafe;`，所以，我们无法修改。然后在 Gson 的 类注释中有介绍可以使用 `GsonBuilder.crater()` 获取对象。

```xml
* Gson gson = new Gson(); // Or use new GsonBuilder().create();
* MyType target = new MyType();
* String json = gson.toJson(target); // serializes target to Json
* MyType target2 = gson.fromJson(json, MyType.class); // deserializes json into target2
```

然后我们恰好发现 GsonBuilder 类中有提供了禁用 html 转义的 `disableHtmlEscaping()`，所以。。。
