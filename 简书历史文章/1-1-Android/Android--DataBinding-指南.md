
DataBinding 是 Android 官方提供的一种对 MVVM 的实现。

借助于 DataBinding 能够精简我们的代码，使我们的代码逻辑更为清晰。

有了 DataBinding 我们就可以抛弃 黄油刀，抛弃 findViewByID ; 有了 DataBinding 我们就能实现数据变化时自动刷新界面；有了 DataBinding 我们就能直接在布局文件中绑定事件；有了 DataBinding 我们能够更好的解耦。总之， 使用 DataBinding 之后的感受就是：一时使用一时爽，一直使用一直爽！！

早期的版本中，排错比较费劲，但是，从 3.1 版本的 gradle 插件开始，DataBinding 更换了新的编译器，使用了增量编译，既提高了编译速度，也让排错变得简单。所以——

不论你之前是否接触过 DataBinding ，强烈推荐你看一下这篇文章。相信你看完之后，一定会爱上它。

这篇文章是我在 19 年 3 月份基于 Android 官方文档的翻译整理版本，由于个人水平有限，难免会有不足的地方，欢迎指正。

由于原文内容比较长，不方便编辑，所以，烦请点击如下链接查看：

[Android DataBinding 指南](https://github.com/CnPeng/LearningNotes/blob/master/01_Android/04_%E8%BF%9B%E9%98%B6%E7%9F%A5%E8%AF%86%E7%82%B9/03_Jetpack/01%E3%80%81DataBinding%E5%AE%98%E6%96%B9%E6%8C%87%E5%8D%97.md#4)


后期，篇幅较长的内容会优先发布到 GitHub, 并在 Gitee (码云) 做同步：

*   https://github.com/CnPeng/LearningNotes

*   https://gitee.com/CnPeng_1/LearningNotes

所以，建议你不妨关注一下这两个仓库😁

> 如果有不明白的内容或者我表述有误，欢迎留言探讨。也可以添加 QQ：893612134 或 微信： P893612134。或者关注我的公众号：CnPeng , 这样就可以及时收到更新推送~\(≧▽≦)/~
