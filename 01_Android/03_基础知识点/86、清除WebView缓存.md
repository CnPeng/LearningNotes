### 1、如何清除缓存 
清除 WebView 缓存时直接调用 ：`mWebView.clearCache(true)`


### 2、确认是否已经清除

以 JSDK 的方式将对应的方法暴露给 H5 ，然后让 H5 写个 测试 DEMO 调用，比如，在 Web 页面中定义一个 Button ，点击 Button 调用原生 清理缓存的方法。

原生通过 WebView 加载了测试页面之后，在 Chrome 浏览器中按如下步骤操作：

* 打开调试工具1

![开启调试工具](https://images.gitee.com/uploads/images/2019/0322/115417_f920faa1_930142.png "屏幕截图.png")


* 打开调试工具2

![链接设备](https://images.gitee.com/uploads/images/2019/0322/115557_575fe6b3_930142.png "屏幕截图.png")

* 调试指定设备

![链接设备2](https://images.gitee.com/uploads/images/2019/0322/115755_a81ec160_930142.png "屏幕截图.png")

* 切换到 netWork 选项卡，点击左侧刷新按钮

如果没有缓存，会显示下图状态

![没有缓存请求网络的情况](https://images.gitee.com/uploads/images/2019/0322/120009_596ad271_930142.png "屏幕截图.png")

* 刷新页面后从缓存加载的状态

如果有缓存，会显示下图状态。

![读取缓存内容](https://images.gitee.com/uploads/images/2019/0322/120104_a0cb7c89_930142.png "屏幕截图.png")


点击测试页面中请求缓存的　Button ， 然后点击上图中的 刷新 按钮，如果显示的不是 `from memory cache` 即表示缓存清理成功。　


### 3、注意事项

我们在编写 JSDK 方法时，需要使用 `@JavascriptInterface` 对方法进行标记，其中的内容并不是运行在 主线程中，而 `mWebView.clearCache(true)` 需要运行在主线程中，所以，我们必须在该 JSDK 方法中通过 hanlder 去触发 `mWebView.clearCache(true)`.


