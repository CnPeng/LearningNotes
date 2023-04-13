Gradle 中动态配置 APP name

有两种方案：


### 1、定义 manifestPlaceholders

* module 的 .gradle 中的配置

```java
android {

	defaultConfig{
		// 定义默认值
		manifestPlaceholders = [appName : "CnPeng的APP"]
		
		// 其他内容省略
	}

	buildTypes {
		debug {
			manifestPlaceholders = [appName : "开发版APP"]
		}
		test {
			manifestPlaceholders = [appName : "测试版APP"]
		}
		
		release {
			// 生产环境不需要处理，使用默认的就行
		}
	}
}
```

* 清单文件中引用：

```java
<application
	android:label="${appName}">
	
	// 其他内容省略
```

* 补充：

如果需要同时定义多个 placehodler ，使用如下格式，多个之间使用逗号间隔。

```java
manifestPlaceholders = [hostName: "cnpeng", action: "android.intent.action"]
```

### 2、定义 resValue

* module 的 .gradle 文件中的配置

```java
android {

	defaultConfig{
		resValue "string", "app_name", "CnPeng的APP" 
	}

	buildTypes {
		debug {
			resValue "string", "app_name", "开发"
		}
		test {
			resValue "string", "app_name", "测试"
		}
		debug {
			// 生产环境直接使用 defaultConfig 中的默认值
		}
}
```

* 清单文件中引用

```java
<application
	android:label="@string/app_name">
```

* 注意：

必须要保证 `res/values/string.xml` 中的资源名 与上面定义的资源名重复。

也就是说，我们在 gradle 中定义了一个键为 `app_name` 的 string 资源，那么 `string.xml` 中就不能有同名的键，否则，会报错并且不能正常编译。 


相关内容：

[手机上安装不同环境的APP](02、手机上安装不同环境的APP)


