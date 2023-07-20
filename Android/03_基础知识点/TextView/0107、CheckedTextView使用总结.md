CheckedTextView 实现文本居中时，使用 `android:gravity="center"` 无效，需要使用 `android:textAlignment="center"`, 示例如下：

```xml
<CheckedTextView
    android:id="@+id/ctv_btmTab"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:checkMark="@null"
    android:checked="true"
    android:textAlignment="center"/>
```

**`textAlignment` 属性是在 View 类中定义的**