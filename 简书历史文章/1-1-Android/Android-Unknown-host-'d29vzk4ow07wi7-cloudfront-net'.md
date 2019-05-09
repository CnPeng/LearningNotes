昨天公司网络莫名挂掉了，等运维小伙伴调整好了之后，AndroidStidio 就不能正常的 SyncProject 了，一直提示 `Unknown host 'd29vzk4ow07wi7.cloudfront.net'`。但此时VPN可以正常访问谷歌，项目中也没有修改gradle内容。所以，直接搜索——



## 1、未解决问题的方案
以下是搜索之后查看到的别人的解决方案，但是，对我并没有生效。虽然没生效，也做一个整理，万一以后哪天再碰见了这个问题，可以不用绕弯路。

### (1)、取消离线模式
Preferences > Gradle > 取消Offline work 
>![](https://upload-images.jianshu.io/upload_images/2551993-7db4e5fa5ae3a9f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### (2)、手动下载gradle.zip
* 手动下载Gradle文件 ，然后放置到gradle文件夹下
>![](https://upload-images.jianshu.io/upload_images/2551993-5068402f1b5526d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### (3)、设置AndroidStudio代理
参考1：https://blog.csdn.net/lintianlin/article/details/81868964
参考2：https://blog.csdn.net/magicbaby810/article/details/79848425

思路是，如果使用了 代理软件/VPN ，可以先从代理软件或者网络高级设置中查看代理地址，然后将对应的代理地址设置给AndroidStudio。

* 本机代理

![](https://upload-images.jianshu.io/upload_images/2551993-ceb0f6997a153a12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 为AndroidStudio设置代理

![](https://upload-images.jianshu.io/upload_images/2551993-36692da165774c94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### (4)、更改maven仓库地址
在 project的 build.gradle 文件中更改Maven地址为 阿里云镜像仓库, 参考链接：http://www.yrom.net/blog/2015/02/07/change-gradle-maven-repo-url/

```
allprojects {
    repositories {
        maven{ url '[http://maven.aliyun.com/nexus/content/groups/public/](http://maven.aliyun.com/nexus/content/groups/public/)'}
    }
}
```

### (5)、 Project的build.gradle文件中新增 mavencenter( ) 
Project的build.gradle文件中新增 mavencenter( ) 
>jcenter()前添加上mavenCentral();
参考链接：https://blog.csdn.net/ZDF123654/article/details/81699249


## 2、解决了问题的方案

尝试上述方法之后依旧不能生效，遂求助于CTO，在CTO的帮助下，终于解了这个问题，最终方案为：**配置本机DNS**
![](https://upload-images.jianshu.io/upload_images/2551993-7994b10d37c10176.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>关于8.8.8.8 /114.114.114.114 DNS地址 
>* 114.114.114.114和8.8.8.8，这两个IP地址都属于公共域名解析服务DNS其中的一部分，而且由于不是用于商业用途的，这两个DNS都很纯净，不用担心因ISP运营商导致的DNS劫持等问题，而且都是免费提供给用户使用的。
>
>* 114.114.114.114是国内移动、电信和联通通用的DNS，手机和电脑端都可以使用，干净无广告，解析成功率相对来说更高，国内用户使用的比较多，而且速度相对快、稳定，是国内用户上网常用的DNS。
>
>* 8.8.8.8是GOOGLE公司提供的DNS，该地址是全球通用的，相对来说，更适合国外以及访问国外网站的用户使用。`注意：国内用户配置该DNS后网络可能会变得稍慢`
>
>该内容摘录自：https://zhidao.baidu.com/question/304558434158495364.html
