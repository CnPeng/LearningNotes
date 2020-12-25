[原文链接：《Android Studio 生成自定义apk、aar名称》](https://blog.csdn.net/yanjingtp/article/details/80605971)

在 Android studio 中默认生成的 apk、aar 的名称都是 `app-release.apk`、`app-debug.apk`、`app-release.aar`、`app-debug.aar`。

通过修改 module 下的 `build.gradle` 可以自定义生成的 apk、aar 名称

## 36.1 自定义 apk 的名字

在 module 的 `build.gradle` 的 `android{}` 节点里添加代码，即可设置 apk 名字 : 


```groovy
android.applicationVariants.all { variant ->
    variant.outputs.each { output ->
        def outputFile = output.outputFile
        if (outputFile != null && outputFile.name.endsWith('.apk')) {
            def fileName = "自定义.apk"
            output.outputFile = new File(outputFile.parent, fileName)
        }
    }
}
```

但是，上面这种方式极有可能会报错，错误信息如下：

```
Cannot set the value of read-only property 'outputFile' for object of type com.android.build.gradle.internal.api.LibraryVariantOutputImpl.
```


那么，我们就需要将前面的代码修改为：

```groovy
 android.applicationVariants.all { variant ->
        variant.outputs.all {
            def fileName = "自定义.apk"
            outputFileName = fileName
        }
    }
```

## 36.2 自定义aar的名字

在我们自定义的 library module 的 `build.gradle` 中的 `android{}` 里添加代码，即可设置 aar 名字

```groovy
android.libraryVariants.all { variant ->
        variant.outputs.each { output ->
            def outputFile = output.outputFile
            if (outputFile != null && outputFile.name.endsWith('.aar')) {
                def fileName = "自定义.aar"
                output.outputFile = new File(outputFile.parent, fileName)
            }
        }
    }
```


但是，上面这种方式极有可能会报错，错误信息如下：

```
Cannot set the value of read-only property 'outputFile' for object of type com.android.build.gradle.internal.api.LibraryVariantOutputImpl.
```

那么，我们就需要将前面的代码修改为：

```groovy
android.libraryVariants.all { variant ->
        variant.outputs.all {
            def fileName = "自定义.aar"
            outputFileName = fileName
        }
    }
```