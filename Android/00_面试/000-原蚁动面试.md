# 1. Part1

## 1.1. 基础-Java 相关

### 1.1.1. Java 的基本数据类型

* byte 、short  、 int  、long  、 float 、double 、 char  、boolean

### 1.1.2. String 是基本数据类型么

不是，String 是一个 java 类

### 1.1.3. java 的三大特点，如何理解这三大特点的？

* 封装——隐藏内部实现，外部调用方只需要传入参数即可得到期望的值
* 继承——子类继承父类，就可以获取父类的公有属性和方法，提升了代码的复用性，提升了开发效率
* 多态——以引用类型的类作为方法的参数时，只有在运行时才可以最终确定该参数的具体类型。这样，不用修改源代码，就可以让引用变量绑定到不同的实现上。

### 1.1.4. 线程和进程

#### 1.1.4.1. 线程和进程的关系

（做个简单的比喻：进程=火车，线程=车厢）

#### 1.1.4.2. 线程安全和不安全是指的啥？

#### 1.1.4.3. 如何创建线程

### 1.1.5. 线程死锁的产生和解决

[《进程线程与死锁（死锁产生原因、条件）及解决办法》](https://blog.csdn.net/qq_30242987/article/details/104733279)

死锁是指多个进程（线程）在执行过程中，由于**竞争资源或者由于彼此通信而造成的一种阻塞的现象（互相挂起等待）**，若无外力作用，它们都将无法推进下去——永远相互等待。


#### 1.1.5.1. 死锁的产生

##### 1.1.5.1.1. 线程将自己锁住

为了保证线程之间的同步和互斥，我们往往需要给其加锁，有时候，线程申请了锁资源，还没有等待释放，又一次申请这把锁，结果就是挂起等待这把锁的释放，但是这把锁是被自己拿着，所以就会永远挂起等待，就造成了死锁。

##### 1.1.5.1.2. 多线程竞争资源循环等待

有两个线程P1和P2，P1首先申请得到了锁L1,P2申请得到了锁L2,这个时候P1有向去申请锁L2，结果是被挂起等待P2释放锁L2,而P2恰好也想申请锁L1，结果是挂起等待P1释放锁L1，此时就造成两个线程互相僵持，造成死锁。

##### 1.1.5.1.3. 进程推进顺序不当引起的死锁问题

有三个线程，P1，P2和P3，分别生产数据M1，M2，M3，同时分别接收别的线程产生的数据M3,M2,M1,如果线程推进的顺序正确，即三个线程都先生产数据，再接收，那么没有问题，但是一旦线程先接受数据，再生产数据，因为一开始没有数据产生，那么就会造成三个线程的死锁问题。


### 1.1.6. List 和 Map

* List 和 map 的区别
* List 的子类有哪些？
	* ArrayList 、LinkedList、Vector
	* ArrayList 和 LinkedList 的区别——底层数据结构的区别
		* ArrayList 底层是数组结构，元素在内存中是连续的，所以在初始化的时候需要申请一块连续的内容空间，有索引，所以，查找快；LinkedList 底层是链表结果，内存中存储的是相互之间的关系，增加数据时才会申请内存，所以，增删快，但是查询时比较慢  


### 1.1.7. 常用的设计模式？

* 单例
* 观察者
* 工厂
* 适配器
* 构造者

## 1.2. 基础-Android 相关

### 1.2.1. Activity 生命周期

* onCreate onStart onResume onPause onStop onDestroy
* 按下 home 键之后触发的生命周期
* 屏幕方向切换时生命周期会发生如何变化？
	* 如何避免这个变化？ `android:configChanges`

### 1.2.2. Fragment 的生命周期

* onAttach、onCreate、onCreateView、onActivityCreated、onStart、onResume、onPause、onStop、onDestroyView、onDestroy、onDetach

### 1.2.3. Service 生命周期及其启动方式启动方式

* start 模式： onCreate()、onStartCommand()，onStart()、onDestory 。
* bind 模式： onCreate()、onBind()，解除绑定的时候会执行 onUnbind()、onDestory()。

### 1.2.4. 广播的分类及如何接收

#### 1.2.4.1. 分类：

* 有序广播、无序广播

#### 1.2.4.2. 如何接收广播？

[《Android接收广播有两种方法》](https://blog.csdn.net/u013894427/article/details/70158521)

##### 1.2.4.2.1. 静态广播接收器

单独创建一个 Java 文件，写一个类，继承 BroadcastReceiver ，并且**在清单文件中注册该接收器**。

* AndroidManifest写法

```xml
<receiver
   android:name=".device.display.daydream.MyDaydreamBroadcastReceiver"
   android:exported="true" >
      <intent-filter>
           <action android:name="helo.intent.finish.daydream" />
      </intent-filter>
</receiver>
```

* BroadcastReceiver写法

```java
public class MyDaydreamBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        //do something
        }
    }
}
```

##### 1.2.4.2.2. 动态广播接收器

在 Activity 或者 service 中创建内部类，继承 BroadcastReceiver，**不用在清单文件中注册**。

写法如下

```java
 public class DaydreamBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        //do something
    }
}
```

##### 1.2.4.2.3. 两种接收器的区别

* 静态注册的广播可以在广播发送后**随时收到，不要求当前应用是否已经启动**。
* 动态注册的广播不会在广播发送后随时收到，**要求当前应用已经启动，并且完成注册过程**。

#### 1.2.4.3. 使用广播时的注意事项

——记得关闭

### 1.2.5. Intent 

* Intent 的作用
* Intent 传递数据时的限制：基本类型、可序列化的类或者集合
* Intent 的内部实现

### 1.2.6. 常见异常及处理

* 内存泄漏的产生
* 内存溢出的产生和解决

### 1.2.7. 流式布局(瀑布流)的实现方案有哪些？

(Rv+GridLayoutManager、Rv+StaggerLayoutManager 、Rv+FlexboxLayoutManager、Chip+ChipGroups)

### 1.2.8. APP 中展示 Web 页面时，如何实现 Web 内容与原生内容的交互？

* JSDK--JavaScript 
* capictor

### 1.2.9. 数据存储的方式

* SP
* SQLite —— 数据库死锁
* 本地文件
* 网络存储

>各种方式的存储特点及适用场景

### 1.2.10. 动画

* 动画的分类
	* 属性动画、补间动画
	* 属性动画和补间动画的区别——属性动画是真的变化，补间动画会占位 
* 复杂动画的实现方案——`Lottie`


### 1.2.11. RecyclerView 和 ListView 的比较

* Rv 能够实现更为复杂的内容
* Rv 需要自己实现上下拉，Rv 不能加头和脚

### 1.2.12. padding 和 margin 的区别、gravity 和 layout_gravity 的区别


## 1.3. 进阶

### 1.3.1. Gradle 知识点

* 一套代码怎么才能打成多个包，并安装在同一个手机上？
	* 修改应用id 
	* 修改 flavor (免费版、收费版) 

### 1.3.2. 内存泄漏和内存溢出

* 内存泄漏的产生
* 内存溢出的产生

### 1.3.3. APP 体积优化——如何在保证兼容性的情况下减小APP的体积？

* 删除无用资源和类
* 使用 Vector 图片资源
* 清除无用的三方库
* 清除多余的 so

### 1.3.4. 图片加载方案

#### 1.3.4.1. 列表中需要加载的图片比较大时怎么办？

——缩放、压缩、缩略图

#### 1.3.4.2. 查看清明上河图时怎么办？

——原理是 分段加载——BitmapRegionDecoder 、现成方案为 `subsampling-scale-image-view`

### 1.3.5. MVC、MVP、MVVM 模式

### 1.3.6. 适配方案

* 屏幕适配
* android 不同版本的适配

>Splash 页面适配如何做？

### 1.3.7. 自定义 View

* 自定义 View 的流程：测量、绘制、摆放


### 1.3.8. 触摸事件的分发

* 事件分发的流程
* 事件分发时 View 和 ViewGroup 的区别

### 1.3.9. 如何实现侧滑删除？

* ViewDragHelper
* ItemTouchHelper (也可以实现仿支付宝的拖拽调序)

## 1.4. 三方库

### 1.4.1. 项目中使用过的三方库有哪些？

* 推送、
	* 如何提升推送的送达率 
		* 针对不同的手机使用不同的推送方案——集成多套推送SDK
		* 打开APP时检测是否开启了推送权限，没有权限则引导用户  
* 图片加载
	* 常用的图片加载框架有哪些？
	* 如何加载并显示 Gif 动图？
* 网络加载
* 分享和三方登录
* 地图定位
* 即时通讯

### 1.4.2. 做过蓝牙通信么？

* 稳定性和安全性时怎么保证的

## 1.5. 新知识

目前使用的 AndroidStudio 是哪个版本？

### 1.5.1. DataBinding

### 1.5.2. JetPack

### 1.5.3. ConstraintLayout

### 1.5.4. Androidx

* 为什么迁移到 Androidx
* 迁移过程中有碰到坑么？怎么爬坑的？ 

### 1.5.5. Kotlin 有研究过么？

* Kotlin 相对于 java 的优点点在哪？
	* 扩展函数 
* Kotlin 中 var、val、const val 的区别


## 1.6. 周边

### 1.6.1. BUG 管理工具

### 1.6.2. 代码协同工具——git/SVN 

### 1.6.3. 发版流程

### 1.6.4. 平时怎么学习的？有博客么？

### 1.6.5. 近期项目中碰到的典型问题，如何解决的？

### 1.6.6. 编码时习惯写注释么？为什么写注释？