## 一、问题现象
今天在将本地 module 添加为项目的 dependency 时，出现了如下的错误：

![image](http://upload-images.jianshu.io/upload_images/2551993-bf420f5a5bea4cc4..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "屏幕截图.png")

该类错误的格式如下：
```
 Error:Unable to resolve dependency for ':app@App名称/compileClasspath': Could not resolve project:module库名称.
```

## 二、问题原因：
刚看到该问题时一脸蒙圈，经过坚持不懈的Google，终于找到了原因和解决方案。

问题原因是：

>**你项目的build.gradle中 buildTypes{} 节点包含的子节点和你依赖的module的 build.type 中 buildType{} 包含的节点不一致**


## 三、解决方案：
解决方案其实很简单：

>* **找到你APP的 build.gradle 文件，将其中的 `buildTypes {}` 节点完整的拷贝到你的module库的 build.gradle 文件中即可。**
>
>* **只要节点数量和名称一致即可，节点中的内容可以不一致**

## 四、解决示例：
我当前项目 build.gradle 的 buildTypes{} 节点如下：
```
  buildTypes {
        debug {
            //内容省略
        }
        release {
            //内容省略
        }
        antTest {
            //内容省略
        }
    }
```

那么，所依赖 moudle 的 build.gradle 的 buildTypes{} 节点如下：

```
  buildTypes {
        debug {
            //可以没有任何内容，只要节点数量和名称一致即可
        }
        release {
            //可以没有任何内容，只要节点数量和名称一致即可
        }
        antTest {
            //可以没有任何内容，只要节点数量和名称一致即可
        }
    }

```

## 五、参考链接：
https://stackoverflow.com/questions/46949622/android-studio-3-0-unable-to-resolve-dependency-for-appdexoptions-compileclas

参考链接中主要内容摘录如下：

![image](http://upload-images.jianshu.io/upload_images/2551993-4d689b99c33f8611..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "屏幕截图.png")

---
本文到此结束，谢谢观看！
**如有不足，敬请指正！**

>**CnPeng 微信公众号上线了！！**
>
>**我们不仅可以聊软件，还可以聊硬件；
我们不仅可以聊技术，还可以聊人生。
这，是一个不羁的公众号。
欢迎扫描下方二维码调戏！**
>
>![](http://upload-images.jianshu.io/upload_images/2551993-d4bb113b7a0bf81d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
