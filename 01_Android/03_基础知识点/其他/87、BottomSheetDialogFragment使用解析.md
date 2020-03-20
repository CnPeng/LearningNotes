# BottomSheetDialogFramgent 使用解析

源码分析部分直接摘录 [原文链接: Android开发之BottomsheetDialogFragment的使用](https://blog.csdn.net/klxh2009/article/details/80393245)，这篇文章分析的挺好，所以，不再重复整理。

示例代码部分是借鉴上述文章中的内容和实际项目需要进行整理的。

## 一、源码分析：

### 1、源码分析

```java
package android.support.design.widget;

public class BottomSheetDialogFragment extends AppCompatDialogFragment {

    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {
        return new BottomSheetDialog(getContext(), getTheme());
    }
```

BottomSheetDialogFragment 的父类是 AppCompatDialogFragment，重写了 onCreateDialog() 方法，返回了一个 BottomSheetDialog 实例，该 dialog 的主题形式来自于 getTheme() 方法，getTheme() 是 DialogFragment 的公共方法。 

进入 BottomSheetDialog 的源码，dialog 的装载发生在 setContentView() 中: 
 
![](https://img-blog.csdn.net/20180521154020972?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tseGgyMDA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 

![](https://img-blog.csdn.net/20180521154031103?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tseGgyMDA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
 
并且在 onStart 方法里： 

![](https://img-blog.csdn.net/20180521154146890?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tseGgyMDA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
 
因此，重写该方法可以定义 BottomSheetDialog 的初始状态。

### 2、布局、样式

在 design 包下，我们找到 `design_bottom_sheet_dialog.xml`，如下： 

![](https://img-blog.csdn.net/20180521154307609?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tseGgyMDA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/container"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <android.support.design.widget.CoordinatorLayout
        android:id="@+id/coordinator"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true">

        <View
            android:id="@+id/touch_outside"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:importantForAccessibility="no"
            android:soundEffectsEnabled="false"
            tools:ignore="UnusedAttribute"/>

        <FrameLayout
            android:id="@+id/design_bottom_sheet"
            style="?attr/bottomSheetStyle"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal|top"
            app:layout_behavior="@string/bottom_sheet_behavior"/>

    </android.support.design.widget.CoordinatorLayout>

</FrameLayout>
```

由上可知，装载 dialog 的容器是 id 为 `design_bottom_sheet` 的 FrameLayout，它指定了一个 style —— `?attr/bottomSheetStyle` 。

我们可以通过自定义如下 Theme 来调整 BottomSheetDialog 的样式：bottomSheetStyle，ctrl + 单击进入该样式，再在打开的文件中搜索 ”bottomSheetStyle”，显示如下： 

![](https://img-blog.csdn.net/20180521154403127?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tseGgyMDA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
 
由图片可知，它引用于 `@style/Widget.Design.BottomSheet.Modal`，继续进入，发现它包含了如下属性： 

![](https://img-blog.csdn.net/20180521154424381?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tseGgyMDA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
 
很明显，background 属性定义了 dialog 的背景色，因此，我们可以重新定义这些属性，如下（注意 parent=”android:Widget”）：

```xml
<style name="TransparentBottomSheetStyle" parent="Theme.Design.Light.BottomSheetDialog">
    <item name="bottomSheetStyle">@style/SheetStyle</item>
</style>

<style name="SheetStyle" parent="android:Widget">
    <item name="android:background">@android:color/transparent</item>
    <item name="behavior_peekHeight">auto</item>
    <item name="behavior_hideable">true</item>
    <item name="behavior_skipCollapsed">false</item>
</style>
```


## 二、示例代码：

### 1、 自定义 AntBottomSheetDialogFragment.java

```java
/**
 * 作者：CnPeng
 * 时间：2019/4/26
 * 功用：底部弹出的 DialogFragment
 * 其他：
 * 参考链接1： https://blog.csdn.net/klxh2009/article/details/80393245
 * 参考链接2：https://blog.csdn.net/xiaxl/article/details/80981336
 */
public class AntBottomSheetDialogFragment extends BottomSheetDialogFragment {

    /**
     * dialog 的主体 view 的id
     */
    private int                              mContentLayoutId;
    /**
     * dialog 的主体view
     */
    private View                             mContentView;
    @StyleRes
    private int                              mDialogStyle;
    /**
     * dialog 的顶部距离屏幕顶部的间距
     */
    private int                              mYOffset;
    private BottomSheetBehavior<FrameLayout> mBehavior;
    /**
     * dialog 的高度
     */
    private int                              mDialogHeight;
    private double                           mHeightPercent;
    /**
     * dialogFragment 的初始状态，默认为展开
     */
    private int                              mStartBehavior = BottomSheetBehavior.STATE_EXPANDED;
    /**
     * 下拉时停顿的位置，不需要停顿的话直接设置为0；-1 表示使用默认停顿位置时；也可以传递其他值进行自定义
     */
    private int                              mPeekHeight    = -1;
    /**
     * 是否跳过下拉时的停顿直接隐藏视图，默认false
     */
    private boolean                          mSkipCollapsed;

    public AntBottomSheetDialogFragment(@LayoutRes int layoutId) {
        mContentLayoutId = layoutId;
    }

    public AntBottomSheetDialogFragment(View contentView) {
        mContentView = contentView;
    }

    /**
     * dialog 的样式
     */
    public void setDialogStyle(int dialogStyle) {
        mDialogStyle = dialogStyle;
    }

    /**
     * dialog 的顶部距离屏幕顶部的偏移量
     */
    public void setYOffset(int yOffset) {
        mYOffset = yOffset;
    }

    /**
     * 设置 dialogFramgent 的初始状态，默认展开
     *
     * @param startBehavior 取值示例 {@link BottomSheetBehavior.STATE_EXPANDED} 等
     */
    public void setStartBehavior(int startBehavior) {
        mStartBehavior = startBehavior;
    }

    @NonNull
    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {

        if (null != getActivity()) {
            return new BottomSheetDialog(getActivity(), 0 == mDialogStyle ? R.style.TransparentBottomSheetStyle : mDialogStyle);
        }

        return super.onCreateDialog(savedInstanceState);
    }

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {

        if (null == mContentView) {
            mContentView = inflater.inflate(mContentLayoutId, container, false);
        }

        return mContentView;
    }

    @Override
    public void onStart() {
        super.onStart();

        //CnPeng 2019/4/26 3:48 PM 默认不显示软键盘
        Window window = getDialog().getWindow();
        if (null != window) {
            window.setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_STATE_HIDDEN);
        }

        //CnPeng 2019/4/26 3:49 PM 控制dialog的高度 以及 默认状态
        BottomSheetDialog dialog = (BottomSheetDialog) getDialog();
        FrameLayout bottomSheet = dialog.getDelegate().findViewById(com.google.android.material.R.id.design_bottom_sheet);
        if (null != bottomSheet) {
            CoordinatorLayout.LayoutParams layoutParams = (CoordinatorLayout.LayoutParams) bottomSheet.getLayoutParams();
            layoutParams.height = getDialogHeight();

            mBehavior = BottomSheetBehavior.from(bottomSheet);
            // 初始为展开状态
            if (null != mBehavior) {
                mBehavior.setState(mStartBehavior);
                mBehavior.setPeekHeight(mPeekHeight);
                mBehavior.setSkipCollapsed(mSkipCollapsed);
            }
        }
    }

    /**
     * 获取dialog的高度
     *
     * @return 去除距离顶部距离之后的dialog的高度
     */
    private int getDialogHeight() {
        if (null != getActivity() && 0 == mDialogHeight) {
            WindowManager windowManager = (WindowManager) getActivity().getSystemService(Context.WINDOW_SERVICE);

            if (null != windowManager) {
                Point point = new Point();
                windowManager.getDefaultDisplay().getSize(point);

                mDialogHeight = point.y - mYOffset;
            }
        }
        return (int) (mDialogHeight * (0 == mHeightPercent ? 1 : mHeightPercent));
    }

    public void setDialogHeight(int dialogHeight) {
        mDialogHeight = dialogHeight;
    }

    /**
     * 设置dialog的高度
     *
     * @param heightPercent dialog的高度占屏幕高度的百分比
     */
    public void setDialogHeight(double heightPercent) {
        mHeightPercent = heightPercent;
    }

    public View getContentView() {
        return mContentView;
    }


    public void close() {
        if (null != mBehavior) {
            mBehavior.setState(BottomSheetBehavior.STATE_HIDDEN);
        }
    }

    public void setPeekHeight(int peekHeight) {
        mPeekHeight = peekHeight;
    }

    public void setSkipCollapsed(boolean skipCollapsed) {
        mSkipCollapsed = skipCollapsed;
    }
}
```

**注意**：<p>
 在上述代码中，获取 FrameLayout 控件时，由于我的项目已经迁移到 AndroidX，并且 gradle 中依赖了 `com.google.android.material:material:1.0.0`，所以，是通过 `com.google.android.material.R.id.design_bottom_sheet` 去获取的。<p>
 如果没有迁移到 AndroidX ,而是依旧使用 support, 则需要使用 `android.support.design.R.id.design_bottom_sheet` 去获取。<p>
此外，`dialog.getDelegate()` 是 AppCompatDialog 中的一个方法，获取的是 AppCompatDelegate 的实例：

```java
 /**
 * @return The {@link AppCompatDelegate} being used by this Dialog.
 */
public AppCompatDelegate getDelegate() {
    if (mDelegate == null) {
        mDelegate = AppCompatDelegate.create(this, this);
    }
    return mDelegate;
}
```
 —— 更具体的来说应该是：AppCompatDelegateImpl 的实例，
 
```java
public static AppCompatDelegate create(Dialog dialog, AppCompatCallback callback) {
    return new AppCompatDelegateImpl(dialog.getContext(), dialog.getWindow(), callback);
}
```

在 AppCompatDelegateImpl 中则实现了 findViewById 方法：

```java
public <T extends View> T findViewById(@IdRes int id) {
   ensureSubDecor();
   // mWindow 的定义为： final Window mWindow
   return (T) mWindow.findViewById(id);
}
```

Window 中的 findViewById 如下：

```java
@Nullable
public <T extends View> T findViewById(@IdRes int id) {
	 // 最终还是调用 View 的 findViewById
    return getDecorView().findViewById(id);
}
```

### 2、调用自定义的 AntBottomSheetDialogFragment

```java
 // 填充并展示
 View mContentView = initTabSortDialogFragment();

 mBottomSheetDialogFragment = new AntBottomSheetDialogFragment(mContentView);
 mBottomSheetDialogFragment.setSkipCollapsed(true);
 mBottomSheetDialogFragment.setPeekHeight(0);
 //CnPeng 2019-04-26 19:44 第二个参数暂时没啥用处
 mBottomSheetDialogFragment.show(mFragmentManager, "bottomSheetDialog");
 
 ...
 // 关闭
 // CnPeng 2019-04-28 22:02 实现左上角 X 的事件
dialogBinding.ivClose.setOnClickListener(v -> {
    if (null != mBottomSheetDialogFragment) {
        mBottomSheetDialogFragment.dismiss();
    }
});
```

### 3、style.xml 中定义 AntBottomSheetStyle

```xml
<style name="TransparentBottomSheetStyle" parent="Theme.Design.Light.BottomSheetDialog">
    <item name="bottomSheetStyle">@style/AntBottomSheetStyle</item>
</style>

<style name="AntBottomSheetStyle" parent="android:Widget">
    <item name="android:background">@android:color/transparent</item>
    <item name="behavior_peekHeight">auto</item>
    <item name="behavior_hideable">true</item>
    <item name="behavior_skipCollapsed">false</item>
</style>
```

## 参考链接：

[(优先参考) Android开发之BottomsheetDialogFragment的使用](https://blog.csdn.net/klxh2009/article/details/80393245)

[BottomSheetDialogFragment使用的注意点](https://www.jianshu.com/p/7fcec871ea36)

[Keyboard hides BottomSheetDialogFragment](https://stackoverflow.com/questions/44625365/keyboard-hides-bottomsheetdialogfragment)

[BottomSheetDialogFragment 使用方式、方法执行顺序、源码解析](https://blog.csdn.net/xiaxl/article/details/80981336)
