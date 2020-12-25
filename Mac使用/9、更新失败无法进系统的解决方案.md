>提前说明：这种方式虽然是重新安装了系统，但是不会清数据。而且会直接安装到最新版本

每天早上到公司之后，先打开电脑，然后点选右上角MAC系统更新的推送，将更新时间选为“明天”，这种操作持续了到底多长时间我也不记得了。直到昨天，我竟然点了一个“立即更新”，然后重启，然后等待，然后就看到了下图中的悲惨界面。

![](http://upload-images.jianshu.io/upload_images/2551993-e4b49caed81a29fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

按照图中提示，点击 Restart 之后重启电脑，会重新走更新的过程，但是更新一段时间之后还是会出现这个错误解面。再重启依旧会报错，如此反复。

请教了各处小伙伴之后，最终的解决方案如下：：
#####1、点击 Restart 之后 立马 按下 CMD + R （不要松开） ，然后等待，
#####2、等出来 MAC 的那个 白苹果 之后松开组合键，再次等待，直到出现下图
![](http://upload-images.jianshu.io/upload_images/2551993-5235a97988950fdd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#####3、选择上图中的  “重新安装 macOS” , 并点击 “继续” 如下图：
![](http://upload-images.jianshu.io/upload_images/2551993-0da3c81c9b169e7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#####4、之后的步骤都是按照屏幕提示点击下一步即可。
![](http://upload-images.jianshu.io/upload_images/2551993-6ab1f3044d516708.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
再往后的图片忘了拍了，一直就是下一步下一步同意之类的。图就不再补了

#####5、开始安装之后就一直等着，等着就好，我等了将近三个小时。。。
安装之前我一直担心会不会丢失数据，因为最近项目太忙，写的代码都只是提交到了本地仓库并没有向远程提交。不过，比较幸运的是，按照这种方式安装之后并不会丢失数据


>补充：
更新完毕之后 在 AndroidStudio 的终端中输入 git 命令时会提示：
 `xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun` , 此时 git  无法使用，解决方案是：在终端中执行如下安装命令  `xcode-select --install`。
详细参考：http://elfxp.com/mac-xcrun-error

本文到此结束，谢谢观看！
**如有不足，敬请指正！**

---

>**CnPeng 微信公众号上线了！！**
>
>**我们可以聊聊软件开发，说说计算机硬件维护，侃侃历史知识，谈谈人生感悟。当然，我们还可以聊点其他的——具体你懂的。**
>
>**欢迎扫描下方二维码关注!**
>
>![](http://upload-images.jianshu.io/upload_images/2551993-d4bb113b7a0bf81d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



