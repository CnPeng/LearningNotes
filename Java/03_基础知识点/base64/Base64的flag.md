# 1. Base64的flag

在 Java 中，`java.util.Base64` 类提供了对 Base64 编码和解码的支持。

该类定义了以下几个常量作为参数传递给 `Base64.getEncoder()` 和 `Base64.getDecoder()` 方法的 flag：

* `Base64.DEFAULT`：使用默认的 Base64 编码行为。
* `Base64.NO_PADDING`：禁用填充字符（=）的使用。
* `Base64.NO_WRAP`：禁用换行符（\r 和 \n）的插入。
* `Base64.CRLF`：输出换行符（\r\n）。
* `Base64.URL_SAFE`：使用 URL 和文件名安全的 Base64 字符集。

这些 flag 影响编码或解码过程中的特定行为和输出格式。你可以根据自己的需求选择合适的 flag。

下面是一个示例代码，演示了如何在 Java 中使用不同的 flag 进行 Base64 编码和解码：

```java
import java.util.Base64;

public class Base64Example {
    public static void main(String[] args) {
        // 原始字符串
        String originalString = "Hello, World!";

        // 使用 DEFAULT flag 进行编码
        String encodedStringDefault = Base64.getEncoder().encodeToString(originalString.getBytes());
        System.out.println("Encoded String (DEFAULT): " +  encodedStringDefault);

        // 使用 NO_PADDING flag 进行编码
        String encodedStringNoPadding = Base64.getEncoder().withoutPadding().encodeToString(originalString.getBytes());
        System.out.println("Encoded String (NO_PADDING): " +  encodedStringNoPadding);

        // 使用 NO_WRAP flag 进行编码
        String encodedStringNoWrap = Base64.getEncoder().withoutWrap().encodeToString(originalString.getBytes());
        System.out.println("Encoded String (NO_WRAP): " +  encodedStringNoWrap);

        // 使用 CRLF flag 进行编码
        String encodedStringCRLF = Base64.getMimeEncoder().encodeToString(originalString.getBytes());
        System.out.println("Encoded String (CRLF): " +  encodedStringCRLF);

        // 使用 URL_SAFE flag 进行编码
        String encodedStringURLSafe = Base64.getUrlEncoder().encodeToString(originalString.getBytes());
        System.out.println("Encoded String (URL_SAFE): " +  encodedStringURLSafe);

        // 使用 DEFAULT flag 进行解码
        byte[] decodedBytesDefault = Base64.getDecoder().decode(encodedStringDefault.getBytes());
        String decodedStringDefault = new String(decodedBytesDefault);
        System.out.println("Decoded String (DEFAULT): " +  decodedStringDefault);
    }
}
```
运行上述代码，你将看到使用不同的 flag 进行 Base64 编码和解码的结果。根据需要选择适合的编码和解码方式。