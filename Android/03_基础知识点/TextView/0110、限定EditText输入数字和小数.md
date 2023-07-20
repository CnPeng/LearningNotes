[原文链接 Android EditText设置只能输入数字和小数点](https://blog.csdn.net/u012246458/article/details/73288487)


利用安卓自身的语法

* 1.如果设置只输入数字

```java
edittext.setInputType( InputType.TYPE_CLASS_NUMBER);
```
此时只会弹出数字输入框，符号点击后不会输入到文字框中

* 2.只能输入数字和小数点

```java
edittext.setInputType(InputType.TYPE_CLASS_NUMBER |InputType.TYPE_NUMBER_FLAG_DECIMAL);
```
此时注意：只设置 `InputType.TYPE_NUMBER_FLAG_DECIMAL`是无法实现只能输入数字和小数点的，必须`InputType.TYPE_CLASS_NUMBER` 和`InputType.TYPE_NUMBER_FLAG_DECIMAL`同时设置才可以

* 3.在布局xml文件中写法：

```xml
<EditText
    android:id="@+id/et_total_money_input"
    style="@android:style/Widget.TextView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_alignParentRight="true"
    android:layout_centerVertical="true"
    android:background="@null"
    android:gravity="right"
    android:hint="请输入金额"
    android:inputType="number|numberDecimal"
    android:textColor="@color/font_FF4081"
    android:textSize="12sp"/>
```