# 1. 0059-从AGP4升级到AGP7时找不到VERSION_NAME的解决

## 1.1. 问题现象

将 AGP 从 4.0 直接升级到 7.1.2 之后，java 代码中无法再调用 `BuildConfig.VERSION_NAME`，编译时会提示找不到 `VERSION_NAME.`

也就是说，原 gradle 文件中 `android` 节点中定义的 versionName、versionCode 都已经失效。

## 1.2. 该如何维护版本

正确的方式是在 `AndroidManifest.xml` 清单文件的 `manifest` 标签中定义 `versionName` 和 `versionCode`。

如果不在此处声明，那么从 App 升级安装时就会提示 "设备已经存在比当前更新的版本"。


示例如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:versionName="1.2.8"
    android:versionCode="68"
    package="com.cn.peng">

     <!--其他内容省略-->
</manifest>
```

也可以将版本号定义为字符串资源，然后进行引用，如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:versionName="@string/version"
    android:versionCode="68"
    package="com.cn.peng">
     <!--其他内容省略-->
</manifest>
```

## 1.3. 如何在java代码中获取 versionName

### 1.3.1. 方式1-推荐

如果使用字符串资源定义版本号，那么我们直接通过 `Context` 的 `getString(resID)` 方法获取即可。

### 1.3.2. 方式2

如果需要在 java 代码中引用这个 `versionName`，可以使用如下方式：

```kotlin
    try {
        val manager = this.packageManager
        val info = manager.getPackageInfo(this.packageName, 0)
        info.versionName
        if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.P) {
            L.i("info.versionName ${info.versionName} ${info.longVersionCode} ")
        }else{
            L.i("info.versionName ${info.versionName} ${info.versionCode} ")
        }
    } catch (e: Exception) {
        e.printStackTrace()
    }
```


### 1.3.3. 方式3-不推荐

如果想继续在 java 代码中通过 `BuildConfig.VERSION_NAME` 获取版本号信息，就需要在 gradle 中自己进行定义和维护。如下：

```groovy
android {
    def VNAME = '1.2.8'
    buildTypes {
        debug {
            manifestPlaceholders = [app_name: "智慧EAC平台"]
            debuggable true
            // gradle 升级到 4.1 以上版本之后，无法通过 BuildConfig 直接获取 VersionName，所以定义 VERSION 字段
            buildConfigField "String", "VERSION", "\"" + VNAME + "\""

            buildConfigField "int", "CONFIG_URL", "R.string.CommonConfigDevUrl"
            buildConfigField "boolean", "isDebug", "true"

            // 其他内容省略
        }
    }

    // 其他内容省略
}
```

在上述代码中，

* 通过 `buildConfigField` 定义了 `String` 类型的 `VERSION` 字段，将 `VNAME` 作为该字段的值。
* VNAME 是字符串类型，但是在 `buildConfigField` 中将其作为属性值时前后还必须加上转义后的引号,否则编译不通过。

通过声明之后，编译之后，在 `BuildConfig` 中就会有 `VERSION` 字段，这样我们就可以通过 `BuildConfig.VERSION` 获取到
版本号。

如果使用这种方式，虽然使用便捷，但我们必须同时在 `AndroidManifest.xml` 和 `build.gradle` 中维护版本号。这不利于代码维护。