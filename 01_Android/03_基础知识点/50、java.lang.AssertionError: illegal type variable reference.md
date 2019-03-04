## 1、问题现象：
APP使用 XX加固助手 加固之后，在安卓 7.x.x 系统中会崩溃：错误信息为 `java.lang.AssertionError: illegal type variable reference。`

未加固之前并没有出现这个问题。

## 2、解决方案

### （1）方案一：

网络搜索发现，大部分都说是开启混淆之后导致的，所以，根据网络搜索结果说明，先检查 module 的 build.gradle 和 proguard-rules.pro 中的配置。

* 如果 buid.gradle 中开启了  `minifyEnabled` ，那么 `proguard-rules.pro` 中需要添加如下内容
```
-keepattributes EnclosingMethod
```

如果添加了上述内容之后依旧不生效，可添加下面的内容：
```
-keepattributes Exceptions,InnerClasses,Signature,Deprecated,
                SourceFile,LineNumberTable,*Annotation*,EnclosingMethod
```

* 如果该方案没能解决问题，请参考方案二。

### (2)、方案二
如果方案一中该配置的都处理了，但是在使用第三方加固时报错了，那说明第三方加固中配置了混淆参数，所以，此时我们只能修改我们代码中的内容。

#### 1）、定位问题点
通过LOG或者BUGGLY查看具体的错误位置。如下图：

![输入图片说明](https://images.gitee.com/uploads/images/2018/0806/113505_4d6d7b97_930142.png "屏幕截图.png")

在上图中，我们可以定位到问题所在，是我们内部AFragment中的第159行和另一个BFragment中的132行。最终问题是指向 AFragment中的159行，那么，这里干了啥呢？

```
//GsonUtil.stringToList(str)是我们自己封装的一个工具类
List<String> settingList = GsonUtil.stringToList(userSetting);
```
stringToList(str)的具体逻辑为：
```
 public static <T> ArrayList<T> stringToList(String gsonString) {
        ArrayList<T> list = null;
        //gson即Gson对象
        if (gson != null) {
            list = gson.fromJson(gsonString, new TypeToken<ArrayList<T>>() {
            }.getType());
        }
        return list;
    }
```
#### 2）、复现规律和问题原因分析
然后重复测试发现如下规律：
* 在没有使用 xxx加固助手之前，正常打包的APP在这地方并不会报错，可以正常运行；
* 除 7.x.x 之外的系统，加固之后也不会报错;

根据上述规律猜测大致的问题原因如下：
* 加固助手中对代码进行了二次混淆
* 二次混淆之后，7.x.x系统中在调用 fromJson的转换时，虽然调用了获取类型的 TypeToken<ArrayList<T>>().getType(),但这个getType()并没有生效——fromJson转换时会进行类型擦除，getType()不生效就表示没能获取到类型。然后，就挂掉了。。。
* 为啥只有7.x.x 的系统中 getType()不生效呢？这个真的没想出来为啥。。。。

#### 3）、问题的解决
在 B 中我们分析了问题原因应该是没有获取到数据类型，那么我们就需要想法子让 getType()拿到具体的数据类型。

##### A：首先我们做了如下尝试：
```
List<String> settingList = new ArrayList<>();
list = new Gson().fromJson(userSetting, new TypeToken<ArrayList<String>>() {
         }.getType());

```
上述代码中，在构造 TypeToken 对象时，我们指定了 ArrayList 接收参数的类型为 String，而不再是T。重新加固并安装运行 —— 不再报错！！

##### B: 然后又做了如下尝试：
```
/**
 * 转成list
 */
public static <T> ArrayList<T> stringToList(String gsonString, Class<T> cls) {
   ArrayList<T> list = new ArrayList<>();
   if (gson != null) {
      JsonArray array = new JsonParser().parse(gsonString).getAsJsonArray();
      for (final JsonElement elem : array) {
          list.add(gson.fromJson(elem, cls));
      }
   }
   return list;
}
```
在上述代码中，我们不再直接转换为List，而是使用GSON解析每一个对象，并且传入 .class —— 有了 .class 也就可以确定类型。重新加固并安装运行——不再报错！

## 3、最后：
**对于为啥加固之后只有7.x.x 的系统报错，这个一直没有想明白，如果你知道，请务必留言告知，谢谢！**


