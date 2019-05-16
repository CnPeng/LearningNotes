API24——N 之前获取 URI 时可以直接通过 `Uri.fromFile(file)` 获取，

但 API24——N 及以后的版本中需要通过调用 FileProvider 获取 URI , 在使用 FileProvider 时必须先修改清单文件。

具体如下：

在清单文件的 `<application></application>` 节点中添加如下内容：

```xml
 <!-- 解决API24 及以上版本调用系统相机时报：FileUriExposedException 的情况 -->
 <provider
     android:name="androidx.core.content.FileProvider"
     android:authorities="${applicationId}.provider"
     android:exported="false"
     android:grantUriPermissions="true">
     <meta-data
         android:name="android.support.FILE_PROVIDER_PATHS"
         android:resource="@xml/file_paths" />
 </provider>
```

在代码中调用时，方式如下：

```java
 private Uri parUri(File cameraFile) {
    Uri imageUri;
    
    // 注意：此处的 authority 必须与清单文件中通过 android:authrities 注册的 主机名完全一致！！！
    String authority = getPackageName() + ".provider";

    if (Build.VERSION.SDK_INT > Build.VERSION_CODES.M) {
        //通过FileProvider创建一个content类型的Uri
        imageUri = FileProvider.getUriForFile(mContext, authority, cameraFile);
    } else {
        imageUri = Uri.fromFile(cameraFile);
    }
    return imageUri;
}
```