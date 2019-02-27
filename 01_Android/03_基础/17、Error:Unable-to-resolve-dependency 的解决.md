![](https://gitee.com/uploads/images/2018/0322/200423_677debe8_930142.png "屏幕截图.png")

```
 Error:Unable to resolve dependency for ':app@App名称/compileClasspath': Could not resolve project:module库名称.
```

导入本地module作为项目的依赖库时，如果出现上面的错误，

是因为：

**你项目的build.gradle中 buildTypes{} 节点包含的子节点和你依赖的module的 build.type 的 buildType{} 包含的节点不一致**

解决方法是：

**找到你APP的 build.gradle 文件，将其中的 `buildTypes {}` 节点完整的拷贝到你的module库的 build.gradle 文件中。**

比如：
我当前项目 build.gradle 的 buildTypes{} 节点如下：
```
  buildTypes {
        debug {
            //内容省略
        }
        release {
            //内容省略
        }
        antTest {
            //内容省略
        }
    }
```

那么，所依赖 moudle 的 build.gradle 的 buildTypes{} 节点如下：

```
  buildTypes {
        debug {
            //可以没有任何内容，只要节点数量和名称一致即可
        }
        release {
            //可以没有任何内容，只要节点数量和名称一致即可
        }
        antTest {
            //可以没有任何内容，只要节点数量和名称一致即可
        }
    }

```

参考链接：
https://stackoverflow.com/questions/46949622/android-studio-3-0-unable-to-resolve-dependency-for-appdexoptions-compileclas

参考链接中主要内容摘录如下：
![](https://gitee.com/uploads/images/2018/0322/195310_af52863e_930142.png "屏幕截图.png")