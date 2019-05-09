## (1) 为什么要自定义Log：
> 在代码编写和测试过程中，必然少不了打印Log 日志。但是，往往我们在用完了Log 之后要么就忘记了去关闭它 -- 这样会影响应用的运行效率；要么就要手动一个个的去找到它然后注释掉 -- 这也是一件非常痛苦的事情。所以，我们需要自定义Log， 将AS的log 进行包装，当我们的项目要上线的时候，统一控制和关闭Log

## (2) 具体代码：
```
package CommonUtils;

import android.util.Log;

import static android.util.Log.VERBOSE;

/**
 * Created by CnPeng on 2016/12/18.
 * <p>
 * 自定义Log工具类，方便在项目上线时统一关闭Log
 */

public class LogUtils {
    public static final int VERBOSE = 0;
    public static final int DEBUG   = 1;
    public static final int INFO    = 2;
    public static final int WARN    = 3;
    public static final int ERROR   = 4;
    public static final int WTF     = 5;

    //屏蔽log的时候，可以将NONE 的值赋给LEVEL
    public static final int NONE = 6;

    //通过控制LEVEL的值，实现屏蔽Log的效果,默认使用最低级别
     //    public static final int LEVEL = NONE;
    public static final int LEVEL = VERBOSE;

    public static void v(String tag, String msg) {
        if (LEVEL <= VERBOSE) {
            Log.v(tag, msg);
        }
    }

    public static void d(String tag, String msg) {
        if (LEVEL <= DEBUG) {
            Log.d(tag, msg);
        }
    }

    public static void i(String tag, String msg) {
        if (LEVEL <= INFO) {
            Log.i(tag, msg);
        }
    }

    public static void w(String tag, String msg) {
        if (LEVEL <= WARN) {
            Log.w(tag, msg);
        }
    }

    public static void e(String tag, String msg) {
        if (LEVEL <= ERROR) {
            Log.e(tag, msg);
        }
    }

    public static void wtf(String tag, String msg) {
        if (LEVEL <= WTF) {
            Log.wtf(tag, msg);
        }
    }

}
```
## (3) 实际使用：
```
。。。其他代码省略。。。    
@Override   //获取传递的数据
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        //获取传递的数据， 先判断arguments是否为空，不为空获取，为空给出默认值 ,TODO 根据position判断要显示的是图片，还是全部，还是文档      
        positon = getArguments() != null ? getArguments().getInt("position") : -1;

        LogUtils.e("没事儿", "就是随便打个log，测试能不能屏蔽");
    }
。。。其他代码省略。。。 
```

## (4) 总结：
>####A:
在上面的代码中，我们定义了7 个常量， 0-5 分别对应AS 中 Log 的级别，另外一个常量LEVEL 用来控制Log 的显示和隐藏。如果我们需要显示Log，就让LEVEL=VERBOSE，如果我们需要屏蔽所有LOG，就让LEVEL 的值大于前面Log级别对应的任意常量值。
>####B:
>因为我们将方法都定义成了static 的，所以可以直接通过  类名.方法名 调用.
>####C：
>在使用Log 时，第一个参数是TAG标识，第二个参数是需要输出的信息。通常情况下，会将当前类名定义为TAG的值，但实际使用的使用，方便起见，可以自己随意定义TAG，就像上面的示例一样。但需要注意的是，**TAG 的值不能是空字符串“”，而且TAG值的长度不能超过23个字符，作为TAG的取值时汉字也是作为一个字符处理的**，如果超过23个字符会出现下面的提示：
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-6c50a5da9d688f84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


