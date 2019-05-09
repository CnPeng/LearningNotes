# DialogFrament使用解析

只是单纯的给出示例，源码解析相关的内容可以查看 参考文档中的第一篇。

## 示例代码

在下面的示例代码中，我们自定义了一个 AntDialogFragment , 至于这个 Fragment 中显示什么内容，怎么显示，完全由传入的 View 或者 layoutId 决定。

### 1、AntDialogFragment.java


```java
/**
 * 作者：CnPeng
 * 时间：2019-04-30
 * 功用：自定义 dialogFragment
 * 其他：
 * 1、DialogFragment 中的主体内容实现全屏有三种方案：setStyle()、onStart、onActivityCreated 中设置 Window 的宽高
 * 2、参考地址为：
 *  https://www.jianshu.com/p/3ecad4bfc55e 、
 *  https://www.jianshu.com/p/87dbd6223a52
 *  https://guides.codepath.com/android/Using-DialogFragment
 */
public class AntDialogFragment extends DialogFragment {

    @LayoutRes
    private int  mLayoutId;
    private View mContentView;

    private AntDialogFragment() {
    }

    public static AntDialogFragment getInstance() {
        return new AntDialogFragment();
    }

    public void setLayoutId(int layoutId) {
        mLayoutId = layoutId;
    }

    public void setContentView(View contentView) {
        mContentView = contentView;
    }

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setStyle(STYLE_NORMAL, R.style.Dialog_FullScreen);
    }

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {

        if (null == mContentView) {
            mContentView = DataBindingUtil.inflate(inflater, mLayoutId, container, false).getRoot();
        }
        return mContentView;
    }

    @Override
    public void onStart() {
        super.onStart();
        Dialog dialog = getDialog();
        Window window;
        if (null != dialog && (null != (window = dialog.getWindow()))) {
            int width = WindowManager.LayoutParams.MATCH_PARENT;
            int height = WindowManager.LayoutParams.MATCH_PARENT;

            window.setLayout(width, height);
            window.setBackgroundDrawable(new ColorDrawable(Color.TRANSPARENT));
        }
    }

    @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {
        getDialog().getWindow().requestFeature(Window.FEATURE_NO_TITLE);
        super.onActivityCreated(savedInstanceState);
        getDialog().getWindow().setBackgroundDrawable(new ColorDrawable(0x00000000));
        getDialog().getWindow().setType(WindowManager.LayoutParams.TYPE_APPLICATION);
        getDialog().getWindow().setLayout(WindowManager.LayoutParams.MATCH_PARENT, WindowManager.LayoutParams.MATCH_PARENT);
    }
}
```

### 2、style.xml 中定义 Dialog.FullScreen 样式

```xml
<style name="Dialog.FullScreen" parent="Theme.AppCompat.Dialog">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowBackground">@color/transparent</item>
    <item name="android:windowIsFloating">false</item>
</style>
```

### 3、代码中调用自定义 DialogFramgment

```java
GuideTabSortBinding binding = DataBindingUtil.inflate(getLayoutInflater(), R.layout.guide_tab_sort, null, false);
AntDialogFragment dialogFragment = AntDialogFragment.getInstance();
dialogFragment.setContentView(binding.getRoot());

binding.ivTabSortGuide.setOnClickListener(v -> {
	// 点击之后关闭 DialogFragment
   dialogFragment.dismiss();
});

// 展示 DialogFrament , 第二个参数暂时用不到，可以任意定义
dialogFragment.show(mContext.getSupportFragmentManager(), "tabSortGuideIv");
```
## 参考地址

* [三句代码创建全屏Dialog或者DialogFragment：带你从源码角度实现](https://www.jianshu.com/p/3ecad4bfc55e) 、
* [DialogFragment 全屏显示](https://www.jianshu.com/p/87dbd6223a52)
* [Using DialogFragment](https://guides.codepath.com/android/Using-DialogFragment)
