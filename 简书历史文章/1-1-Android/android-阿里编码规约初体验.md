##一、阿里编码规约简介

阿里编码规约是由阿里巴巴 P3C 项目组开发的针对 Java 语言的代码检测插件。

能够在编码时给出编码建议，也能够在代码Review的时候扫描潜在的代码隐患。

该插件已经在 Github 上开源，地址为：https://github.com/alibaba/p3c

##二、AndroidStudio集成阿里编码规约
###（1）、集成步骤示意图

![步骤1：打开通用设置界面](http://upload-images.jianshu.io/upload_images/2551993-126b61b3e19e5540.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![步骤2：切换到 Plugins 菜单并选择底部的 Browse Reposiories](http://upload-images.jianshu.io/upload_images/2551993-ccc2eb6bdf39d88d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




![步骤3：搜索并安装，底部进度条走完之后表示安装成功，此时直接重启即可。如果不确定是否已经安装上，可以直接右键左侧栏中插件名称，在弹出的邮件菜单中查看 DownloadAndInstall 是否为灰色不可点击状态，如果是表示安装完成，直接重启AS即可](http://upload-images.jianshu.io/upload_images/2551993-762bb4507f7eb40e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![步骤4：重启AS之后，在Tools 中能看到 阿里编码规约条目，表示安装成功](http://upload-images.jianshu.io/upload_images/2551993-f2ee3eb03592b3b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##三、简单设置
在上面 步骤4 的图中，我们能看到 阿里编码规约 中共有三个子菜单，分别是：

图标|子菜单名称|含义/功能
--|--|--
![](http://upload-images.jianshu.io/upload_images/2551993-d8d75d61c655ad0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|编码规约扫描|检测代码中的隐藏问题，未选中module时，默认分析当前的文件；如果想分析module，则先在左侧选中，然后点击分析。具体参考` 四 基本使用 `中的动图。
![](http://upload-images.jianshu.io/upload_images/2551993-43fca4c9f6b9de3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|是否开启实时检测|编码过程中是否开启实时检测并提醒，默认开启
![](http://upload-images.jianshu.io/upload_images/2551993-37b944504fb8a720.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|切换语言|支持中文和英文模式，默认与根据当前设备的语言设置同步。切换语言之后需要重启AS才能生效

##四、基本使用

###（1）、问题分类
使用阿里编码规约扫描出来的问题分为三种：

种类|大致含义
--|--
Blocker|Blocker 是较严重的，极易出问题的
Critical|Critical是一般的，可能会出问题的；
Major|Major 是一些规范和优化级别的

####1）、Blocker示例：

![Blocker示例](http://upload-images.jianshu.io/upload_images/2551993-f6529967acd8a8b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上图中，提示我们要用 ScheduledExecutorService 替代 Timer，并给出了原因：Timer运行多个TimeTask任务时，只要其中一个挂掉了，其他的TimeTask也不会执行，而ScheduledExecutorService则不会。

**补充：ScheduledExecutorService 代码示例**
 ``` 
ScheduledExecutorService executorService = new ScheduledThreadPoolExecutor(1, new BasicThreadFactory
                    .Builder().daemon(true).build());
executorService.schedule(new Runnable() {
       @Override
       public void run() {
            //do something
            handler.sendEmptyMessage(PlayAudioOver);
      }
}, 1000, TimeUnit.MILLISECONDS);
```
>在上述代码中,创建一个 executorService , 1000毫秒之后发送handler空消息执行某个任务。这里在引入 BasicThreadFactory 的时候需要添加 dependencies: `    compile 'org.apache.commons:commons-lang3:3.5' `。在上面的Blocker示例的图中，我们能看到除了提示信息之外，还有示例代码，示例代码的第一行给我们指出了 BasicThreadFactory 所处的包

####2）、Critical示例：

![Critical示例](http://upload-images.jianshu.io/upload_images/2551993-ac4743983427eb0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图中，Switch语句中没有default语句，就给出了提示，并指明了所在文件和文件中的位置

####3）、major示例

![major示例](http://upload-images.jianshu.io/upload_images/2551993-3f0bf7d7a833f213.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图中提示说不能出现魔法值，就是未经定义的数值常量或者字符串常量，要求我们在使用前先定义。


###（2）、基本使用示例
####1）、分析当前文件
![分析当前文件：未选中左侧module时，默认分析当前文件。分析完成后，底部会多出一个 Inspection Results ，里面展示了具体的分析结果](http://upload-images.jianshu.io/upload_images/2551993-ed3402e561ece505.gif?imageMogr2/auto-orient/strip)

####2）、分析module

![分析module:先在左侧点击module名称，然后Tools--阿里编码规约--编码规约扫描](http://upload-images.jianshu.io/upload_images/2551993-68fedf394794fb56.gif?imageMogr2/auto-orient/strip)


####3)、分析并修改
下面演示一个分析并修改的案例。

在下面的案例中我们没有选中左侧的module，而是直接点击的分析，那么就会分析当前正在打开的文件——MainActivity.java

另外，由于当前使用的LICEcap ， 无法录制菜单栏，但，我们在`二（1）集成步骤示意图 的 步骤四` 中能够知道 `阿里编码规约` 的入口 是在 `Tools`  菜单中

![分析并修改](http://upload-images.jianshu.io/upload_images/2551993-2cbdc218b611f8fd.gif?imageMogr2/auto-orient/strip)

在上面的动图中，共扫描出了 1个Blocker , 4 个 Critical，60 Majors 。

动图中以修改Critical为例，展开条目后，会提示问题位置的文件和行号，直接双击具体的行号条目会实现跳转，直接跳转到问题位置。如下图：

![双击定位问题位置](http://upload-images.jianshu.io/upload_images/2551993-75bf9c148da5c188.gif?imageMogr2/auto-orient/strip)



将光标放置到上面后会以popup的形式提示你为什么要修改，如果提示过长后面还有一个超链接的 more 字样，点击more 可以查看完整提示信息。如下图：

![查看完整提示信息](http://upload-images.jianshu.io/upload_images/2551993-11036c0c8f833612.gif?imageMogr2/auto-orient/strip)




定位到问题位置后，可以手动 Shift + F6 按照提示批量修改，也可以直接 Alt+Enter 实现自动修复。（注意：此处的快捷键是 MAC 系统下 MAC OX 10.5+模板的快捷键，其他系统或者其他模板的快捷键会有不同）自动修复如下图：

![Alt + Enter 的自动修复提示](http://upload-images.jianshu.io/upload_images/2551993-8e0e984fbe4e5d01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


修改完成之后，在末尾会有  `No longer valid` 字样，表示已经修改完毕了。如下图：

![No longer valid](http://upload-images.jianshu.io/upload_images/2551993-0c064c1bb9851f81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##五、总结
阿里编码规约支持中文，扫描速度快，提示也是中文易于理解。

但是，个人认为扫描准确度和深度较 FindBugs等还有差距。



##六、附录：
参考链接 [https://juejin.im/post/59e2e0bd6fb9a0450d101de9](https://juejin.im/post/59e2e0bd6fb9a0450d101de9)
