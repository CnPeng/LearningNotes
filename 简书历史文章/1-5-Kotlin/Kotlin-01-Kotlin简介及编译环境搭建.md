![kotlin](http://upload-images.jianshu.io/upload_images/2551993-07c250dcbb9fa03c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##一、Kotlin简介
Kotlin是一个基于JVM的新的编程语言，由JetBrains开发。

JetBrains，作为目前广受欢迎的Java IDE IntelliJ的提供商，在Apache许可下已经开源其Kotlin编程语言。

与Java相比，Kotlin的语法更简洁、更具表达性，而且提供了更多的特性，比如，高阶函数、操作符重载、字符串模板。它与Java高度可互操作，可以同时用在一个项目中。


(以上内容来自于度娘)
##二、为什么要学Kotlin

首先放一张kotlin中文站的截图：
![kotlin中文站的截图.png](http://upload-images.jianshu.io/upload_images/2551993-f69dfb143e40312f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

瞅见没，简介+安全+互操作性+工具友好性。

当然，学习kotlin的原因还在于下面这张图：
![我们用kotlin可以做什么？](http://upload-images.jianshu.io/upload_images/2551993-b5026e9c0b973226.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看看，kotlin能写Android，能写Script，能写JS，能写SpringBoot（我不知道这是啥，看着很厉害的样子），还能写Native，似乎有一同江湖的节奏啊。

另外，在2017 google/IO 大会上，kotlin被扶正成为Android开发的官方语言，在我个人看来，在Android领域取代java只是早晚的事情了。当 kotlin 取代java的那一天，Google就再也不用和ORACLE 就 java的使用权问题扯皮了。

##三、Kotlin编译环境

###（一）、kotlin的编译环境有哪些？
好了，我们知道Kotlin是啥玩意了，也知道它有多么🐂 牛掰了，那么，接下来我们就看看如何搭建kotlin编译环境。

再来一张官网截图：
![kotlin支持的编译工具.png](http://upload-images.jianshu.io/upload_images/2551993-06ff7ab79123d49e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上图中，我们可以得知，编译kotlin时可以使用 [Intellij IDEA](https://www.jetbrains.com/idea/)、[AndroidStudio](https://developer.android.google.cn/studio/index.html)、[Eclipse](http://www.eclipse.org/downloads/)、[Complier ](https://github.com/JetBrains/kotlin/releases/tag/v1.1.2-2)。

* Intellij IDEA 中已经集成了 Kotlin 编译环境
* AndroidStudio 3.0 也集成了 Kotlin 编译环境
* Eclipse 需要安装对应的 kotlin 插件才可以
* Complier 是纯命令行编译环境

对应的下载页面分别为：

编译器|下载地址
--|--
 Intellij IDEA    | https://www.jetbrains.com/idea/
AndroidStudio   | https://developer.android.google.cn/studio/index.html
Eclipse|http://www.eclipse.org/downloads/
Complier|https://github.com/JetBrains/kotlin/releases/tag/v1.1.2-2

###（二）、Kotlin编译环境搭建

>说明：当前已经测试了 Intellij IDEA 、Eclipse、AndroidStudio，暂时未测试Complier ，所以暂时不写 Complier 的使用介绍，后期使用过后会及时更新该文档。

#### 1、Intellij IDEA 编译环境搭建

![官网下载界面](http://upload-images.jianshu.io/upload_images/2551993-62fb43b237ecc7e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载及安装的过程省略。这里需要说明的是，正如上图中我们看到的，IEDA分为 Ultimate 旗舰版 和 Community 社区版，前者主要针对 Web 和 企业级 开发，是收费的，试用期一个月；社区版是免费开源的，所以，学习阶段可以直接下载社区版的。

![初次开启](http://upload-images.jianshu.io/upload_images/2551993-a2d860b36089ee53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图中编号的含义分别如下：
* 1、创建一个新的项目
* 2、导入已有项目
* 3、打开本地项目
* 4、从版本控制软件中拉取

我们选择1 新建一个项目，然后按照下图操作：
![新建kotlin项目](http://upload-images.jianshu.io/upload_images/2551993-392c2b4df4e1f98f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![创建项目名称并选择路径](http://upload-images.jianshu.io/upload_images/2551993-da093607b7be1e52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![正在初始化](http://upload-images.jianshu.io/upload_images/2551993-5fe0aefa6ef40760.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![新建kotlin文件](http://upload-images.jianshu.io/upload_images/2551993-a5c9703668c334f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![为文件命名](http://upload-images.jianshu.io/upload_images/2551993-34d1db9896b39dd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![编辑内容](http://upload-images.jianshu.io/upload_images/2551993-8fb217d52297201b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![运行程序](http://upload-images.jianshu.io/upload_images/2551993-0f0c09612bcc40e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![大功告成](http://upload-images.jianshu.io/upload_images/2551993-a71fca9189cf1f9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2、Eclipse 编译环境构建
Eclipse 的下载及安装步骤省略，需要主要的是：Eclipse 需要配合JDK使用，同时必要的话需要配置环境变量，步骤省略，不懂的可留言，我可以单独回复。后期时间充足时我可能会补充环境变量配置的方法

附：[JDK下载地址](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 

下面开始讲解如果搭建Eclipse编译环境：

![ECS开启中](http://upload-images.jianshu.io/upload_images/2551993-1cc99839b05f917c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![选择工作空间--其实就是找个地方放你的代码文件](http://upload-images.jianshu.io/upload_images/2551993-54f41d28fbedd970.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![引导页](http://upload-images.jianshu.io/upload_images/2551993-fcb0d3ada5baef72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Eclipse 对kotlin的支持是通过插件来实现的，下载插件时需要进入 marketplace ,具体参照下图：
![进入 marketplace](http://upload-images.jianshu.io/upload_images/2551993-d2f9be6b7fded115.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![marketplace初始化中.png](http://upload-images.jianshu.io/upload_images/2551993-258a82a7d1c63120.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![搜索并安装kotlin 插件.png](http://upload-images.jianshu.io/upload_images/2551993-226ac847a893c733.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

安装过程中会弹出许可提示，记得选择接受

![安装中](http://upload-images.jianshu.io/upload_images/2551993-a03b491d96884b84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![安装完成提示重启](http://upload-images.jianshu.io/upload_images/2551993-5388282e4a217cb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![kotlin插件安装成功](http://upload-images.jianshu.io/upload_images/2551993-bd6bd101441c8f54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![创建kotlin项目](http://upload-images.jianshu.io/upload_images/2551993-c8bb01948f1637ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![为项目命名并选择项目存储路径](http://upload-images.jianshu.io/upload_images/2551993-356e024b098c1218.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![项目创建成功](http://upload-images.jianshu.io/upload_images/2551993-33c92db1e396effb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![创建kotlin文件](http://upload-images.jianshu.io/upload_images/2551993-a61b09ff259513d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![输入kotlin文件名称](http://upload-images.jianshu.io/upload_images/2551993-321e444b3ae8c1eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![文件创建完成并输入代码](http://upload-images.jianshu.io/upload_images/2551993-a5f4811d0bc5c2d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![空白处右击开始运行](http://upload-images.jianshu.io/upload_images/2551993-1244e3effbe5f5ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![运行时提示保存](http://upload-images.jianshu.io/upload_images/2551993-462a0d7253b3f155.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![查看运行结果](http://upload-images.jianshu.io/upload_images/2551993-b17c10920fc0deb6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


####3、AndroidStudio 编译环境的搭建
安装步骤省略，下载地址前面已经有说明。
需要注意的是：AndroidStudio 的编译也需要JDK ，但是在下载AndroidStudio的时候可以选择带JDK版本的。

![打开AndroidStudio并创建一个新的AndroidStudio项目 ](http://upload-images.jianshu.io/upload_images/2551993-5f0b5d21806273d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![输入项目名称、选择项目路径、勾选kotlin支持](http://upload-images.jianshu.io/upload_images/2551993-60794dbaf079bf0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![选择设备](http://upload-images.jianshu.io/upload_images/2551993-206909e1e89d1c09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![创建空页面.png](http://upload-images.jianshu.io/upload_images/2551993-688ba24da470605c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![输入页面名称](http://upload-images.jianshu.io/upload_images/2551993-88e19f8da98c48ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![创建完成，展示自动生成的kotlin代码](http://upload-images.jianshu.io/upload_images/2551993-937263be9abcb370.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

到此，基于AndroidStudio的kotlin编译环境搭建成功。
>关于如何在AndroidStudio 中编写kotlin代码，后面再单独说。现在重点介绍的是kotlin的基本语法类内容。


本篇文章到此结束。

=================================================

**CnPeng 微信公众号上线了！！**

**我们可以聊聊软件开发，说说计算机硬件维护，侃侃历史知识，谈谈人生感悟。当然，我们还可以聊点其他的——具体你懂的。**

**欢迎扫描下方二维码关注！**
![扫码关注](http://upload-images.jianshu.io/upload_images/2551993-da7a61b3bf8be04d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

文末福利--扫码领支付宝红包，领完记得使用哈☺
![扫码领支付宝红包](http://upload-images.jianshu.io/upload_images/2551993-e1f1d25efd4c14b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


