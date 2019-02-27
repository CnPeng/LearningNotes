https://blog.csdn.net/shift_wwx/article/details/78468397

平台版本是android 7.0，在adb install *.apk 会提示下面的错误：

 Failure [INSTALL_FAILED_TEST_ONLY: installPackageLI]



方法1：

> 修改AndroidManifest.xml 中android:testOnly="true" 改成 android:testOnly="false"，或者直接去掉。



方法2：

>adb push *.apk /tmp
>adb shell pm install -t /tmp/*.apk


方法3：(已测可用)

>adb install -t *.apk

方法4：

[Android Studio 3.0 and FLAG_TEST_ONLY](https://commonsware.com/blog/2017/10/31/android-studio-3p0-flag-test-only.html)  一文中，给出了关于Android studio 3.0中出现问题的说明：

```
Option to indicate this application is only for testing purposes.
For example, it may expose functionality or data outside of itself that would cause a security hole, but is useful for testing.
This kind of application can not be installed without the INSTALL_ALLOW_TEST flag, which means only through adb install.

1. You cannot install an app with android:testOnly="true" by conventional means, such as from an Android file manager or from a download off of a Web site
2. Android Studio 3.0 sets android:testOnly="true" on APKs that are run from the IDE
```

解决办法：

在gradle.properties(项目根目录或者gradle全局配置目录 ~/.gradle/)文件中添加：
>android.injected.testOnly=false