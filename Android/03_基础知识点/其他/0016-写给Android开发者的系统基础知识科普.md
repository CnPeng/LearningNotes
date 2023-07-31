# 0016

[原文：写给 Android 开发者的系统基础知识科普](https://juejin.cn/post/7215509220750098488?utm_source=gold_browser_extension#heading-2)

虚拟机与运行时
对象的概念
假设 getObjectAddress(Object) 是一个获取对象内存地址的方法。
第一题：
考虑如下代码：
public static void main(String[] args) {
    Object o = new Object();
    long address1 = getObjectAddress(o);
    // .......
    long address2 = getObjectAddress(o);
}
复制代码
main 方法中，创建了一个 Object 对象，随后两次调用 getObjectAddress 获取该对象的地址。两次获取到的对象地址是否有可能不同？换句话说，对象的地址是否有可能变更？




答：有可能。JVM 中存在 GC 即“垃圾回收”机制，会回收不再使用的对象以腾出内存空间。GC 可能会移动对象。
第二题：
考虑如下代码：
private static long allocate() {
    Object o = new Object();
    return getObjectAddress(o);
}

public static void main(String[] args) {
    long address1 = allocate();
    // ......
    long address2 = allocate();
}
复制代码
allocate() 创建了一个 Object 对象，然后获取它的对象地址。
main 方法中调用两次 allocate()，这两个对象的内存地址是否有可能相同？




答：有可能。在 allocate() 方法中创建的对象在该方法返回后便失去所有引用成为“不再需要的对象”，如果两次方法调用之间，第一次方法调用中产生的临时对象被上文中提到的 GC 机制回收，对应的内存空间就变得“空闲”，可以被其他对象占用。
第三题：
哎呀，既然上面说同一个对象的内存地址可能不相同，两个不同对象也有可能有相同的内存地址，而java 里的 == 又是判断对象的内存地址，那么
Object o = new Object();
if (o != o)
复制代码
还有
Object o1 = new Object();
Object o2 = new Object();
if (o1 == o2)
复制代码
这里的两个 if 不是都有可能成立？




答：不可能。== 操作符比较的确实是对象地址没错，但是这里其实还隐含了两个条件：

这个操作符比较的是 “那一刻” 两个对象的地址。
比较的两个对象都位于同一个进程内。

上述提到的两种情况都不满足“同一时间”这一条件，因此这两条 if 永远不会成立。
类与方法
第四题：
假设 Framework 是 Android Framework 里的一个类，App 是某个 Android App 的一个类：
public class Framework {
    public static int api() {
        return 0;
    }
}

public class App {
    public static void main(String[] args) {
        Framework.api();
    }
}
复制代码
编译 App，然后将 Framework 内 api 方法的返回值类型从 int 改为 long，编译 Framework 但不重新编译 App，App 是否可以正常调用 Framework 的 api 方法？




答：不能。Java 类内存储的被调用方法的信息里包含返回值类型，如果返回值类型不对在运行时就找不到对应方法。将方法改为成员变量然后修改该变量的类型也同理。
第五题：
考虑如下代码：
class Parent {
    public void call() {
        privateMethod();
    }
    private void privateMethod() {
        System.out.println("Parent method called");
    }
}

class Child extends Parent {
    private void privateMethod() {
        System.out.println("Child method called");
    }
}

new Child().call();
复制代码
Child 里的 privateMethod 是否重写了 Parent 里的？call 中调用的 privateMethod() 会调用到 Parent 里的还是 Child 里的？




答：不构成方法重写，还是会调用到 Parent 里的 privateMethod。private 方法是 direct 方法，direct 方法无法被重写。
操作系统基础
多进程与虚拟内存
假设有进程 A 和进程 B。
第六题：
进程 A 里的对象 a 和进程 B 里的对象 b 拥有相同的内存地址，它们是同一个对象吗？




答：当然不是，上面才说过“对象相等”这个概念在同一个进程里才有意义，不认真听课思考是会被打屁屁的~
第七题：
进程 A 内有一个对象 a 并将这个对象的内存地址传递给了 B，B 是否可以直接访问（读取、写入等操作）这个对象？




答：不能，大概率会触发段错误，小概率会修改到自己内存空间里某个冤种对象的数据，无论如何都不会影响到进程 A。作为在用户空间运行的进程，它们拿到的所谓内存地址全部都是虚拟地址，进程访问这些地址的时候会先经过一个转换过程转化为物理地址再操作。如果转换出错（人家根本不认识你给的这个地址，或者对应内存的权限不让你执行对应操作），就会触发段错误。
第八题：
还是我们可爱的进程 A 和 B，但是这次 B 是 A 的子进程，即 A 调用 fork 产生了 B 这个新的进程：
void a() {
    int* p = malloc(sizeof(int));
    *p = 1;
    if (fork() > 0) {
        // 进程 A 也即父进程
        // 巴拉巴拉巴拉一堆操作
    } else {
        // 进程 B 也即子进程
        *p = 2;
    }
}
复制代码
（fork 是 Posix 内创建进程的 API，调用完成后如果仍然在父进程则返回子进程的 pid 永远大于 0，在子进程则返回 0）
（还是理解不了就把 A 想象为 Zygote 进程，B 想象为任意 App 进程）
这一段代码分配了一段内存，调用 fork 产生了一个子进程，然后在子进程里将预先分配好的那段内存里的值更改为 2。
问：进程 B 做出的更改是否对进程 A 可见？




答：不可见，进程 A 看见的那一段内存的值依然是 1。Linux 内核有一个叫做“写时复制”（Copy On Write）的技术，在进程 B 尝试写入这一段内存的时候会偷偷把真实的内存给复制一份，最后写入的是这份拷贝里的值，而进程 A 看见的还是原来的值。
跨进程大数据传递
已知进程 A 和进程 B，进程 A 暴露出一个 AIDL 接口，现在进程 B 要从 A 获取 10M 的数据（远远超出 binder 数据大小限制），且禁止传递文件路径，只允许调用这个 AIDL 接口一次，请问如何实现？




答：可以传递文件描述符（File Descriptor）。别以为这个玩意只能表示文件！举个例子，作为应用层开发者我们可以使用共享内存的方法，这样编写 AIDL 实现类把数据传递出去：
@Override public SharedMemory getData() throws RemoteException {
    int size = 10 * 1024 * 1024;
    try {
        SharedMemory sharedMemory = SharedMemory.create("shared memory", size);
        ByteBuffer buffer = sharedMemory.mapReadWrite();
        for (int i = 0;i < 10;i++) {
            // 模拟产生一堆数据
            buffer.put(i * 1024 * 1024, (byte) 114);
            buffer.put(i * 1024 * 1024 + 1, (byte) 51);
            buffer.put(i * 1024 * 1024 + 2, (byte) 4);
            buffer.put(i * 1024 * 1024 + 3, (byte) 191);
            buffer.put(i * 1024 * 1024 + 4, (byte) 98);
            buffer.put(i * 1024 * 1024 + 5, (byte) 108);
            buffer.put(i * 1024 * 1024 + 6, (byte) 93);
        }
        SharedMemory.unmap(buffer);
        sharedMemory.setProtect(OsConstants.PROT_READ);
        return sharedMemory;
    } catch (ErrnoException e) {
        throw new RemoteException("remote create shared memory failed: " + e.getMessage());
    }
}
复制代码
然后在进程 B 里这样拿：
IRemoteService service = IRemoteService.Stub.asInterface(binder);
try {
    SharedMemory sharedMemory = service.getData();
    ByteBuffer buffer = sharedMemory.mapReadOnly();

    // 模拟处理数据
    int[] temp = new int[10];
    for (int i = 0;i < 10;i++) {
        for (int j = 0;j < 10;j++) {
            temp[j] = buffer.get(i * 1024 * 1024 + j);
        }
        Log.e(TAG, "Large buffer[" + i + "]=" + Arrays.toString(temp));
    }
    SharedMemory.unmap(buffer);
    sharedMemory.close();
} catch (Exception e) {
    throw new RuntimeException(e);
}
复制代码
这里使用的 SharedMemory 从 Android 8.1 开始可用，在 8.1 之前的系统里也有一个叫做 MemoryFile 的 API 可以用。
打开 SharedMemory 里的源码，你会发现其实它内部就是创建了一块 ashmem （匿名共享内存），然后将对应的文件描述符传递给 binder。内核会负责将一个可用的文件描述符传递给目标进程。
你可以将它理解为可以跨进程传递的 File Stream（只要能通过权限检查），合理利用这个小玩意有奇效哦 ：）

作者：canyie
链接：https://juejin.cn/post/7215509220750098488
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


