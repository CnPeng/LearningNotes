# 1. 控制EditText不换行

Android EditText，在某些场景下，比如输入用户名，或者输入密码，我们希望限制用户只输入一行，或者不希望文本框换行。

解决方案是：设置 `maxLines="1"`, 并设置 `inputType` 属性，不设置 `inputType` 时 `maxLines` 不生效。

示例：

```xml
<EditText
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:maxLines="1"
    android:inputType="text"
/>
```