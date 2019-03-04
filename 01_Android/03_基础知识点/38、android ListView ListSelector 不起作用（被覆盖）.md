原文地址：https://blog.csdn.net/qq_21063873/article/details/46890853

写代码时，为设置了ListView 设置了ListSelector ，但是发现程序运行不起作用。经过修改才发现问题。

原来是我的ListView 的adapter 是自己写的，

然后在我自己写的adapter里面用的item自己设置了背景。



所以，刷新视图时把selector的效果给覆盖了。



解决办法就是 在ListView中 设置  android:drawSelectorOnTop="true"



确保selector 的刷新次序是在最后