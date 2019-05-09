##一、问题现象
在 [android:仿微信录制短视频的实现](http://www.jianshu.com/p/353a3830bc3d) 中罗列了我厂APP中 关于 **长按频录制短视频+点按拍照** 的具体实现代码。之前在多种测试机上测试均没有问题，直到某一天，一位同事用他自己的魅族 MX5 手机测试时发现这么一个“很严重”的问题——只能点按拍照，不能长按录制短视频，只要长按就会崩溃！

然后拿过手机连接到电脑上，查看崩溃信息，如下：

![崩溃信息详情](http://upload-images.jianshu.io/upload_images/2551993-8e2a458e0241fd46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##二、分析过程

在上面的图中，我们能够知道崩溃的位置是 stopRecord( ) , 该方法中调用了 MediaRecorder.stop() 方法，问题出在这里。

而在我们的代码中，触发stopRecord( ) 方法结束录制事件时有两种方式，一种是抬手操作（ACTION_UP），一种是录制进度条动画执行完毕(onAnimationEnd())。

我们在上面的图中也能发现，此次崩溃时实际是由CaptureButton 中的 onAnimationEnd( ) 方法中触发了stopRecord( ) 方法。

然后打断点开始 Ddbug，比较尴尬的事情是，Debug的时候完成了录制过程，并没有产生崩溃。此时，脑袋里就产生了这么一个想法：此次崩溃实际应该是由时间差导致的，因为正常运行的时候代码处理的会很快，而Debug的时候会比较耗费时间，既然Debug时不会崩溃，那么基本就可以断定是时间差导致的问题。

然后在CaptureButton 的内部类 RecordRunnable 的 run( )方法 中为ValueAnimator增加动画监听时重写 onAnimationStart() 方法记录动画开始时间，然后在onAnimationEnd() 方法中记录动画结束时间， 然后我们就发现了下面这个情况：

![动画执行时间不足](http://upload-images.jianshu.io/upload_images/2551993-a82800476568db5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
     
这就不对了，通过上面的log我们能看出，从动画开始到结束总共才25ms，也就是说动画瞬时开始又瞬时结束了，明显不正常，因为我们代码中通过 `record_anim.setDuration(10000);` 设置的时长是10S。

然后想破了脑袋，又谷歌了一阵子都没有找到解决方案，然后寻求同事的帮助，然后就又知道了一个比较坑的知识点……

##三、解决方案
最终的解决方案是：
按照如下步骤设置 ”动画程序时长缩放“ 为 1x 即可解决该问题。
>**系统设置--开发者选项--动画程序时长缩放--1x**,

这个值默认就是1x，**如果选择了 “关闭”，就会出现本文中 动画瞬时开启又瞬时关闭的情况！！**

然后在 onAnimationEnd() 中触发 MediaRecorder.stop( ) 时，由于时间过短，MediaRecorder没有接收到有效的Audio/Video数据，然后就抛出了 RuntimeException。这一点在 stop()的源码中有如下描述：
```
  /**
     * Stops recording. Call this after start(). Once recording is stopped,
     * you will have to configure it again as if it has just been constructed.
     * Note that a RuntimeException is intentionally thrown to the
     * application, if no valid audio/video data has been received when stop()
     * is called. This happens if stop() is called immediately after
     * start(). The failure lets the application take action accordingly to
     * clean up the output file (delete the output file, for instance), since
     * the output file is not properly constructed when this happens.
     *
     * @throws IllegalStateException if it is called before start()
     */
    public native void stop() throws IllegalStateException;
```
