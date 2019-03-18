基于：https://developer.android.com/studio/build/manifest-build-variables 整理


### 1、定义和引用占位符
当我们需要再 AndroidManifest.xml 引用定义在 build.gradle(module)的变量时，就需要使用 **manifestPlaceholders** 属性。

示例：

*  **build.gradle中定义占位符** 
 
```java
android {
    defaultConfig {
       //多个之间使用逗号分隔
       manifestPlaceholders = [hostName: "cnpeng", action: "android.intent.action"]

    }
    ...
}
```
*  **清单文件中引用占位符**

```java
<activity android:name="com.cnpeng.SysShare.AddAppToSysShareActivity">
    <!--将应用加入系统分享界面，并处理分享过来的数据-->
    <intent-filter>
         <action android:name="${action}.SEND" />
         <action android:name="${action}.SEND_MULTIPLE" />
         <category android:name="android.intent.category.DEFAULT" />
         <data android:mimeType="application/*" />
         <data android:mimeType="audio/*" />
         <data android:mimeType="image/*" />
         <data android:mimeType="text/*" />
         <data android:mimeType="video/*" />
         <data android:mimeType="multipart/*" />
    </intent-filter>
</activity>
``` 

### 2、applicationId占位符

默认情况下，构建工具会通过 ${applicationId} 对外暴露应用id。

该值是合并完各变体中应用ID之后的最终值。

当我们需要为 intent Action 定义一个唯一的标识时，该占位符是非常有用的。

示例：
*  **build.gradle** 

假设我们的gradle中有如下配置

```java
android {
    defaultConfig {
        applicationId "com.example.myapp"
    }
    productFlavors {
        free {
            applicationIdSuffix ".free"
        }
        pro {
            applicationIdSuffix ".pro"
        }
    }
}
```

*  **清单文件中引用应用id** 

```java
<intent-filter ... >
    <action android:name="${applicationId}.TRANSMOGRIFY" />
    ...
</intent-filter>
```

当我们构建 free 版本时，name 的最终值如下：

```java
<intent-filter ... >
   <action android:name="com.example.myapp.free.TRANSMOGRIFY" />
    ...
</intent-filter>
```
