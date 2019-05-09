>声明：本文是基于 http://blog.csdn.net/mabeijianxi/article/details/68525164 中第二种方式的二次整理。

###1、在 main 下新建 jni 目录

创建目录：

![](http://upload-images.jianshu.io/upload_images/2551993-eab577e2dae1dd82.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

创建 jni 目录完成：

![](http://upload-images.jianshu.io/upload_images/2551993-20db0aab3a2cb28e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



###2、在 jni 目录中新建 .cpp 文件（假设名称为 jniTest.cpp）
![](http://upload-images.jianshu.io/upload_images/2551993-6fa475d5fd62dad1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###3、找到项目的 gradle.properties ，添加一行 android.useDeprecatedNdk=true 

![](http://upload-images.jianshu.io/upload_images/2551993-88c4b26fa2f5c3f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###4、打开你主 Module 的 build.gradle ,在 defaultConfig 里添加:

```
ndk{
     moduleName 'jniTest'
     abiFilter 'armeabi-v7a'
 }
```
![](http://upload-images.jianshu.io/upload_images/2551993-be19d9cbc6a39076.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>* **moduleName** 就是在 第2步 创建的 **.cpp** 文件的名称。也是生成的 **.so** 文件的名字中的一部分。**.so**文件名称的生成规则是：lib后面拼接.cpp文件的名称，以 **.so** 结尾。`如jniTest.cpp 构建之后生成的 .so 文件的名称为 libjniTest.so `.
>
>* **abiFilter** 就是对架构类型对应的so 的过滤，一般保留 arm 即可。
`当然除了这些还有很多可配参数。比如想添加个日志库，那就添加: IdLibs “log“ `


###5、在java里面声明个 native 方法，并引入 cpp 库文件

```
//声明native方法
private native String jniTellMeWhy(String hiJni);

//使用静态代码块引入cpp
 static {
        System.loadLibrary("jniTest");
    }
```

###6、复制 native 方法所在类的全类名
>Mac 版 AS 中，使用 Mac OS X 10.5+ 快捷键模式，获取全类名的 快捷键是：shift+alt+cmd+c。  `先将光标放置到类名上，然后同时按下该快捷键即可复制全类名。`

示例：

![](http://upload-images.jianshu.io/upload_images/2551993-d7ffe776716f9b80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
按下快捷键之后，在粘贴板中得到：com.cnpeng.jnitest.MainActivity

###7、在 jniTest.cpp 文件中声明 jniTellMeWhy 的方法体
代码如下：
```
#include <jni.h>
#include <string.h>
#include <stdlib.h>

extern "C" jstring
Java_com_cnpeng_jnitest_MainActivity_jniTellMeWhy(JNIEnv *env, jobject obj, jstring str) {
    const char *question = env->GetStringUTFChars(str, JNI_FALSE);
    char *answer = "fuck,no why!!!";
    char *data = (char *) malloc(strlen(question) + strlen(answer) + 1);
    strcpy(data, question);
    strcat(data, "JNI说:");
    strcat(data, answer);
    return env->NewStringUTF(data);
}
```

**上面代码的解读如下：**

（1）、这里用的是 c++ 所以得加上 **extern "C"** ，因为在 C++ 中函数在编译的时候会拼接上参数，`这也是 c++ 中函数重载的处理机制，`比如一个 set(int a) 和一个 set(int a,int b) ，在编译的时候就变成了 set_int 与 set_int_int ，我们加上extern ”C“ 就表示我们想按照C来编译，所以函数名字后面就不会拼接上参数类型了。

（2）、由于native方法 jniTellMwWhy() 有一个 String类型返回值，所以在方法体上方声明其对应的返回值类型为 jstring。

（3）、在 .cpp 文件中声明方法体时，方法名称的命名规则是：
>* 以 **Java_** 开头，
>* 后面跟 native 方法所在类的全类名，全类名中的 **.** 用 **_**
 替换。`复制全类名的方法在 第 6 步中已经有说明`
>* 最后追加 **_方法名**`该方法名就是在java类中声明的 native 方法名`

（4）、native方法 jniTellMwWhy() 接收一个 String类型的参数。当我们在 .cpp文件中声明该方法时，由于C中每个函数默认都会两个参数，一个是 JNIEnv 指针类型的结构体，一个是调用者对象，比如我们这里就是MainActivity对象。所以，第三个参数，才是我们传入的参数及其类型。

###8、将主 module与 C++工程 利用 gradle 关联

![](http://upload-images.jianshu.io/upload_images/2551993-75e3626abaeff846.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/2551993-20ab505fd81d0911.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>* **Build System** ：选择 ndk-build 
>* **Project Path** : 选择 Android.mk 文件，该文件是完成前面几步之后AS自动生成的，其目录是：app/build/intermediates/ndk/debug/Android.mk

选择完成之后，点击 ok  , 即可实现二者的关联。

###9、 在Java类中调用 native 方法
![](http://upload-images.jianshu.io/upload_images/2551993-ed83bb3c87f53099.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行结果：

![](http://upload-images.jianshu.io/upload_images/2551993-867c5bc3292e95a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



参考资料：
[Android下玩JNI的新老三种姿势](http://blog.csdn.net/mabeijianxi/article/details/68525164)

[向您的项目添加 C 和 C++ 代码](https://developer.android.com/studio/projects/add-native-code.html?utm_source=android-studio#create-sources)

[NDK 入门指南](https://developer.android.com/ndk/guides/index.html)
