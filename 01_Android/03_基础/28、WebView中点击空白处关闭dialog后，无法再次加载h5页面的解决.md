## 一、问题现象

通过WebView加载H5页面，安卓端监测网络变化，如果网络状态发生变化将当前网络状态和网络类型传递给H5，由H5调用 onJsAlert() , onJsConfirm().

安卓端设置 WebChromeClient,重写其中的 onJsAlert()、onJsConfirm(),在这两个方法中弹出AlertDialog给用户展示相应的提示信息。

之前，AlertDialog中没有设置 setCancelable()，所以点击 alertDialog之外的区域也可以关闭弹窗。但是——

点击空白处或者返回键关闭了alertDialog 之后，返回到列表界面，再次点击长文条目进入详情页面，将无法通过webView将H5页面加载出来！！！！！

## 二、解决方案

### 1、问题分析
反复测试发现，如果点了alertDialog中的按钮关闭了alertDialog之后，可以再次从列表中进入详情页面，展示也正常。

如果点击alertDialog外部的区域则会百分百的复现上面的问题！！！

### 2、解决思路
根据上面的分析，思路就是强制用户必须点击按钮才能关闭按钮。

### 3、解决方案
为AlertDialog设置 setCancelable(false)

## 三、问题原因
...不晓得啥子原因啊。。。。。！！！