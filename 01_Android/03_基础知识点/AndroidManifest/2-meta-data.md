* [参考文章：《关于Android Manifest中的 `<meta-data>`》](https://www.jianshu.com/p/37f3ea4fd3d6)
* [官方文档：`<meta-data>`](https://developer.android.google.cn/guide/topics/manifest/meta-data-element.html)


---

说明：`meta-data` 和 `strings` 的区别在于它们的作用域。**`strings` 只能在应用本地被读取；而处于 `AndroidManifests.xml` 下的 `meta-data` 则是对外界开放的**，是向系统注册的信息，系统及外界是可以通过 `PackageInfo` 相关 API 获取到 `meta-data` 的信息的。二者关系可以形象一点看待是私有和公有的关系。

## 2.1 语法

```xml
<meta-data 
    android:name="string"           
    android:resource="resource specification"           
    android:value="string" />
```

`<meta-data>` 是一个键值对，用来为父控件存储多余的数据。一个控件可以包含任意数量的 `<meta-data>`，这些值都存储在单个 `Bundle` 对象中，并作为 `PackageItemInfo.metaData` 字段提供给父控件。

## 2.2 属性

* `android:name：`该 item 的唯一名称。为了保证其名字唯一性，可以使用 Java 命名风格
* `android:resource：` 对资源的引用。**其值为资源的 ID**，可以通过该 `Bundle` 的 `Bundle.getInt()` 方法获取其 ID 值
* `android:value：`分配给该 item 的值。[更多类型及介绍见官网文档](https://developer.android.google.cn/guide/topics/manifest/meta-data-element.html)

## 2.3 用法

在 Android 中，可以在 `AndroidManifest.xml` 中定义 `meta-data` 信息。

### 2.3.1 基本用法

最基本的用法增加 item 用来存储信息，且该信息可以被整个项目所使用。这时，`<meta-data>` 定义在`<activity>` 外面，且在 `<application>` 内，例如：

```xml
<manifest>
    <application 
        android:icon="@drawable/icon" 
        android:label="@string/app_name">

        <meta-data 
        		android:name="my_test_metagadata" 
        		android:value="testValue" />

        <activity 
            android:name=".MainActivity" 
            android:label="@string/app_name">

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

        </activity>

    </application>
<manifest>
```

读取时如下：

```java
ApplicationInfo ai = getPackageManager().getApplicationInfo(getPackageName(), PackageManager.GET_META_DATA);
Bundle bundle = ai.metaData;
String myApiKey = bundle.getString("my_test_metagadata");
```

其中，可以存储的类型还包括 `boolean`, `int`, `string`, `float`

### 2.3.2 用于第三方库或 APIs

项目中经常需要引用第三方库或使用其 API，但对于详细的产品，需要一个 KEY 来识别，例如：判断不同的使用者或不同的机型，这个 KEY 必须由开发者定义，并使用相应 API，但由于安全性，这个 KEY 不可能被共享。

通过使用 `<meta-data>`，开发人员可以与使用者共享第三方库或 `APIs` 的 `KEY`，例子如下：

```java
try {
    ApplicationInfo ai = getPackageManager().getApplicationInfo(context.getPackageName(), PackageManager.GET_META_DATA);
    Bundle bundle = ai.metaData;
    String myApiKey = bundle.getString("my_test_metagadata");
} catch (Exception e) {
    Log.e(TAG, "Dear developer. Don't forget to configure <meta-data android:name=\"my_test_metagadata\" android:value=\"testValue\"/> in your AndroidManifest.xml file.");
}
```

例如，为了能够使用 `HockeyApp` 的 `crash report` 功能，需要在 `AndroidManifest` 中添加如下代码：

```xml
<meta-data 
	android:name="net.hockeyapp.android.appIdentifier" 
	android:value="${HOCKEYAPP_APP_ID}" />
```

这行代码会在加载 `net.hockeyapp.android.appIdentifier` 时使用 value 值为 `${HOCKEYAPP_APP_ID}` 的数据，所以，HockeyApp 可以知道是哪一台设备请求获取 crash report 功能。

### 2.3.3 配置 Activity

`<meta-data>` 的另一个应用是配置 Activity，通过这种方式，可以为当前 Acitivity 配置数据，项目能够更好的处理 Activity。这时，`<meta-data>` 标签在 `<activity>` 标签内。例如：

```xml
<manifest>
    <application 
        android:icon="@drawable/icon" 
        android:label="@string/app_name">
        <activity 
            android:name=".MainActivity" 
            android:label="@string/app_name">

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

        </activity>

        <activity android:name=".SearchableActivity" >
            <intent-filter>
                <action android:name="android.intent.action.SEARCH" />
            </intent-filter>
            <meta-data 
            		android:name="android.app.searchable"
                 android:resource="@xml/searchable"/>
        </activity>
    </application>
<manifest>
```

从 `<activity>` 标签内读取数据可以通过如下方式：

```java
try {
        ActivityInfo ai = getPackageManager().getActivityInfo(this.getComponentName(), PackageManager.GET_META_DATA);
        Bundle bundle = ai.metaData;
        if (bundle != null) {
            String apiKey = bundle.getString("apikey");
            Log.d(this.getClass().getSimpleName(), "apiKey = " + apiKey);
            }
        }
    } catch (PackageManager.NameNotFoundException e) {
        Utilities.log(this.getClass().getSimpleName(), "Failed to load meta-data, NameNotFound: " + e.getMessage());
    } catch (NullPointerException e) {
        Log.e(this.getClass().getSimpleName(), "Failed to load meta-data, NullPointer: " + e.getMessage());
    }
```    
