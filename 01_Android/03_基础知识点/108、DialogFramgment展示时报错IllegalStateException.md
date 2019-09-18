该问题摘自 Buggly

```
#83834 java.lang.IllegalStateException
Can not perform this action after onSaveInstanceState

com.zjelite.antlinkercampus.home.MainActivity.showAdPopWindow(MainActivity.java:514)
```

我在显示fragment的代码中使用了：

```java
fragment.show(getSupportFragmentManager, fragment.toString());
```
而这里是因为两次toString()结果不同，导致不同的tag指向的是同一个fragment。
获取fragment的tag的正确方法应该是使用其提供的fragment.getTag()方法。