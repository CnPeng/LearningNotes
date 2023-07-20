## 一、使用Toolbar的步骤

* 确保 Activity 扩展 AppCompatActivity：

```kotlin
    class MyActivity : AppCompatActivity() {
      // ...
    }
```    

* 清单文件中修改 <application> 的主题为 appcompat 中的任意 NoActionBar 主题

```xml
<!--使用 NoActionBar 主题可以防止应用使用原生 ActionBar 类提供应用栏-->
<application
    android:theme="@style/Theme.AppCompat.Light.NoActionBar"
 />
```    

* 向 Activity 的布局添加一个 Toolbar。

以下布局代码可以添加一个 Toolbar 并使其浮动在 Activity 之上

```xml
 <android.support.v7.widget.Toolbar
     android:id="@+id/my_toolbar"
     android:layout_width="match_parent"
     android:layout_height="?attr/actionBarSize"
     android:background="?attr/colorPrimary"
     android:elevation="4dp"
     android:theme="@style/ThemeOverlay.AppCompat.ActionBar"
     app:popupTheme="@style/ThemeOverlay.AppCompat.Light"/>  
```




## 参考资料

[https://developer.android.com/training/appbar/setting-up](https://developer.android.com/training/appbar/setting-up)

[http://yifeng.studio/2016/10/12/android-toolbar/](http://yifeng.studio/2016/10/12/android-toolbar/)


[https://www.jianshu.com/p/79604c3ddcae](https://www.jianshu.com/p/79604c3ddcae)

