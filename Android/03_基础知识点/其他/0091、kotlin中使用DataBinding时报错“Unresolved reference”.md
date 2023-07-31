## 一、问题现象

当前使用的 AndroidStudio 版本为：3.4 ，kotlin 版本为 ：1.3.31
 
在 kotlin 中 使用 DataBinding 时，报 `Unresolved reference: BR` 或类似错误

## 二、解决方案为：

* 在 module 的 build.gradle 文件中添加 `apply plugin: 'kotlin-kapt'`

具体如下：

```xml
apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'
// 加上这一句就不会报错了
apply plugin: 'kotlin-kapt'
apply plugin: 'kotlin-android-extensions'

android {
    compileSdkVersion var.compileSdkVersion
    
	 // 启用数据绑定
    dataBinding {
        enabled = true
    }

    //...其他内容省略
}

dependencies {
    implementation fileTree(include: ['*.jar'], dir: 'libs')
    //...其他内容省略
}
```

