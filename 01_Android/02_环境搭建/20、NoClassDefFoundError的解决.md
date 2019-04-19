* 错误信息：

 java.lang.NoClassDefFoundError:

![AHiUjP.png](https://s2.ax1x.com/2019/04/11/AHiUjP.png)

* 复现步骤

在 AndroidStudio 中修改 包名之后，直接编译或运行。

然后安装到手机上之后，无法打开 APP （注意：异常现象会根据调用被修改包中内容的时机不同而不同）

* 解决方案：

清除编译缓存，具体如下：

![AHFeUg.png](https://s2.ax1x.com/2019/04/11/AHFeUg.png)
