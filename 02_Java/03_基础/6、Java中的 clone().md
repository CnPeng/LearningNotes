> 以下内容摘自 《黑马阳哥面试宝典》

## 1 为什么要用 clone?
首先，克隆对象是很有必要的，当一个对象需要被多人操作，但是又不想相互影响，需要保持原对象的状态，这 时，就会克隆很多个相同的对象。

## 2 new 一个对象的过程和 clone 一个对象的过程区别
new 操作符的本意是分配内存。程序执行到 new 操作符时， 首先去看 new 操作符后面的类型，因为知道了类型， 才能知道要分配多大的内存空间。分配完内存之后，再调用构造函数，填充对象的各个域，这一步叫做对象的初始化， 构造方法返回后，一个对象创建完毕，可以把他的引用(地址)发布到外部，在外部就可以使用这个引用操纵这个对 象。

clone 在第一步是和 new 相似的， 都是分配内存，调用 clone 方法时，分配的内存和原对象(即调用 clone 方 法的对象)相同，然后再使用原对象中对应的各个域，填充新对象的域， 填充完成之后，clone 方法返回，一个新的 相同的对象被创建，同样可以把这个新对象的引用发布到外部。

## 3 clone 对象的使用

### 3.1 复制对象和复制引用的区别

```
Person p = new Person(23, "zhang"); 
Person p1 = p;
System.out.println(p);
System.out.println(p1);
```

当 Person p1 = p;执行之后， 是创建了一个新的对象吗? 首先看打印结果:

```
com.itheima.Person@2f9ee1ac 
com.itheima.Person@2f9ee1ac
```
可以看出，打印的地址值是相同的，既然地址都是相同的，那么肯定是同一个对象。p 和 p1 只是引用而已，他们 都指向了一个相同的对象 Person(23, “zhang”) 。 可以把这种现象叫做引用的复制。上面代码执行完成之后， 内 存中的情景如下图所示:
     
 ![输入图片说明](https://images.gitee.com/uploads/images/2018/0926/084356_fef9a820_930142.png "屏幕截图.png")
            

而下面的代码是真真正正的克隆了一个对象。

```      
Person p = new Person(23, "zhang"); 
Person p1 = (Person) p.clone(); 
System.out.println(p);
System.out.println(p1);
```
从打印结果可以看出，两个对象的地址是不同的，也就是说创建了新的对象， 而不是把原对象的地址赋给了一个 新的引用变量:

```
com.itheima.Person@2f9ee1ac 
com.itheima.Person@67f1fba0
```

以上代码执行完成后， 内存中的情景如下图所示:
![输入图片说明](https://images.gitee.com/uploads/images/2018/0926/084607_627e375c_930142.png "屏幕截图.png")
          
### 3.2 深拷贝和浅拷贝
上面的示例代码中，Person 中有两个成员变量，分别是 name 和 age， name 是 String 类型， age 是 int 类型。 代码非常简单，如下所示:

```
public class Person implements Cloneable{
    private int age ;
    private String name;

    public Person(int age, String name) {
         this.name = name;
         this.age = age;
    }

    public Person() {} 

    public int getAge() {
        return age;
    }

    public String getName() {
        return name;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return (Person)super.clone();
    }
```
由于 age 是基本数据类型，那么对它的拷贝没有什么疑议，直接将一个 4 字节的整数值拷贝过来就行。

但是 name 是 String 类型的， 它只是一个引用， 指向一个真正的 String 对象，那么对它的拷贝有两种方式: **直接将原对象中 的 name 的引用值拷贝给新对象的 name 字段，或者是根据原 Person 对象中的 name 指向的字符串对象创建一个新 的相同的字符串对象，将这个新字符串对象的引用赋给新拷贝的 Person 对象的 name 字段。这两种拷贝方式分别叫 做浅拷贝和深拷贝**。

深拷贝和浅拷贝的原理如下图所示:
![输入图片说明](https://images.gitee.com/uploads/images/2018/0926/085051_a0a2c5e1_930142.png "屏幕截图.png")

下面通过代码进行验证。
* 如果两个 Person 对象的 name 的地址值相同， 说明两个对象的 name 都指向同一个 String 对象， 也就是浅拷贝， 
* 而如果两个对象的 name 的地址值不同， 那么就说明指向不同的 String 对象， 也就 是在拷贝 Person 对象的时候， 同时拷贝了 name 引用的 String 对象， 也就是深拷贝。验证代码如下:

```
Person p = new Person(23, "zhang");
Person p1 = (Person) p.clone();
String result = p.getName() == p1.getName()? "clone 是浅拷贝的" : "clone 是深拷贝的";
System.out.println(result);

```
打印结果为:
```
clone 是浅拷贝的
```
所以，**clone 方法执行的是浅拷贝**， 在编写程序时要注意这个细节。


## 4、如何进行深拷贝:

由上一节的内容可以得出如下结论:

**如果想要深拷贝一个对象，这个对象必须要实现 Cloneable 接口，实现 clone 方法，并且在 clone 方法内部，把该对象引用的其他对象也要 clone 一份，这就要求这个被引用的对象必须也要实现 Cloneable 接口并且实现 clone 方法**。

那么，按照上面的结论，实现以下代码 Body 类组合了 Head 类，要想深拷贝 Body 类，必须在 Body 类的 clone 方法中将 Head 类也要拷贝一份。代码如下:

```
static class Body implements Cloneable{
      public Head head;
      public Body() {}

      public Body(Head   head) {
        this.head = head;
      } 

     @Override
     protected Object   clone() throws CloneNotSupportedException {
         Body newBody   = (Body) super.clone(); 
         newBody.head   = (Head) head.clone(); 
         return newBody;        
     }
}

static class Head implements Cloneable{ 
    public Face face;

    public Head() {}
 
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

public static void main(String[] args) throws CloneNotSupportedException {
     Body body = new Body(new Head(new Face()));
     Body body1 = (Body) body.clone();
     System.out.println("body == body1 : " + (body == body1) );
     System.out.println("body.head == body1.head : " + (body.head == body1.head));
}

```

打印结果：

```
body == body1 : false
body.head == body1.head : false
```