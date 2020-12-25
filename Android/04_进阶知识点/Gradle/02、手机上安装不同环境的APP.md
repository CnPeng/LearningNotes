同一个手机上安装不同环境的APP，本质上就是让不同环境的APP具有不同的 applicationId ,

有个两种实现方案：

### 1、修改 applicationIdSuffix

```java
android {
    defaultConfig {
        applicationId "com.cnpeng.myapp"
    }
    productFlavors {
        debug {
            applicationIdSuffix ".free"
        }
        test {
            applicationIdSuffix ".pro"
        }
        release {
			// 生产环境不用定义，直接使用 defaultConfig 中的默认值
        } 
    }
}
```

按照上述默认修改之后， debug、test、release 环境下 applicationId 分别为：

- com.cnpeng.myapp.debug
- com.cnpeng.myapp.test
- com.cnpeng.myapp


### 2、修改 applicationId

```java
android {
    defaultConfig {
        applicationId "com.cnpeng.myapp"
    }
    productFlavors {
        free {
            applicationId "com.cnpeng.myapp.free"
        }
        pro {
            applicationId "com.cnpeng.myapp.pro"
        }
    }
}
```

按照上述默认修改之后， debug、test、release 环境下 applicationId 分别为：

- com.cnpeng.myapp.debug
- com.cnpeng.myapp.test
- com.cnpeng.myapp


相关内容：

[01、修改APP名称.md](01、修改APP名称.md)