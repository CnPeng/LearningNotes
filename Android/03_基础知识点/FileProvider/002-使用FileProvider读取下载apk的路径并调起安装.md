# 002-使用FileProvider读取下载apk的路径并调起安装

为了通过`FileProvider`获取文件的`Uri`，你需要先在你的应用中设置好`FileProvider`。以下是配置和使用`FileProvider`来获取文件`Uri`的步骤：

### 1. 在AndroidManifest.xml中配置FileProvider

在`<application>`标签内添加`<provider>`元素来定义`FileProvider`。需要设置`android:authorities`属性为一个唯一的字符串，通常是你的应用包名加上`.fileprovider`，同时指定需要共享的文件路径。

```xml
<application ...>
    ...
    <provider
        android:name="androidx.core.content.FileProvider"
        android:authorities="${applicationId}.fileprovider"
        android:exported="false"
        android:grantUriPermissions="true">
        <meta-data
            android:name="android.support.FILE_PROVIDER_PATHS"
            android:resource="@xml/file_paths" />
    </provider>
    ...
</application>
```

### 2. 创建file_paths.xml资源文件

在`res/xml`目录下创建一个名为`file_paths.xml`的文件（如果目录不存在，请先创建），并定义你想要通过`FileProvider`共享的文件路径。例如，如果你想共享下载目录中的文件，可以这样配置：

```xml
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <external-path name="downloads" path="Download/" />
</paths>
```
这里的`<external-path>`指定了外部存储的根目录，`path`属性指定了在此根目录下的相对路径，即`Download/`目录。

### 3. 使用FileProvider获取Uri

修改之前动态生成Intent的部分，使用`FileProvider.getUriForFile()`方法来获取文件的Uri。首先，你需要知道文件的确切路径，然后构建Uri。以下是修改后的示例代码：

```java
// 假设已知下载完成的文件路径
String filePath = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS) + "/your_downloaded_file.apk";

// 构建File对象
File file = new File(filePath);

// 使用FileProvider获取Uri
Uri uri = FileProvider.getUriForFile(context, "${applicationId}.fileprovider", file);

// 设置Intent
Intent installIntent = new Intent(Intent.ACTION_VIEW);
installIntent.setDataAndType(uri, "application/vnd.android.package-archive");
installIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
// 添加读取权限
installIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
context.startActivity(installIntent);
```

请确保替换`${applicationId}`为你在清单文件中设置的实际authorities值，并且正确设置了`filePath`变量来指向你想要安装的APK文件的路径。

这样，你就通过`FileProvider`安全地为安装意图提供了文件的Uri，并且正确地授予了读取权限。