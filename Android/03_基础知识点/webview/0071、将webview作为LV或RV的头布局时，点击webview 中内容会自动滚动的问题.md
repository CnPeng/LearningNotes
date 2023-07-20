### 1、原文内容
Recyclerview 添加webview为header 点击webview 会自动滚动问题

https://blog.csdn.net/blueZhangFun/article/details/79397477

### 2、项目中的实际问题
长文详情页面ArticleDetailsActivity中，内容详情模块使用的是WebView，评论模块使用的原生。

整体是一个LV，WebView作为头布局。

点击详情中的图片跳转到原生的图片查看框架中。

点击详情中的视频，跳转到原生的视频播放界面中。

但是，关闭原生的播放视频界面再回到详情页面时，默认会滚动到LV的1索引条目位置——头布局为0索引，第一个条目就是1索引；也就是说，会滚动WebView的最底端，而不是固定在之前的位置。

**目前的解决方案就是 上述文章中描述的为 webView设置焦点状态为false ,即 webView.setFoucusable(false)** 
