## 1、错误现象
* 错误信息截图
![](https://images.gitee.com/uploads/images/2018/0831/154445_ade29277_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2018/0831/154553_478d8471_930142.png "屏幕截图.png")

* 错误信息详细描述

```
org.gradle.api.tasks.TaskExecutionException: Execution failed for task ':jingutong:transformResourcesWithMergeJavaResForDebug'.

...省略其他内容

Caused by: com.android.builder.merge.DuplicateRelativeFileException: More than one file was found with OS independent path 'META-INF/rxjava.properties'

...省略其他内容

```

## 2、问题原因

module 的 gradle 中引用了 rxjava，同时，依赖的另一个 library 中也依赖的 rxjava ,这样，就造成了重复引用，两者就冲突了！！

```

dependencies {
    implementation fileTree(include: ['*.jar'], dir: 'libs')

    ...省略其他内容

    //之前的代码中已经引用了 rxjava
    implementation 'io.reactivex:rxjava:1.3.0'

    //新引用的库中也使用了 rxjava/rxjava2 ,这样就造成了重复引用
    implementation 'com.github.LuckSiege.PictureSelector:picture_library:v2.2.3'

}
```

## 3、解决方案

```
android {
   
    ...其他节点内容省略

    packagingOptions {
        //解决 rxjava冲突的问题
        exclude 'META-INF/rxjava.properties'
    }
}
```