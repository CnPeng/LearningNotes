参考内容

https://developer.android.com/studio/preview/features/?utm_source=android-studio&utm_medium=studio-assistant


## 1 、迁移前提
迁移到AndroidX的前提有如下三条：

* compileSdk 不低于 28 （当前最高就是28）
* 升级 gradle 为不低于 3.2.0 的版本，对应的设置在 project 的build.gradle 中，如下：
```
 dependencies {
        classpath 'com.android.tools.build:gradle:3.3.0-alpha05'
}
```
* 在gradle.properties 中设置如下属性
    * android.useAndroidX：设置true时，表示使用AndroidX。
    * android.enableJetifier：设置true时，表示让AndroidStudio自动将第三方库中的suppor转换为AndroidX 
```
android.useAndroidX=true
android.enableJetifier=true
```


## 2、如何迁移

Refactor > Migrate to AndroidX