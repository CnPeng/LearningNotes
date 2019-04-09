### 1. 空安全检查

```kotlin

if (nullable != null) nullable.doStuff()  // Java
nullable?.doStuff() // Kotlin


// Java
public void sendMessageToClient(
    @Nullable Client client,
    @Nullable String message,
    @NotNull Mailer mailer
) {
    if (client == null || message == null) return;
    PersonalInfo personalInfo = client.getPersonalInfo();
    if (personalInfo == null) return;
    String email = personalInfo.getEmail();
    if (email == null) return;
    mailer.sendMessage(email, message);
}

// Kotlin
fun sendMessageToClient(
        client: Client?, message: String?, mailer: Mailer
) {
    if (client == null || message == null) return
    var personalInfo = client.personalInfo ?: return
    var email = personalInfo.email ?: return
    mailer.sendMessage(email, message)
}
```

爽翻天的链式调用

```kotlin
//optional加链式调用：
val name = post?.author?.friends?.get (0)?.name : "none"
```

### 2. 类型推导
### 3. 函数的默认参数与JAVA中的重载
### 4. anko

[anko](https://github.com/Kotlin/anko)

```kotlin
// Java
view.setOnClickListener(new View.OnClickListener() {
 @Override
 public void onClick(View v) {
 System.out.println("This is horrible");
 }
});

// Kotlin
view.onClick {
    println("WAT")
}
```

### 5. 默认public/

### 6. if / else、when 都是表达式有返回值（没有switch）

when 替代 if/else

```kotlin
// Java
if (firstName.equals("Dan")) {
    person.setTeam(programmers);
} else if (lastName.equals("Dihiansan")) {
    person.setTeam(designers);
} else {
    person.setTeam(others);
}

// Kotlin
when {
    firstName == "Dan" -> person.team = programmers
    lastName  == "Dihiansan" -> person.team = designers
	 else -> person.team = others
}
```

when 替代 switch

```kotlin
// Java
switch (firstName) {
 case "Dan": person.setTeam(programmers)
 break;
 case "Jay": person.setTeam(programmers)
 break;
 case "Jamie": person.setTeam(designers)
 break;
 default:
        person.setTeam(others)
}

// Kotlin
when (firstName) {
 "Dan", "Jay" -> person.team = programmers
 "Jamie" -> person.team = designers
 else -> person.team = others
}
```



8. 扩展函数
9. 与 Java 互操作
10.  字符串模板简化
11. 区间的使用/ 遍历的简化
12. 单例的简化--object
13. 类的构造
14. 属性的声明
15. 数据类

```java
public class UserModel {
    private int userId;
    private String firstName;
    private String lastName;
    private String address;
    public UserModel() {
    }
    public UserModel(int userId, String firstName, String lastName, String address) {
        this.userId = userId;
        this.firstName = firstName;
        this.lastName = lastName;
        this.address = address;
    }
    public int getUserId() {
        return userId;
    }
    public void setUserId(int userId) {
        this.userId = userId;
    }
    public String getFirstName() {
        return firstName;
    }
    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }
    public String getLastName() {
        return lastName;
    }
    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
    public String getAddress() {
        return address;
    }
    public void setAddress(String address) {
        this.address = address;
    }
}
```

```kotlin
// 序列化 只需要添加一个注解，然后在 gradle 中开启 实验特性即可
// data class 数据类默认具有 setter getter
@Parcelize
data class UserModel(@SerializedName("userID") var userId:Int, var firstName: String, var lastName: String, var address: String)
```
16. 代理/委托
17. 协程
### 18. 函数类型 和 高阶函数

```kotlin
// Java
public String fullName() {
 return getFirstName() + " " + getLastName();
}

// Kotlin
fun fullName() = "${firstName} ${lastName}"
```
19. 枚举类/印章类
20. 简化写法：let with run apply also
21. 结构相等和引用相等

```kotlin
// 不需要区分是 equals 还是 ==

val john1 =Person（“John”）
val john2 =Person（“John”）
john1 == john2 // true（结构相等）
john1 === john2 // false（引用相等）
```

* inline 内联函数提高效率


* 提高开发效率，代码更简洁, 避免了 JAVA 的冗长
* 没有了分号，不用 new
* ktx ([Kotlin Android Extensions](https://kotlinlang.org/docs/tutorials/android-plugin.html)) 中内置了视图绑定，不用 fbc 了，

## 二、缺点

*  


---
## 三、参考资料

[怎么用Kotlin去提高生产力：Kotlin Tips](https://juejin.im/post/5abe031af265da238059c18c)

[From Java to Kotlin(重点参考)](https://fabiomsr.github.io/from-java-to-kotlin/classes.html)

[Kotlin 作为 Android 开发语言相比传统 Java 有什么优势](https://www.zhihu.com/question/37288009)

[正面示例 https://kaixue.io/kotlin-overview/](https://kaixue.io/kotlin-overview/)

[反面示例：https://blog.csdn.net/csdnnews/article/details/80746096](https://blog.csdn.net/csdnnews/article/details/80746096)


[过滤集合元素的示例](https://www.jianshu.com/p/6783d3fe2c19)

![这是一个图片](https://pbs.twimg.com/media/C0yRbHLUUAAgacl.jpg)