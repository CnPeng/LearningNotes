##1 主要知识点介绍
乍一看标题，你觉得很简单，你可能心里再想 ——“呵，这么简单的东西有什么看头，谁还不会写个输入框！” 所以呢，为了让你能有看下去的欲望，我先把用到的主要知识点列一下吧。如果你看过了主要知识点之后觉得有点看头就请继续。
>主要知识点：
* 自定义属性的时候如何使用 android 已有的属性？
* 当某个属性取值是颜色，并且既支持 #fff 也支持 @color/white 时，如何获取其值并设置给相应控件？
* 当某个属性的取值是数值，并且既支持 px 也支持 dp 时，如何获取其值并设置给相应控件？
* 分析 TextView 源码中 setTextSize( ) 的实现，并通过反射获取父类中私有的方法并使用
* 回调的使用

##2 需求分析
这是一个什么样的需求呢？其实很简单，就是一个输入框，能输入文字，而且具有删除按钮，输入文字之后呢，点击一下删除按钮就能将文字清空，就像下面这个样子：

![Test.gif](http://upload-images.jianshu.io/upload_images/2551993-80d20d5e0cb68e21.gif?imageMogr2/auto-orient/strip)

是不是很简单？对的，很简单，就是一个线性布局或者相对布局，然后包含EditText和一个ImageView，为了方便复用呢，我们就通过自定义将它封装成一个控件。

实现的最终功能及效果包含如下：
> 
 * 内容为空时不显示删除图标 √
 * 焦点改变时不显示删除图标 √
 * 删除图标的点击事件      √
 * 内容变化时控制删除图标的展示 √
 * EditText 的字号，颜色，hint 的字号，颜色， maxLines, inputType ,maxLength √
 * 暴露替换删除按钮图片的属性、图标和文字之间的padding设置属性 √

##3 如何实现？
###（1）自定义属性时如何使用原生的属性？
自定义属性时有这么三种方式：
> * 为每一个自定义控件声明一组属性。如下：

```
 <declare-styleable name="RoundAngleImageView">
        <attr name="Width" format="dimension"/>
        <attr name="Height" format="dimension"/>
    </declare-styleable>

    <!--CnPeng 自定义的自动搜索框-->
    <declare-styleable name="CustomAutoSearchET">
        <attr name="Width" format="dimension"/>
        <attr name="Height" format="dimension"/>
        <attr name="parentPadding_right" format="dimension"/>
        <attr name="parentPadding_bottom" format="dimension"/>
        <attr name="parentPadding" format="dimension"/>
    </declare-styleable>
```
上面这种方式呢，就是在每一个自定义控件中声明了一组属性，像上面的 width 和 height 其实完全可以抽取出来——也就是第二种方式：
>* 将多个控件中都声明的相同属性抽取 , 在外部定义，各个控件中直接引用，不再需要重复定义format。具体如下：

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
      <attr name="Width" format="dimension"/>
      <attr name="Height" format="dimension"/>

      <declare-styleable name="RoundAngleImageView">
         <attr name="Width" />
         <attr name="Height" />
      </declare-styleable>

     <!--CnPeng 自定义的自动搜索框-->
     <declare-styleable name="CustomAutoSearchET">
        <attr name="Width" />
        <attr name="Height"/>
        <attr name="parentPadding_right" format="dimension"/>
        <attr name="parentPadding_bottom" format="dimension"/>
        <attr name="parentPadding" format="dimension"/>
     </declare-styleable>
</resources>
```
>* 直接引用 android 系统已经定义过的属性。具体如下

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="EditTextWithDel">
        <attr name="android:textSize"/>
        <attr name="android:textColor"/>
        <attr name="android:textColorHint"/>
        <attr name="android:maxLines"/>
        <attr name="android:inputType"/>
        <attr name="android:maxLength"/>
        <attr name="android:drawableRight"/>
        <attr name="android:drawablePadding"/>
        <attr name="android:hint"/>
        <attr name="android:text"/>
        <attr name="android:ellipsize"/>
        <attr name="android:cursorVisible"/>
        <attr name="android:textColorLink"/>
        <attr name="android:autoLink"/>
        <attr name="android:gravity"/>
        <attr name="android:enabled"/>
        <attr name="showRightDrawable" format="boolean"/>
    </declare-styleable>
</resources>
```
瞧见没，就是上面这个样子，如果你要定义的某个属性android已经定义过了，那么就直接拿来使用吧。格式：    `    <attr name="android:text"/>` 由于是引用的 android 的，所以在引用时必须加上 ` android:  `

###(2) 当某个属性取值是颜色，并且既支持 #fff 也支持 @color/white 时，如何获取其值并设置给相应控件？

其实对于这种情况呢，以前我也不会，后来翻了下 TextView 的源码，发现源码里是用 ColorStateList 来接收值的。代码如下：
```
case R.styleable.EditTextWithDel_android_textColor:
       
       ColorStateList textColor = a.getColorStateList(attr);  // a 就是 TypeArray 对象
       editText.setTextColor(textColor);
break;
```
那么 ColorStateList  是个啥呢？先摘录一段 ColorStateList 类中的注释

 ```
ColorStateLists are created from XML resource files defined in the "color" subdirectory directory of an application's resource directory. 
The XML file contains a single "selector" element with a number of "item" elements inside. For example:

   <selector xmlns:android="http://schemas.android.com/apk/res/android">
     <item android:state_focused="true"
             android:color="@color/sample_focused" />
     <item android:state_pressed="true"
             android:state_enabled="false"
             android:color="@color/sample_disabled_pressed" />
     <item android:state_enabled="false"
             android:color="@color/sample_disabled_not_pressed" />
     <item android:color="@color/sample_default" />
   </selector>
```
啥意思呢？我的理解就是，**android 底层中某个控件颜色的定义其实是成对的 -- 以 selector 的形式展现的，ColorStateList 就是用来接受selector 中的颜色值的。也就是说 ColorStateList 中不论何时都会有两个值，但同一时刻只能展示其中的一种值。**

所以呢，在我们获取某个属性的颜色值时，不需要去关心是 16进制的 #fff 还是 10进制的 @color/white ，我们直接通过 ColorStateList  去获取即可。（其实，我以前也是在纠结，如果我传入的值是 #FFF 我在获取属性值的时候该怎么处理呢）

###(3) 当某个属性的取值是数值，并且既支持 px 也支持 dp 时，如何获取其值并设置给相应控件？

TypeArray 中有 这么个方法：getDimensionPixelSize( attr , def )。有了这个方法之后，我们也不需要关心传入的是 dp , px ,还是 sp ,总之最终都会通过该方法转成 px。attr 是需要取值的属性名称

让我们瞅瞅TextView 中的源码是怎么写的：

```
 final int textSize = ta.getDimensionPixelSize(R.styleable.TextAppearance_textSize, 0);
        if (textSize != 0) {
            setRawTextSize(textSize);
        }
```
上面的代码中，通过 getDimensionPixelSize( ) 获取到相应的 PX 值之后，通过私有的 setRawSize( ) 设置字体的大小。

###（4）如何通过反射获取父类中私有的方法并使用?
反射是 java 中的知识点，在 android 开发时使用频率也是蛮高的，比如我们这个自定义 带有删除按钮的输入框 中就有使用。

在 （3）中，我们知道获取 textSize 的时候可以直接使用 ta.getDimensionPiexlSize( ) ，如果我要将这个值设置给 text 就必须通过 setRawTextSize( ) ——因为 如果直接调用 setTextSize( )  得到的大小是不对的！！！我们看 setText( ) 源码：

```

    @android.view.RemotableViewMethod
    public void setTextSize(float size) {
        setTextSize(TypedValue.COMPLEX_UNIT_SP, size);
    }


    public void setTextSize(int unit, float size) {
        Context c = getContext();
        Resources r;

        if (c == null)
            r = Resources.getSystem();
        else
            r = c.getResources();

        setRawTextSize(TypedValue.applyDimension(
                unit, size, r.getDisplayMetrics()));
    }

    private void setRawTextSize(float size) {
        if (size != mTextPaint.getTextSize()) {
            mTextPaint.setTextSize(size);

            if (mLayout != null) {
                nullLayouts();
                requestLayout();
                invalidate();
            }
        }
    }
```
瞅见没，setTextSize( float) 中会调用 setTextSize( TypedValue.COMPLEX_UNIT_SP ，size) , 这个方法会给你传入的值加上一个单位—— **SP ！！**。在两个参数的 setTextSize( ) 中又去调了 setRawTextSize( ) ，但是要注意，这里 setRawTextSize( ) 的参数是 `TypedValue.applyDimension( unit, size, r.getDisplayMetrics())`。我们瞅瞅 applyDimension( ) 中干了啥。

```
 public static float applyDimension(int unit, float value,
                                       DisplayMetrics metrics)
    {
        switch (unit) {
        case COMPLEX_UNIT_PX:
            return value;
        case COMPLEX_UNIT_DIP:
            return value * metrics.density;
        case COMPLEX_UNIT_SP:
            return value * metrics.scaledDensity;
        case COMPLEX_UNIT_PT:
            return value * metrics.xdpi * (1.0f/72);
        case COMPLEX_UNIT_IN:
            return value * metrics.xdpi;
        case COMPLEX_UNIT_MM:
            return value * metrics.xdpi * (1.0f/25.4f);
        }
        return 0;
    }
```
呵，这个方法也是将传入的值转成 px 。but , **由于在setTextSize( ) 方法中，我们传入的 float 值被强加了 一个单位 —— sp , 所以在上面的方法中 还会把我们传入的 值 再乘上一个屏幕密度，然后。。。我们将 ta.getDimensionPiexlSize( ) 获取到的 px 再通过 setTextSize( ) 设置给文字时，得到的文字大小就出错了。。。**

所以呢，当我们通过 ta.getDimensionPiexlSize( ) 获取字体大小时就需要调用 setRawTextSize( )去设置，但是， 我们在上面也看到了，setRawTextSize( ) 是 private 私有的！！这样，我们就需要祭出 反射 神器了。代码如下：

```
case R.styleable.EditTextWithDel_android_textSize:
        int textSize=a.getDimensionPixelSize(attr,16);

        //170621 使用反射的方式获取EditText 的父类 TextView 中的私有方法-- setRawTextSize(float)
        Class clazz=editText.getClass().getSuperclass();  //获取TextView的class
        try{
        Method method=clazz.getDeclaredMethod("setRawTextSize",float.class); //暴力反射获取全部方法，含私有和公有
        method.setAccessible(true);     //访问权限设置为true
        method.invoke(editText,textSize); //调用反射方法，第一个参数表示是哪个对象调用反射获取的方法，第二个参数表示方法的参数
        }catch(Exception e){
        e.printStackTrace();
        }
break;                
```
在上面的代码中：
>* `Class clazz = editText.getClass().getSupperclass() `是获取 editText 的父类 TextView 的字节码文件
* `Method method=clazz.getDeclaredMethod("setRawTextSize",float.class); `中第一个参数表示需要获取的方法名，第二个参数表示 该方法的参数字节码。需要注意，第二个参数实际是一个可变数组！！所以，这句代码的意思就是，通过暴力反射获取TextView 中 的 setRawTextSize( ) 方法，该方法的参数 是  float 类型。（**暴力反射既能获取到公有方法也能获取私有方法**）
* `method.setAccessible(true); ` 取得对反射到的方法操作的权限
* ` method.invoke(editText,textSize);` 中，第一个参数表示是谁在调用被反射到的方法，第二个参数表示要传递给方法的参数。这句代码就是说，调用被反射到的方法并传入参数。

**170703 补充：其实，在上面设置字体大小的时候，可以不使用反射，直接使用两个参数的  setTextSize(int , float) , 第一个参数表示传入 的 float 值的单位 ， 第二个代表你想要的字体大小。具体如下：**
>       editText.setTextSize(COMPLEX_UNIT_PX, textSize);    //170703 不使用反射方式时用这种方法


###（5） 回调的使用
在自定义这个带有删除按钮的输入框时，我们用一个 单独的 ImageView 来展示。当EditText 中文本发生变化时我们动态的控制 这个删除按钮的展示和隐藏，那么这样我们在控件内部就需要去调用 onTextChangeListener 监听。

但是，如果我们还需要在使用这个自定义控件的界面中，继续去监听文本变化时该怎么办呢？我不能再去调用 onTextChangeListener
 了，因为这样会把控件内部 监听覆盖，然后 删除按钮的事件就不好使了。。。那我到底该咋办呢？？还能怎么办？写个回调呗。。。看代码：

```
 editText.addTextChangedListener(new TextWatcher() { //文字变化
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {

            }

            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
                if (s.length() > 0) {
                    iv_del.setVisibility(View.VISIBLE);
                } else {
                    iv_del.setVisibility(View.GONE);
                }

                if (onTextChangedListener != null) {
                    onTextChangedListener.onTextChanged(s, start, before, count);
                }
            }

            @Override
            public void afterTextChanged(Editable s) {
                if (editText.isFocused() && s.length() > 0) {
                    iv_del.setVisibility(View.VISIBLE);
                } else {
                    iv_del.setVisibility(View.GONE);
                }
            }
        });


    /**
     * 自定义文本变化监听，
     */
    public interface OnTextChangedListener {
        void onTextChanged(CharSequence s, int start, int before, int count);
    }

    /**
     * 对外暴露设置自定义的文本变化监听，
     */
    public void setOnTextChangedListener(OnTextChangedListener onTextChangedListener) {
        this.onTextChangedListener = onTextChangedListener;
    }

    OnTextChangedListener onTextChangedListener;
```
有了这个回调之后，当我们在使用这个自定义控件的界面中还需要监听文本变化时就去实现这个回调，这样，就不会担心监听器冲突的问题了。

##4 完整代码
完整控件代码：

```
/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/6/20:下午4:49
 * <p>
 * 说明：
 * 内容为空时不显示删除图标 √
 * 焦点改变时不显示删除图标 √
 * 删除图标的点击事件      √
 * 内容变化时控制删除图标的展示 √
 * EditText 的字号，颜色，hint 的字号，颜色， maxLines, inputType ,maxLength √
 * 暴露替换删除按钮图片的属性、图标和文字之间的padding设置属性 √
 */
public class EditTextWithDel extends LinearLayout {

    private Context      mContext;
    private LinearLayout ll_root;
    private EditText     editText;
    private ImageView    iv_del;

    public EditTextWithDel(Context paramContext) {
        this(paramContext, null);
    }

    public EditTextWithDel(Context paramContext, AttributeSet paramAttributeSet) {
        this(paramContext, paramAttributeSet, 0);
    }

    public EditTextWithDel(Context paramContext, AttributeSet paramAttributeSet, int defStyleAttr) {
        super(paramContext, paramAttributeSet, defStyleAttr);
        mContext = paramContext;
        initView();

        initAttr(paramAttributeSet, defStyleAttr);
    }

    private void initAttr(AttributeSet paramAttributeSet, int defStyleAttr) {
        TypedArray a = mContext.obtainStyledAttributes(paramAttributeSet, R.styleable.EditTextWithDel, defStyleAttr, 0);

        int n = a.getIndexCount();
        for (int i = 0; i < n; i++) {
            int attr = a.getIndex(i);

            switch (attr) {
                case R.styleable.EditTextWithDel_android_autoLink:
                    int mAutoLinkMask = a.getInt(attr, 0);
                    editText.setAutoLinkMask(mAutoLinkMask);
                    break;

                case R.styleable.EditTextWithDel_android_drawableRight:
                    Drawable drawableRight = a.getDrawable(attr);
                    iv_del.setImageDrawable(drawableRight);
                    break;

                case R.styleable.EditTextWithDel_android_drawablePadding:
                    int drawablePadding = a.getDimensionPixelSize(attr, 0);
                    iv_del.setPadding(drawablePadding, 0, 0, 0);
                    break;

                case R.styleable.EditTextWithDel_android_maxLines:
                    editText.setMaxLines(a.getInt(attr, -1));
                    break;

                case R.styleable.EditTextWithDel_android_hint:
                    CharSequence hint = a.getText(attr);
                    editText.setHint(hint);
                    break;

                case R.styleable.EditTextWithDel_android_text:
                    CharSequence text = a.getText(attr);
                    editText.setText(text);
                    break;

                case R.styleable.EditTextWithDel_android_enabled:
                    editText.setEnabled(a.getBoolean(attr, isEnabled()));
                    break;
                case R.styleable.EditTextWithDel_android_ellipsize:
                    int ellipsize = a.getInt(attr, -1);
                    if (editText.getMaxLines() == 1 && editText.getKeyListener() == null && ellipsize < 0) {
                        ellipsize = 3; // END
                    }

                    switch (ellipsize) {
                        case 1:
                            editText.setEllipsize(TextUtils.TruncateAt.START);
                            break;
                        case 2:
                            editText.setEllipsize(TextUtils.TruncateAt.MIDDLE);
                            break;
                        case 3:
                            editText.setEllipsize(TextUtils.TruncateAt.END);
                            break;
                        case 4:
                            editText.setEllipsize(TextUtils.TruncateAt.MARQUEE);
                            break;
                        default:
                            break;
                    }
                    break;

                case R.styleable.EditTextWithDel_android_cursorVisible:
                    if (!a.getBoolean(attr, true)) {
                        editText.setCursorVisible(false);
                    }
                    break;

                case R.styleable.EditTextWithDel_android_maxLength:
                    int maxlength = a.getInt(attr, -1);
                    if (maxlength >= 0) {
                        editText.setFilters(new InputFilter[]{new InputFilter.LengthFilter(maxlength)});
                    } else {
                        editText.setFilters(new InputFilter[0]);
                    }
                    break;


                case R.styleable.EditTextWithDel_android_textColor:
                    ColorStateList textColor = a.getColorStateList(attr);
                    editText.setTextColor(textColor);
                    break;

                case R.styleable.EditTextWithDel_android_textColorHint:
                    ColorStateList textColorHint = a.getColorStateList(attr);
                    editText.setHintTextColor(textColorHint);
                    break;

                case R.styleable.EditTextWithDel_android_textColorLink:
                    ColorStateList textColorLink = a.getColorStateList(attr);
                    editText.setLinkTextColor(textColorLink);
                    break;

                case R.styleable.EditTextWithDel_android_textSize:
                    int textSize = a.getDimensionPixelSize(attr, 16);

                    //170621 使用反射的方式获取EditText 的父类 TextView 中的私有方法-- setRawTextSize(float)
                    Class clazz = editText.getClass().getSuperclass();  //获取TextView的class
                    try {
                        Method method = clazz.getDeclaredMethod("setRawTextSize", float.class); //暴力反射获取全部方法，含私有和公有
                        method.setAccessible(true);     //访问权限设置为true
                        method.invoke(editText, textSize); //调用反射方法，第一个参数表示是哪个对象调用反射获取的方法，第二个参数表示方法的参数
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                    break;

                case R.styleable.EditTextWithDel_android_inputType:
                    int inputType = a.getInt(attr, EditorInfo.TYPE_NULL);
                    editText.setInputType(inputType);
                    break;
                case R.styleable.EditTextWithDel_android_gravity:
                    editText.setGravity(a.getInt(attr, -1));
                    break;
                case R.styleable.EditTextWithDel_showRightDrawable:
                    boolean show = a.getBoolean(attr, true);
                    iv_del.setVisibility(show ? View.VISIBLE : View.GONE);
                    break;
            }
        }
        a.recycle();
    }

    private void initView() {
        LayoutInflater.from(mContext).inflate(R.layout.custom_edittext_withdel, this);
        ll_root = (LinearLayout) findViewById(R.id.ll_root_ETwithDel);
        editText = (EditText) findViewById(R.id.et_input_ETwithDel);
        iv_del = (ImageView) findViewById(R.id.iv_del_ETwithDel);
        initEvent();
    }

    /**
     * 初始化事件，点击，删除，文字变化
     */
    private void initEvent() {
        iv_del.setOnClickListener(new View.OnClickListener() {   //清空内容
            @Override
            public void onClick(View v) {
                editText.setText(null);
            }
        });

        editText.addTextChangedListener(new TextWatcher() { //文字变化
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {

            }

            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
                if (s.length() > 0) {
                    iv_del.setVisibility(View.VISIBLE);
                } else {
                    iv_del.setVisibility(View.GONE);
                }

                if (onTextChangedListener != null) {
                    onTextChangedListener.onTextChanged(s, start, before, count);
                }
            }

            @Override
            public void afterTextChanged(Editable s) {
                if (editText.isFocused() && s.length() > 0) {
                    iv_del.setVisibility(View.VISIBLE);
                } else {
                    iv_del.setVisibility(View.GONE);
                }
            }
        });

        editText.setOnFocusChangeListener(new OnFocusChangeListener() {    //焦点变化时也去控制 删除按钮的显示和隐藏
            @Override
            public void onFocusChange(View v, boolean hasFocus) {
                if (hasFocus) {
                    if (editText.getText().length() > 0) {
                        iv_del.setVisibility(View.VISIBLE);
                    }
                } else {
                    iv_del.setVisibility(View.GONE);
                }
            }
        });
    }


    /**
     * 返回EditText中的文本数据
     */
    public Editable getText() {
        return editText.getText();
    }

    /**
     * 设置文本到EditText中
     */
    public void setText(CharSequence charSequence) {
        editText.setText(charSequence);
    }

    /**
     * 设置EditText的选中
     */
    public void setSelection(int index) {
        editText.setSelection(index);
    }

    /**
     * 为EditText设置过滤器
     */
    public void setFilters(InputFilter[] filters) {
        editText.setFilters(filters);
    }

    /**
     * 自定义文本变化监听，
     */
    public interface OnTextChangedListener {
        void onTextChanged(CharSequence s, int start, int before, int count);
    }

    /**
     * 对外暴露设置自定义的文本变化监听，
     */
    public void setOnTextChangedListener(OnTextChangedListener onTextChangedListener) {
        this.onTextChangedListener = onTextChangedListener;
    }

    OnTextChangedListener onTextChangedListener;
}

```

布局文件：

```
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout
    android:id="@+id/ll_root_ETwithDel"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center"
    android:orientation="horizontal">

    <EditText
        android:id="@+id/et_input_ETwithDel"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:background="@null"
        android:hint="请输入内容"
        android:maxLines="1"
        android:textColor="@color/c_3e3e3e"
        android:textColorHint="@color/acacac"
        android:textSize="14sp"/>
    <ImageView
        android:id="@+id/iv_del_ETwithDel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="@dimen/dp5"
        android:src="@drawable/del"
        android:visibility="gone"/>

</LinearLayout>

```
属性文件：

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="EditTextWithDel">
        <attr name="android:textSize"/>
        <attr name="android:textColor"/>
        <attr name="android:textColorHint"/>
        <attr name="android:maxLines"/>
        <attr name="android:inputType"/>
        <attr name="android:maxLength"/>
        <attr name="android:drawableRight"/>
        <attr name="android:drawablePadding"/>
        <attr name="android:hint"/>
        <attr name="android:text"/>
        <attr name="android:ellipsize"/>
        <attr name="android:cursorVisible"/>
        <attr name="android:textColorLink"/>
        <attr name="android:autoLink"/>
        <attr name="android:gravity"/>
        <attr name="android:enabled"/>
        <attr name="showRightDrawable" format="boolean"/>
    </declare-styleable>
</resources>
```

## 5 附录：
[源码地址：https://github.com/CnPeng/CrazyAndroid.git](https://github.com/CnPeng/CrazyAndroid.git)
该文中的内容对应 项目中 c_00_CommonCustomView 中的EditTextWithDel.java

**注意：文章中的代码是我直接从我们项目中摘录的完善之后的，会和 gitHub 上的有略微差异**
