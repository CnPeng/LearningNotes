基于 https://developer.android.com/studio/build/application-id 整理

### 1、应用ID基本介绍

#### （1）、基本介绍
应用ID是应用的唯一标识。

当我们上传APP到应用市场或者覆盖安装时，如果 **应用ID一致，并且签名文件一致，则认为是同一款APP** 

应用ID默认定义在模块的 build.gradle(module)中，对应 applicationId 属性。

示例如下：
```
android {
    defaultConfig {
        applicationId "com.example.myapp"
       ...
    }
    ...
}
```

#### （2）、命名规则

新建项目时，默认应用ID为apk的完整包名，但二者没有必然关系，我们可以根据需要修改应用ID。

在更改应用ID时，需要遵守如下规则：

* 至少包含两段（即 有一个或多个圆点间隔符）
* 每段必须以字母开头
* 所有字符必须为 字母、数字、下划线。对应正则`[a-zA-Z0-9_]`


#### （3）、注意事项 

* **Android API 中的部分方法和参数会牵涉到 “软件包名称” 这个概念，实际上它对应的就是应用ID，而不是真实的包名** 
* 比如 Context.getPackageName()返回的就是应用ID
* 无论何时都不需要在应用代码以外分享代码真实的软件包名称。


### 2、更改用于构建变体的应用ID 

当我们构建变体APK时，可以在 productFlavors 中重新定义 **applicationId** , 或者通过 **applicationIdSuffix** 为应用id添加后缀，从而得到不同的变体。

* **示例1、在productFlavors中修改id：** 
```
android {
    defaultConfig {
        applicationId "com.example.myapp"
    }
    productFlavors {
        free {
            applicationId "com.example.myapp.free"
        }
        pro {
            //输出的应用id为：com.example.myapp.pro
            applicationIdSuffix ".pro"
        }
    }
}
```

* **示例2、在buildType中修改id：** 

```
android {
    ...
    buildTypes {
        debug {
            //当我们需要在同一个手机中同时安装调试和生产APP时，可以使用该方法
            applicationIdSuffix ".debug"
        }
    }
}
```

### 3、更改用于测试的应用ID

 **这一段实在没看懂，所以，直接复制官方文档中的内容。** 

> 默认情况下，构建工具会将应用 ID 应用到您的仪器测试 APK，该 APK 将应用 ID 用于给定的构建变体，同时追加 .test。例如，com.example.myapp.free 构建变体的测试 APK 的应用 ID 为 com.example.myapp.free.test。
> 
> 尽管不必需，您也可以通过在 defaultConfig 或productFlavor 块中定义 testApplicationId 属性来更改应用 ID。
> 
> 注：为了避免与受测应用的名称冲突，构建工具会根据测试应用 ID（而不是清单文件中定义的软件包名称）通过命名空间为您的测试 APK 生成 R 类。



### 4、更改软件包名称

#### （1）、package
默认情况下，软件包名与应用ID一致，但二者没有必然关系。

我们可以自定义包名（项目目录结构名称），但是 **在自定义包名的时候需要保证同步更新清单文件中的 package 属性值** ,否则，会报错。

示例：
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapp"
    android:versionCode="1"
    android:versionName="1.0" >
```

#### (2)、package的作用
package 的属性值有如下两个用途：

* 会作为应用生成的 R.java 类的命名空间

如：对于上面的清单，R 类将为 com.example.myapp.R。

* 可用来解析清单文件中声明的相关类名称

如：在清单文件中，我们声明某个activtiy时，可能会使用`<activity android:name=".MainActivity"> ` 这种声明方式，那么，其中的 " . " 就表示 package 的属性值。 

* 当我们在包名目录又定义的子目录，并在子目录中编写了相关代码类，那么，在清单文件中声明时，也必须带上子目录名称

如：`<activity android:name=".sub.MainActivity">` 或者 `<activity android:name="com.example.myapp.sub.MainActivity">`

#### （3）、多个package

当我们的项目中有多个源集，并且不同的源集中定义了不同的清单文件时，需要注意他们的优先级和替代规则。

详细可参考：https://developer.android.com/studio/build/manifest-merge


#### （4）、注意事项

尽管清单文件中 package 和 Gradle中的 applicationId 可以具有不同的名称，但 **构建工具会在构建结束时将应用 ID 复制到 APK 的最终清单文件中** 。

所以，如果您在构建后检查 AndroidManifest.xml 文件，package 属性发生更改就不足为奇。实际上，Google Play 商店和 Android 平台会注意 package 属性来标识您的应用；所以 **构建利用原始值后（用作 R 类的命名空间并解析清单类名称），它将会舍弃此值并将其替换为应用 ID。**

 
### 5、清单文件中引用应用ID

如果您需要在清单文件中引用应用 ID，可以在任何 **清单属性中使用 ${applicationId} 占位符** 。

构建时，Gradle 会将此标记替换为实际的应用 ID。

 如需了解详细信息，请参阅[将构建变量注入清单](https://developer.android.com/studio/build/manifest-build-variables)。

