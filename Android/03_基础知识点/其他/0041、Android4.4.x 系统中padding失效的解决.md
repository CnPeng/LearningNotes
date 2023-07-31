### 1、复现步骤
在Android 4.4 系统中，

先在 xml 文件中为控件设置 padding， 

然后 java 代码中动态改变该控件的背景，

然后。。。padding失效了。

### 2、问题分析
分析个啥啊，搜索一番。。。然后发现，**在4.4及之前版本中，Java代码中 setBackgroud() 后，xml中的padding必然会失效，这是一个BUG**

* [setBackgroundResource() discards my XML layout attributes](https://stackoverflow.com/questions/5890379/setbackgroundresource-discards-my-xml-layout-attributes)

* [日常记录-代码中Background后Padding 失效](http://www.91yian.com/471.html)

* [TextView中setPadding()方法失效，不起作用](https://www.cnblogs.com/dingzq/p/7997875.html)

### 3、解决方案：

在低版本手机中，setBackground() 后再调用一次 setPadding()

```
if (Build.VERSION.SDK_INT <= Build.VERSION_CODES.LOLLIPOP) {
    //CnPeng 2018/7/27 下午12:08 4.4X 版本setBackground() 之后 xml 中设置的 padding 会失效，所以，此处再手动设置一次
    int paddingLeft = CommonUtils.dip2px(mActivity, 15);
    mBinding.tvCompletionRate.setPadding(paddingLeft, 0, 0, 0);
}
```
**在 Buidl.java 中 Build.VERSION_CODES.LOLLIPOP对应的是API21，在 https://source.android.com/setup/start/build-numbers 中 LOLLIPOP 21 对应的版本号是5.0。但是，实际代码中发现，上面的判断必须是 <= 。否则，在4.4手机中setPadding依旧不生效**

测试手机——华为荣耀4x(che1-CL20)

