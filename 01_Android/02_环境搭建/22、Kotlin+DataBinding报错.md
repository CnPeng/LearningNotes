19-05-22

使用的 AndroidStudio 版本号为：3.4.1

在使用 Kotlin + DataBinding 组合模式时，可能会报错各种莫名错误，对于这种情况，先确认是否开启了 **kapt**, 一般只要开了就不会再报错了。

在当前 module 的 build.gradle 中做修改，示例如下：

```xml
apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'

// 最核心的就是这一句，加上之后基本就解掉了各种莫名问题
apply plugin: 'kotlin-kapt'

android {
    compileSdkVersion 28

    defaultConfig {
       //...内容省略
    }

    dataBinding{
    	 // 启用数据绑定
        enabled=true
    }
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
	//... 其他中依赖
}
```

