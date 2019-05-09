这个标题有点长，乍一看这么个标题你可能没明白啥意思，且听我慢慢道来。

公司的项目中新增了一个“心动” 的功能，用户初次使用时需要给一个引导页，就是下面图中的这个样子（这就是做完之后的效果了）。
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-3010c2d86c6b9bb5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在上图中整体实现的时候使用的是popUpWindow。该popupWindow整体使用相对布局，里面再用一个相对布局布局嵌套了三个TextView："啊哦。。。。pass" 用一个TextView，中间灰色的上传头像的提示用了一个TextView，底部“我知道了” 也是一个TextView。上面的左划示意图使用above 放在 包含TextView的相对布局上方，并通过负的margin值将它下移并覆盖在包含TextView相对布局上。

这个界面并没有什么难度，这里重点说的是第一个TextView中的图文混排，并让图片的横向中间线与该行文字的横向中间线对齐，也就是说，让文字与那个💔 图片的中间在水平方向对齐。

## 1. 图文混排的方式有哪些？

通常我们向TextView中插入图片实现图文混排有如下方式：
>1. 使用drawableLeft等属性设置，这种方式对应的java方法是   setCompoundDrawablesWithIntrinsicBounds(leftDrawble,topDrawable,rightDrawable,bottomDrawable);
* 使用 SpannableString ,先将图片转成ImageSpan对象，然后通过setSpan插入到SpannableString 中，最后再将SpannableString通过setText设置给TextView。（SpannableString 继承自CharSquence）
- 此外，还有一种利用Html.ImageGetter格式化图片的方式。（截止目前为止，我没用过这种方式，如果想了解的话，可以参考[http://wangleyiang.iteye.com/blog/1771439](http://wangleyiang.iteye.com/blog/1771439)中的第二点）

## 2. 使用SpannableString+ImageSpan怎么实现图文混排？

### (1). 基本实现方式
效果图如下：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-fc53391ed58b6e00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
实现方式很简单，我们只需要在xml布局文件中定义一个TextView，然后在代码中获取该TextView并创建一个含有图片的SpannableString,并将该SpannableString通过setText( )设置给TextView即可。代码如下：

```
public class SpannableStringAndImageSpanActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_spannbalestring_imagespan);
        init();
    }

    private void init() {

        TextView tv_test = (TextView) findViewById(R.id.tv_test);
        SpannableString spannableString = new SpannableString("点击 按钮有惊喜");

        ImageSpan imageSpan = new ImageSpan(this, R.mipmap.ic_launcher);

        //setSpan插入内容的时候，起始位置不替换，会替换起始位置到终止位置间的内容，含终止位置。
        //Spanned.SPAN_EXCLUSIVE_EXCLUSIVE模式用来控制是否同步设置新插入的内容与start/end 位置的字体样式，此处没设置具体字体，所以可以随意设置
        spannableString.setSpan(imageSpan, 2, 3, Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        tv_test.setText(spannableString);
    }
}
```
xml布局文件中只给了一个普通的TextView，代码省略。
>1. 在上面的代码中，我们通过ImageSpan的构造方法得到了一个ImageSpan对象。该构造方法中传入的两个参数分别是上下文和图片的id。（imageSpan的构造方法还有很多）
>2. SpannbaleString的setSpan方法中，传入的四个参数分别是 ImageSpan对象、将ImageSpan插入到的起始位置(start)、将ImageSpan插入到的终点位置(end)、是否应用字体样式。具体将ImageSpan对象插入到哪个位置，由第二个和第三个参数确定，**插入的时候会覆盖从 start 位置开始（不包含该位置）到终止位置间的内容（包含该位置）**。第四个参数是在你插入文本的时候使用的，用来控制新插入的文本与已有文本内容的字体样式是否一致的如果你插入的是图片，这里就可以随便选择一种模式。

经过上面虽然实现了图文混排，但是，细心的你可能发现了，这时候的文字和图片是基于底部对齐的（由于图片的原因，图片底部与边框有一点点的间距）。那么如果我想更改对齐方式怎么办呢？


###(2). 更改图片与文本的对齐方式--ALIGN_BASELINE对齐

设置对齐方式的方法很简单，在构造ImageSpan对象的时候，传入第三个参数ALIGN_BASELINE 即可，代码如下：
 ```
 ImageSpan imageSpan = new ImageSpan(this, R.mipmap.ic_launcher, DynamicDrawableSpan.ALIGN_BASELINE);
```

设置对齐方式为ALIGN_BASELINE后的效果图：
![](http://upload-images.jianshu.io/upload_images/2551993-6d3961063817f785.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

咦，看着跟上面的图没啥区别啊？那么我再把上面没设置对齐方式的图拉下来看下：
![](http://upload-images.jianshu.io/upload_images/2551993-fc53391ed58b6e00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
仔细对比下，我们发现，设置对齐方式之后，图往上跑了一点点。

>**其实，在ImageSpan 中，官方只给出了两中对齐方式：**
>1. 一种是 ALIGN_BOTTOM , 表示与文字内容的底部对齐，如果在构造ImageSpan时没有传入对齐方式，那么默认就是这种底部对齐。
>2. 另一中就是 ALIGN_BASELINE, 表示与文字内容的基线对齐。那么，你可能会问我基线是啥？请继续往下看：

## 3. Paint.FontMetrics 是啥？
###(1). Paint.FontMetrics基本介绍
要说基线呢，我们先了解这个Paint.FontMetircs, 官方对该类的解释是：`Class that describes the various metrics for a font at a given text size.`, 意思是说，这玩意儿是绘制文本内容时存储该文本内容位置信息的一个类。这个类中有如下五个字段：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-e89615ed195f2aa9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###(2). BaseLine 基线到底是啥？
上图中这5个字段除了leading 外，其他四个都是相对于 基线BaseLine来确定的，那么，到底啥是基线？？先来看一张图：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-6f9a8dd2e97a1497.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如上图，**标准的英文书写是基于四线三格，其中，我们书写英文的时候，都是以第三条线为基准，也就是说，基线就是这个四线三格中的第三条线！！**

###(3). Paint.FontMetrics中字段的含义及示意图
官方文档中对这几个字段的解释很简单，但理解起来挺费劲，直接上图，**图中的标注都是跑代码之后确定的，如果有不准确的地方，欢迎指正：**

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-8b5a5d1ef1672ad8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
根据上图可知：
>- **ascent**   
文字内容的顶部到基线的距离。即 ascent=文字内容顶部Y坐标 - 基线Y坐标。由于android中坐标系是 右下为正，所以得到的ascent实际是一个负数。
****
> - **descent**
文字内容的底部到基线的距离。即 descent=文字内容底部Y坐标 - 基线Y坐标。
****
>- ** 基线 **
在图中，基线的坐标用Y表示，在ImageSpan父类的 draw( ) 中，会传入一个 float Y ,就是这个基线的坐标。实际上，**基线的Y坐标=文字内容中间线Y坐标+1/2 （文字内容高度）**
****
>- **top**
对应图中 文字所在行的top 坐标
****
>- **bottom**
对应图中 文字所在行的bottom 坐标
**需要注意：如果设置了行间距，且文本内容产生了换行，那么这个bottom 也会将行间距包裹。所以， 图中蓝色的文字内容中间线的Y轴坐标并不一定等于 (bottom+top)/2**

## 4 自定义ImageSpan实现文字与图片居中对齐
好了，前面说了那么多，终于进入正题了。。。
在上面的2 SpannableString+ImageSpan实现图文混排中，我们已经知道官方并没有给出文字与图片居中对齐的模式,所以需要我们自定义。

关于自定义ImageSpan的分析，已经有前辈讲解过了，此处不再赘述，请参考[http://blog.csdn.net/gaoyucindy/article/details/39473135](http://blog.csdn.net/gaoyucindy/article/details/39473135)。但是，按照该文章中的代码实现的时候，有个问题就是：如果给TextView设置了行间距，且文本产生了换行，那么就无法对齐了！！

那么，设置了行间距之后，该如何实现文本和图片的居中对齐呢？也有前辈分析过了，请看：[http://www.cnblogs.com/withwind318/p/5541267.html](http://www.cnblogs.com/withwind318/p/5541267.html) , 但是，这篇文章中的实现方式没有重写 getSize( ) 方法，所以也有一个问题：文本和图片并不是在TextView的居中位置，而且如果图片高于文本的话，图片会显示不全！！如下图：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-fd8693b3bf6aefd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

参考了那么多了，终于该给出我的终极方案了！！
**根据上面链接中两位前辈的分析，其实我们自定义的时候，需要做的事情是 获取文本内容的中间线以及图片的中间线，然后获取两者差值，然后在draw方法中绘制图片时将差值作为canvas.translate(x, transY) 中的transY；同时要重写 getSize( )。这样最终实现的效果是，不论是否设置行间距，不论图片大于文本还是文本大于图片，都能实现文本和图片的居中对齐！**

看最终效果图：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-5edc1c2d439b19a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上代码：
```
public class SpannableStringAndImageSpanActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_spannbalestring_imagespan);
        init();
    }

    private void init() {

        TextView tv_test = (TextView) findViewById(R.id.tv_test);
        SpannableString spannableString = new SpannableString("点击 按钮有惊喜");

        //调用自定义的imageSpan,实现文字与图片的横向居中对齐
        CustomImageSpan imageSpan = new CustomImageSpan(this, R.mipmap.ic_launcher, 2);

        //setSpan插入内容的时候，起始位置不替换，会替换起始位置到终止位置间的内容，含终止位置。
        //Spanned.SPAN_EXCLUSIVE_EXCLUSIVE模式用来控制是否同步设置新插入的内容与start/end 位置的字体样式，此处没设置具体字体，所以可以随意设置
        spannableString.setSpan(imageSpan, 2, 3, Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        tv_test.setText(spannableString);
    }

    /**
     * 自定义imageSpan实现图片与文字的居中对齐
     */
    class CustomImageSpan extends ImageSpan {

        //自定义对齐方式--与文字中间线对齐
        private int ALIGN_FONTCENTER = 2;

        public CustomImageSpan(Context context, int resourceId) {
            super(context, resourceId);
        }

        public CustomImageSpan(Context context, int resourceId, int verticalAlignment) {
            super(context, resourceId, verticalAlignment);
        }

        @Override
        public void draw(Canvas canvas, CharSequence text, int start, int end, float x, int top, int y, int bottom,
                         Paint paint) {

            //draw 方法是重写的ImageSpan父类 DynamicDrawableSpan中的方法，在DynamicDrawableSpan类中，虽有getCachedDrawable()，
            // 但是私有的，不能被调用，所以调用ImageSpan中的getrawable()方法，该方法中 会根据传入的drawable ID ，获取该id对应的
            // drawable的流对象，并最终获取drawable对象
            Drawable drawable = getDrawable(); //调用imageSpan中的方法获取drawable对象
            canvas.save();

            //获取画笔的文字绘制时的具体测量数据
            Paint.FontMetricsInt fm = paint.getFontMetricsInt();

            //系统原有方法，默认是Bottom模式)
            int transY = bottom - drawable.getBounds().bottom;
            if (mVerticalAlignment == ALIGN_BASELINE) {
                transY -= fm.descent;
            } else if (mVerticalAlignment == ALIGN_FONTCENTER) {    //此处加入判断， 如果是自定义的居中对齐
                //与文字的中间线对齐（这种方式不论是否设置行间距都能保障文字的中间线和图片的中间线是对齐的）
                // y+ascent得到文字内容的顶部坐标，y+descent得到文字的底部坐标，（顶部坐标+底部坐标）/2=文字内容中间线坐标
                transY = ((y + fm.descent) + (y + fm.ascent)) / 2 - drawable.getBounds().bottom / 2;
            }

            canvas.translate(x, transY);
            drawable.draw(canvas);
            canvas.restore();
        }

        /**
         * 重写getSize方法，只有重写该方法后，才能保证不论是图片大于文字还是文字大于图片，都能实现中间对齐
         */
        public int getSize(Paint paint, CharSequence text, int start, int end, Paint.FontMetricsInt fm) {
            Drawable d = getDrawable();
            Rect rect = d.getBounds();
            if (fm != null) {
                Paint.FontMetricsInt fmPaint = paint.getFontMetricsInt();
                int fontHeight = fmPaint.bottom - fmPaint.top;
                int drHeight = rect.bottom - rect.top;

                int top = drHeight / 2 - fontHeight / 4;
                int bottom = drHeight / 2 + fontHeight / 4;

                fm.ascent = -bottom;
                fm.top = -bottom;
                fm.bottom = top;
                fm.descent = top;
            }
            return rect.right;
        }
    }
}
```
xml布局文件：
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">

    <TextView
        android:id="@+id/tv_test"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="#fffaa3"
        android:lineSpacingExtra="@dimen/dp100"
        android:textSize="16sp"/>

</LinearLayout>
```

>上面的已经是完整代码了，如果想直接下载运行，请到gitHub下载：[https://github.com/CnPeng/CrazyAndroid](https://github.com/CnPeng/CrazyAndroid)。该仓库中的b_01_spannableString_ImageSpan 对应该文中的内容

****
>写在最后，最近项目太紧了，过了年一直在加班。这次的总结也很仓促，本来想写的更细一些，并且也想把SpannableString的使用完整总结，but 时间太紧了，先这样吧，后面时间充足了再修正吧！

**欢迎各位指正文中错误的地方，一起交流，一起进步！**

参考链接：
[http://wangleyiang.iteye.com/blog/1771439](http://wangleyiang.iteye.com/blog/1771439)
[http://blog.csdn.net/gaoyucindy/article/details/39473135](http://blog.csdn.net/gaoyucindy/article/details/39473135)
[http://www.cnblogs.com/withwind318/p/5541267.html](http://www.cnblogs.com/withwind318/p/5541267.html)
[http://stackoverflow.com/questions/27631736/meaning-of-top-ascent-baseline-descent-bottom-and-leading-in-androids-font](http://stackoverflow.com/questions/27631736/meaning-of-top-ascent-baseline-descent-bottom-and-leading-in-androids-font)
[https://github.com/GcsSloop/AndroidNote/blob/master/CustomView/Advance/%5B99%5DDrawText.md](https://github.com/GcsSloop/AndroidNote/blob/master/CustomView/Advance/%5B99%5DDrawText.md)

