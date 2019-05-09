## 1、Intent 可以传递的数据类型
在 [Intent和IntentFilters（1）](http://www.jianshu.com/p/fcca6dd10437) 中我们知道了通过 Intent 的putExtra( ) 方法可以实现组件之间的数据传递。那么putExtra( ) 方法能够传递的数据类型由哪些限制呢？

下面这张图是我从AndroidSrudio中截取的，这张图里列出了所有可以通过intent传递的数据类型。
![putExtra.png](http://upload-images.jianshu.io/upload_images/2551993-73dc4422c1bf6162.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

根据这张图可以看出，intent 对象可以传递的具体数据类型以及使用的方法。
###(1) putExtra( key , value ) 方法 
* 可以传递 8 种基本数据类型以及这八种基本数据类型的数组，
* 可以传递 Serializable 实现类的对象，
* 可以传递Parcelable 实现类的对象和数组，
* 可以传递Bundle 对象


###(2) putExtras( value )方法 
* 可以传递Bundle 对象，
* 可以传递Intent 对象


###(3) Intent 也可以直接传递集合
* 传递 **Integer类型** 的集合可以直接使用：putIntegerArrayListExtra( key，value)
* 传递 **String 类型** 的集合可以直接使用： putStringArrayListExtra( key , value)
* 传递 **CharSequence 类型** 的集合可以直接使用： putCharSequenceArrayList( key , value)
* 传递 ** Parcelable 类型** 的集合可以直接使用 ：putParcelableArrayList( key,value)

## 2 Intent 传递数据的示例代码
### (1) 传递基本数据类型数据
* IntentUseActivity.java  数据发送方
```
public class IntentUseActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_intent_use);

        Button btn_forResult = (Button) findViewById(R.id.btn_forResult);
        btn_forResult.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(IntentUseActivity.this, TestActviity.class);
                intent.putExtra("name","张三"); 
                startActivity(intent); 
            }
        });
    }
}
```
 >putExtra(key,value) 中两个参数的含义分别是：
>* “name” ， 表示 键名，TestActivity中获取数据时要以键名为条件；
>* “张三”，表示具体要传递的值，需要注意的是，这里的值是什么类型，在 TestActivity 中就要用对应类型的getXXXExtra( key ) 来取值，XXX就是这里值的数据类型。具体参考下面的代码

* TestActviity.java  数据接收方
```
public class TestActviity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_testactivtiy);

        //getIntent 获取上一个界面传递的intent对象，然后用 getStringExtra( ) 拿到具体数据
        String name = getIntent().getStringExtra("name");

        Toast.makeText(this, "获取到上一个界面传递过来的数据是：" + name, Toast.LENGTH_SHORT).show();
    }
}
```
>在这段代码中，先通过getIntent( )方法获取到上一个界面传递的intent对象，然后由于intent 中传递的数据是字符串类型的“ 张三”，所以此处使用getStringExtra( key) 来获取数据，又由于 intent中定义的键名是“name” ,所以此处getStringExtra( ) 传入的参数就是 name.

###(2) 传递基本数据类型的数组
* IntentUseActivity.java  数据发送方
  ```
public class IntentUseActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_intent_use);

        Button btn_forResult = (Button) findViewById(R.id.btn_forResult);
        btn_forResult.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(IntentUseActivity.this, TestActviity.class);

                String[] names = new String[]{"张三", "李四", "王五", "赵二麻子"};
                intent.putExtra("namesArray", names);
                startActivity(intent);
            }
        });
    }
}
  ```

* TestActviity.java  数据接收方
```
public class TestActviity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_testactivtiy);

        String []names = getIntent().getStringArrayExtra("namesArray");
        
        //字符串数组转字符串，并将两端的[] 替换掉
        String str=Arrays.toString(names).replaceAll("[\\[\\]\\s,]", "");
        Log.e("上一个界面传递过来的数据：",str);
    }
}
```
>注意：
> * 在接收方获取intent 中传递的数组时需要使用 getXXXArrayExtra(key), XXX由数组的成员类型决定，key 就是 intent 中putExtra（）中的key.
> * 将数组转成字符串的时候，可以直接使用  Arrays.toString( ) ，传入要转换的数组；使用这个方法转换之后，会在两端带有 [ ] , 所以可以使用 replaceAll("[\\[\\]\\s,]", "") 将其替换成空串。

###(3)  传递 bean 对象
如果需要使用 intent 传递一个bean对象，有如下几种方式：
>* A
让该bean 类实现Serializable 接口，然后使用putExtra( key,value)传递
* B
让bean 实现Parcelable 接口，然后使用putExtra( key,value)传递
* C
将bean对象中的数据分别通过Bundle对象的putXXX( )方法存进bundle 对象，然后在用intent 的puExtra(key,bundle) 或 putExtras(bulde) 方法传递bundle对象。（这种方法不如前两种来的直接，所以，我一般不喜欢这种） 

####1）让bean 类实现Serializable 接口
Serializable 表示序列化，普通的bean类直接实现Serializable 接口后就可以将该bean类 的对象序列化成可存储或者可传输的状态。（普通的bean类不可以直接在组件之间传输）
* 创建User 类，让该类实现Serializable 接口

```
public class User implements Serializable {
    public String name;
    public String sex;
    public int    age;


    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }
}
```
* IntentUseActivity.java 数据发送方

```
public class IntentUseActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_intent_use);

        Button btn_forResult = (Button) findViewById(R.id.btn_forResult);
        btn_forResult.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(IntentUseActivity.this, TestActviity.class);
                User user = new User();
                user.setAge(27);
                user.setName("张三");
                user.setSex("女");

                 //直接将user对象作为value
                intent.putExtra("SerializableBean", user);   
                startActivity(intent);
            }
        });
    }
}
```

* TestActivity.java

```
public class TestActviity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_testactivtiy);

        User user = (User) getIntent().getSerializableExtra("SerializableBean");
        
        
        Log.e("上一个界面传递过来的数据：",user.getName()+user.getAge()+user.getSex());
    }
}
```
* Log 中的结果：
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-445bc38e9750d9b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 这里并没有什么特殊的，只是单纯的让一个普通的bean类实现了Serializable 接口，这样就可以 用 intent 传递这个bean 类的对象了。
> 在接收数据的时候，我们使用的是getSerializableExtra( )方法，得到一个Serializable 对象，然后向下强转得到 user 对象

####2) 让bean 实现Parcelable 接口
parcel 是打包的意思，所以parcelable 也就是可以打包的，与Serializable 相反，Parcelable 是将bean 类中的数据拆分之后，重新打包并存储进Parcel 类中。

* 创建User类，让User类实现Parcelable 接口，并重写方法

```
public class User implements Parcelable {
    public String  name;
    public String  sex;
    public int     age;
    public boolean isAdult;


    public boolean getIsAdult() {
        return isAdult;
    }

    public void setIsAdult(boolean isAdult) {
        this.isAdult = isAdult;
    }


    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    @Override
    public int describeContents() {
        return 0;    //直接返回0就好
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        //将数据写到Parcel中
        dest.writeString(name);
        dest.writeString(sex);
        dest.writeInt(age);

        //boolean类型的数据使用这种方式write到parcel中
        dest.writeByte(isAdult ? (byte) 1 : (byte) 0);  
    }
    
    // 创建Creator 常量,读取Parcel 中的数据
    public static final Parcelable.Creator<User> CREATOR= new Creator<User>() {
        @Override
        public User createFromParcel(Parcel source) {
            User user=new User();
            user.name=source.readString();
            user.sex=source.readString();
            user.age=source.readInt();

            //boolean 类型的数据使用这种方式读取
            user.isAdult=source.readByte()==1;  
            return user;
        }

        @Override
        public User[] newArray(int size) {
            return new User[size];  //返回一个size 大小的 User数组
        }
    };
}
```
> 注意：
A：
我们让User类实现了Parcelable接口之后，需要重写其中的describeContents（）方法，该方法的返回值通常直接给0就好；需要重写writeToParcel( ) ，将user 对象中的数据存储到Parcel 中；最后还需要创建一个 Parcelable.Creator 类型的常量，并在Creator 的构造方法中的createFromParcel( ) 方法中，读取存储在Parcel 中的数据，**此处尤为注意：readXXX ( )的顺序必须和writeXXX( )的顺序保持一致，否则数据会错位**

>B：
writeToParcel( ) 方法中，我们要写到Parcel中的数据是什么类型，那么就用什么类型的writeXXX 。比如，我们定义的name 是String 类型，那么就需要使用writeString( name) ；同理，在createFromPacel( ) 中读取数据的时候也是这样子，数据是String 类型，就用readString( ) 。除boolean类型和char外，其他基本数据类型都可遵从该规律, boolean 类型的写法参考C，Parcel 中没有直接读取char的方法

>**C:
>Parcel 中，也没有直接读取和写出 boolean类型数据的方法.所以在 write 和 read boolean类型的数据时就需要借助Byte 。因Byte 占两个字节，要么0 要么1 ，这就和Boolean的要么true要么false 极为相似，所以，我们在write 的时候，判断isAdult 是true 还是false, 如果true 就写出字节1 ，如果false 就写出 字节 0。在read 的时候，我们只需要判断读到的数据是否等于1 即可，等于1 isAdult 就是true,否则就是 false** 。


* IntentUseActivity.java 数据发送方
```
public class IntentUseActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_intent_use);

        Button btn_forResult = (Button) findViewById(R.id.btn_forResult);
        btn_forResult.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(IntentUseActivity.this, TestActviity.class);
                
                User user = new User();
                user.setAge(27);
                user.setName("张三");
                user.setSex("女");
                user.setIsAdult(true);
                intent.putExtra("ParcelableBean", user);
                startActivity(intent);
            }
        });
    }
}
```
>

* TestActviity.java 数据接收方
```
public class TestActviity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_testactivtiy);

         //此处获取数据的时候， 使用 getParcelableExtra(key)
        User user = (User) getIntent().getParcelableExtra("ParcelableBean");
        
        Log.e("上一个界面传递过来的数据：",user.getName()+user.getAge()+user.getSex()+user.isAdult);
    }
}
```
* 运行结果

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-1182e3c1ae96211b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####3）使用Bundle 传递(对象)数据
使用bundle 传递数据时，有如下两种情况：
**A**
 如果要传递的数据是一个bean对象，那么该bean类必须先实现Serializable 接口或者Pacelable 接口,然后通过putSerializable(key,value) 或者 putParcelable(key,value) 将对象封装进bundle ，然后在通过 Intent 的putExtra(key,value) 或者putExtras(bundle) 传递给下一个界面。示例代码如下（这里只给出bean类实现Serializable 接口的代码）：
* 创建User 类，并实现Serializable 接口

```
public class User implements Serializable {
    public String  name;
    public String  sex;
    public int     age;
    public boolean isAdult;

    public boolean getIsAdult() {
        return isAdult;
    }

    public void setIsAdult(boolean isAdult) {
        this.isAdult = isAdult;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }
}
```
* IntentUseActivity.java 数据发送方

```
public class IntentUseActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_intent_use);

        Button btn_forResult = (Button) findViewById(R.id.btn_forResult);
        btn_forResult.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(IntentUseActivity.this, TestActviity.class);

                User user=new User();
                user.setName("张三");
                user.setSex("女");
                user.setAge(27);
                user.setIsAdult(true);
                
                //创建bundle 对象，封装数据
                Bundle bundle = new Bundle();
                bundle.putSerializable("UserBean",user);
                
                //用intent传递bundle 对象
                intent.putExtra("bundleBean", bundle);
                // intent.putExtras(bundle);

                startActivity(intent);
            }
        });
    }
}
```

* TestActviity.java 数据接收方

```
public class TestActviity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_testactivtiy);

        Bundle bundle = getIntent().getBundleExtra("bundleBean");
        //Bundle bundle = getIntent().getExtras();

        User user = (User) bundle.getSerializable("UserBean");
        
        Log.e("上一个界面传递过来的数据：", user.getName() + user.getAge() + user.getSex() + user.isAdult);
    }
}
```


**B**
如果你没有预定义bean类，只是想通过bundle 传递数据，那么就先创建 Bundle对象，然后用bundle 的putXXX(key,value) 方法，将数据封装进bundle ，然后再通过intent  的putExtra(key,value) 或者 putExtras(bundle) 传递给下一个界面。对应代码如下：
* IntentUseActivity.java 数据发送方

```
public class IntentUseActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_intent_use);

        Button btn_forResult = (Button) findViewById(R.id.btn_forResult);
        btn_forResult.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(IntentUseActivity.this, TestActviity.class);

                //创建bundle 对象，封装数据
                Bundle bundle = new Bundle();
                bundle.putString("name", "张三");
                bundle.putString("sex", "女");
                bundle.putInt("age", 27);
                bundle.putBoolean("isAdult", true);
                

                //用intent传递bundle 对象
                intent.putExtra("bundleBean", bundle);
                // intent.putExtras(bundle);

                startActivity(intent);
            }
        });
    }
}
```
* TestActviity.java 数据接收方
```
public class TestActviity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_testactivtiy);

        Bundle bundle = getIntent().getBundleExtra("bundleBean");
        //Bundle bundle = getIntent().getExtras();

        Log.e("上一个界面传递过来的数据：", bundle.getString("name") + bundle.getInt("age") + bundle.getString("sex") + bundle
                .getBoolean("isAdult"));
}
```
> 注意：
**A**
**Intent 传递 bundle 对象时有两种方法，一种是putExtra(key,bundle) , 对应的获取方法是 getBundleExtra(key) ; 另外一种是 putExtras(bundle) ,对应的获取方法是 getExtras( )。**
>
**B**
实际开发中，在intent中使用bundle 作为数据载体的方式并不常用，因为这样并不比使用intent 的putXXX 方便，这里主要是为了介绍这种思路以及怎么用bundle 存储数据；其实Bundle 更多的是用在 onSaveInstaceState( ) 方法中，确保activity页面关闭时能够保存数据，具体的使用方法，后期会有相应的文章介绍

















###(4) 传递 Bean 对象的集合
最近的项目中就用到了这种方式，需要在组件之间传递一个 bean 对象的集合，实现方式大致有四种：
#### 1）方式1
**让bean实现Serializable 接口，然后将集合强转成Srializable 类型，然后通过intent 的 putExtra( key , value ) 传递**；代码如下：

* 创建User类，并让其实现Serializable 接口
  代码省略，具体可以查看上面的代码

* IntentUseActivity.java 数据发送方
```
public class IntentUseActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_intent_use);

        Button btn_forResult = (Button) findViewById(R.id.btn_forResult);
        btn_forResult.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(IntentUseActivity.this, TestActviity.class);
                //                List<User> list = new ArrayList<User>();
                ArrayList<User> list = new ArrayList<User>();
                for (int i = 0; i < 10; i++) {
                    User user = new User();
                    user.setName("张三" + i);
                    user.setSex("女");
                    user.setAge(27);
                    user.setIsAdult(true);
                    list.add(user);
                }
                intent.putExtra("SerialUserList", list);    //如果集合是ArrayList 不需要强转
                //                intent.putExtra("SerialUserList", (Serializable) list);   //如果集合是List 需要强转
                startActivity(intent);
            }
        });
    }
}
```
* TestActviity.java 数据接收方
```
public class TestActviity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_testactivtiy);

        //获取Serializable 对象，并强转成 list集合
        Serializable serializable = getIntent().getSerializableExtra("SerialUserList");
        
        //        List<User> userList= (List<User>) serializable;
        ArrayList<User> userList = (ArrayList<User>) serializable;  //接收的时候强转
        for (User user : userList) {
            Log.e("上一个界面传递过来的数据：", user.getName() + user.getAge() + user.getSex() + user.isAdult);
        }
    }
}
```
> 这里有一点需要注意的，如果定义的集合类型是ArrayList 在传递的时候不需要强转，如果List 则需要强转。




####2） 方式2 
**让bean 实现Parcelable 接口，然后使用 intent 的putParcelableArrayList(key,value) 方法**

* 创建User类，让其实现Parcelable接口
  代码省略，具体可以参考前面的代码。
  >让bean类实现Parcelable 接口时，有一个小技巧，可以先把bean类及其成员变量以及set get 方法定义好，然后再去实现Parcelable 接口，会提示你实现方法，你就直接按下快捷键实现相应的方法就好，然后你就会发现， writeToPaecel ( ) 以及 CREATOR常量都已经帮你生成好了，同时生成了一个bean类的有参构造，参数是Parcel  , 那么你现在唯一需要做的就是在给出一个空参构造 。

* IntentUseActivity.java 数据发送方
```
public class IntentUseActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_intent_use);

        Button btn_forResult = (Button) findViewById(R.id.btn_forResult);
        btn_forResult.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(IntentUseActivity.this, TestActviity.class);
                List<User> list = new ArrayList<User>();
                //                ArrayList<User> list = new ArrayList<User>();
                for (int i = 0; i < 10; i++) {
                    User user = new User();
                    user.setName("张三" + i);
                    user.setSex("女");
                    user.setAge(27);
                    user.setIsAdult(true);
                    list.add(user);
                }
                //                intent.putParcelableArrayListExtra("ParcelableList", list);  //如果集合是ArrayList 不需要强转
                intent.putParcelableArrayListExtra("ParcelableList", (ArrayList<User>) list); //如果集合是List需要强转成ArrayList
                startActivity(intent);
            }
        });
    }
}
```

* TestActviity.java 数据接收方
```
public class TestActviity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_testactivtiy);

        //        ArrayList<User> userList=getIntent().getParcelableArrayListExtra("ParcelableList");
        List<User> userList = getIntent().getParcelableArrayListExtra("ParcelableList"); //上面的代码也可以

        for (User user : userList) {
            Log.e("上一个界面传递过来的数据：", user.getName() + user.getAge() + user.getSex() + user.isAdult);
        }
    }
}
```

####3) 方式3 
**让bean类实现Serializable接口，然后将集合强转成Srializable 类型，然后再调用Bundle 对象的 putSerializable(key ,value) ，最后在用intent 的putExtras(bundle) ,或者 putExtra(key,bundle) 将数据传递出去。**（这种方式归根结底也是用了A 方式，只不过多封装了一层，效率不高，不如直接用A实在。） 
>  这种方法就不列出完整代码了，只给出关键部分：
* 发送方
```
Bundle bundle = new Bundle();    
bundle.putSerializable("userList",(Serializable)list);  
intent.putExtras(bundle);    
startActivity(intent);
```
* 接收方
```
List<User> userList = (List<User>) getIntent( ).getSerializableExtra ("userList");
```


>> **注意：**
**使用intent传递实现了Serializable 或者Parcelable接口 的bean对象或bean对象集合的时候，bean内部包含的成员变量必须全部是基本数据类型！！！ **

>>**如果有非基本数据类型会报如下错误：Parcelable encountered IOException writing serializable object android**


》》》》》》等会儿，晚上下班接着写
####4) 方式4
**如果，你感觉用 Intent 对象传来传去比较麻烦，还要去解析，那么 最最直接的方法就是，自定义Application，并在自定义的Application中创建一个全局的集合变量，这样各个组件就都可以访问、获取、设置这个集合的值了。**



###(5) 传递 String 、Integer、CharSequence 类型数据的集合
