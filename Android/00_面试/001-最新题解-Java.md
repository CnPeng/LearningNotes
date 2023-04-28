# 1. 001-最新题解-Java


## 1.1. java中的强引用、软引用、弱引用、虚引用分别如何使用

### 1.1.1. 引用方式的分类

Java中的强引用、软引用、弱引用、虚引用是用来管理对象的内存的方式。

* 强引用（`Strong Reference`）：
最常见的引用方式，**如果一个对象具有强引用，那么只要强引用存在，垃圾回收器就不会回收这个对象**。例如：

```java
// obj是一个强引用
Object obj = new Object(); 
```

* 软引用（`Soft Reference`）
如果一个对象只具有软引用，**那么当系统内存不足时，垃圾回收器就会回收这个对象。软引用通常用来实现缓存。**例如：

```java
// obj是一个软引用
SoftReference<Object> obj = new SoftReference<Object>(new Object()); 
```

* 弱引用（`Weak Reference`）
如果一个对象只具有弱引用，那么**垃圾回收器在下一次扫描时就会回收这个对象。弱引用通常用来实现对象之间的关联关系**。例如：

```java
// obj是一个弱引用
WeakReference<Object> obj = new WeakReference<Object>(new Object());
```

* 虚引用（`Phantom Reference`）`/ˈfæntəm/`：
如果一个对象只具有虚引用，那么**垃圾回收器在回收这个对象时会把这个对象加入到引用队列中，而不是直接回收。虚引用通常用来在对象被回收前执行一些操作**。例如：

```java
ReferenceQueue<Object> queue = new ReferenceQueue<Object>();
// obj是一个虚引用
PhantomReference<Object> obj = new PhantomReference<Object>(new Object(), queue); 
```

### 1.1.2. 使用方式

* 强引用：不需要特别的处理，直接使用即可。
* 软引用：使用 `SoftReference` 类来创建软引用对象，如果软引用对象不为空，就可以通过 `get()` 方法获取到引用的对象。
* 弱引用：使用 `WeakReference` 类来创建弱引用对象，如果弱引用对象不为空，就可以通过 `get()` 方法获取到引用的对象。
* 虚引用：使用 `PhantomReference` 类来创建虚引用对象，如果虚引用对象不为空，可以通过 `get()` 方法获取到 `null` 。虚引用通常与 `ReferenceQueue` 类一起使用，可以在对象被回收前执行一些操作。

## 1.2. java类中的静态成员和方法什么时候被销毁

Java中的静态成员和方法是与类一起加载的，只有在类被卸载时才会被销毁。类被卸载的情况包括两种：

* 当JVM停止或程序结束时，所有的类都会被卸载。
* 当一个类的所有实例都被销毁，并且没有任何地方引用该类时，该类也会被卸载。

在这两种情况下，静态成员和方法都会被销毁。但是，在实际开发中，通常不需要过多考虑静态成员和方法的销毁问题，因为它们的作用是为了在整个应用程序生命周期内共享数据和方法，而不是为了被销毁。

## 1.3. 单例模式的不同方式

### 1.3.1. 实现方式分类

Java中单例模式的实现方式有以下几种：

* 饿汉式：在类加载时就创建对象，线程安全，但可能会浪费内存。

* 懒汉式：在需要时才创建对象，线程不安全，需要加锁或使用双重检查锁定等方式保证线程安全。

* 枚举式：在枚举类型中使用单例，线程安全，但不够灵活。

* **静态内部类式**：在静态内部类中创建单例对象，**线程安全，且延迟加载，推荐使用**。

* 双重检查锁式：在懒汉式的基础上使用双重检查锁定，线程安全，但容易出现空指针异常。

* 容器式：将单例对象存储在静态Map等容器中，可以管理多个单例对象，但需要处理并发问题。

以上几种实现方式各有优缺点，具体使用哪种方式取决于具体的需求和场景。一般来说，静态内部类式和枚举式是比较推荐的实现方式，因为它们都是线程安全的且不容易出现其他问题。

### 1.3.2. 实现代码

#### 1.3.2.1. 饿汉式

```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    private Singleton() {}
    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

#### 1.3.2.2. 懒汉式

```java
public class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

#### 1.3.2.3. 枚举式

实现枚举式单例非常简单，只需要定义一个枚举类型，枚举类型中只有一个枚举值，该枚举值就是单例对象，代码如下：

```java
public enum Singleton {
    INSTANCE;
    public void doSomething() {
        // 单例对象的操作
    }
}
```

这个枚举类型就是一个单例，可以通过 `Singleton.INSTANCE` 来访问单例对象。如：

```java
Singleton.INSTANCE.doSomething();
```

**枚举类型的实现方式天生是线程安全的**，因为枚举类型的实例是在类加载时创建的，且枚举类型是不可变的。此外，**枚举类型还可以防止反序列化和反射攻击**，是一种非常优秀的单例实现方式。

#### 1.3.2.4. 静态内部类式(推荐)

```java
public class Singleton {
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    private Singleton() {}
    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

#### 1.3.2.5. 双重检查锁式

```java
public class Singleton {
    private static volatile Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

* 容器式

```java
public class SingletonManager {
    private static Map<String, Object> map = new HashMap<>();
    private SingletonManager() {}
    public static void registerService(String key, Object instance) {
        if (!map.containsKey(key)) {
            map.put(key, instance);
        }
    }
    public static Object getService(String key) {
        return map.get(key);
    }
}
```

以上几种实现方式各有优缺点，具体使用哪种方式取决于具体的需求和场景。一般来说，**静态内部类式和枚举式是比较推荐的实现方式**。