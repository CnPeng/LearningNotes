代码原贴：https://zhuanlan.zhihu.com/p/32754893
GitHub地址：https://github.com/lingfengsan/MillionHero
视频地址：https://www.bilibili.com/video/av18152515/#page=2


采坑记录：
##1、tessOCR类 java.lang.UnsatisfiedLinkError: 找不到指定的模块

原因：没有安装tesseract 。
MAC下安装方法：
https://tonydeng.github.io/2016/07/28/on-the-use-of-tesseract-picture-text-recognition/
Win和Linux安装方法：
http://xiaosheng.me/2015/12/18/article10/

##2、 I/O error reading PNG header!
截图时设置的时间不足，截图未完成，就开始识别，但是实际图片中没有内容，所以会报错。
出错代码块：
Phone 类的 getImage() 方法中：

```
  //截屏存到手机本地
        try {
            while (!curPhoto.exists()) {
                Runtime.getRuntime().exec(ADB_PATH
                        + " shell /system/bin/screencap -p /sdcard/screenshot.png");
                Thread.sleep(1500);
                //将截图放在电脑本地
                Runtime.getRuntime().exec(ADB_PATH
                        + " pull /sdcard/screenshot.png " + curPhoto.getAbsolutePath());
                Thread.sleep(200);
            }
            //返回当前图片名字
            return curPhoto;
        } catch (IOException e) {
```
解决方案：
将 截图时 sleep( )的时间调的稍大一些.

或者使用下面的代码块（暂未测试)：
```
//      //网上改进方案
//        try {
//        while (!curPhoto.exists()) {
//            Process   screenshot = Runtime.getRuntime().exec(ADB_PATH + " shell screencap -p /sdcard/screenshot.png");
//            screenshot.waitFor();
//            Process pull = Runtime.getRuntime().exec(ADB_PATH + " pull /sdcard/screenshot.png " + curPhoto.getAbsolutePath());
//            pull.waitFor();}
//        retrun curPhoto;
//        } catch (Exception e) {
//            e.printStackTrace();
//        }
```
##3、Failed loading language 'chi_sim' Tesseract couldn't load any languages!

```问题原因，没有找到 chi-sim 语言包，即中文语言包。在 TessOcr 类的 getOCR 方法中设置了语言类型是：chi-sim , 但是资源目录中并没有这个语言包。所以，需要安装。```

```MAC 下安装语言包可以使用 homebrew命令,参考[https://tonydeng.github.io/2016/07/28/on-the-use-of-tesseract-picture-text-recognition/](https://tonydeng.github.io/2016/07/28/on-the-use-of-tesseract-picture-text-recognition/)```

MAC OS中安装了之后依旧不好使！！！！gitHub上有同样情况：https://github.com/nguyenq/tess4j/issues/26

##补充：homeBrew的安装及使用
http://www.cnblogs.com/TankXiao/p/3247113.html

https://www.jianshu.com/p/4e80b42823d5


其他助手参考：
https://github.com/idealspark/ZhiShiWenDa-Helper
