## 一、需求

当 PopupWindow 中的内容是通过 RecyclerView/ListView 等动态填充的列表内容时，需要根据内容动态控制其高度或者宽度。


## 二、思路

当 PopupWindow 调用了 `showXXX()` 方法之后，通过 contentView 的 viewTreeObserver  监听该 contentView 的布局事件—— `viewTreeObserver.addOnGlobalLayoutListener(listener)`，在其中判断如果超出了最大限制，就获取 contentView 的 layoutParams, 并将 maxHeight 设置给 layoutParams。

需要注意的是：

* 必须在 `showXXX()` 去设置最大高度值，因为只有 show 之后才会触发 layout 事件
* 也可以通过 viewTreeObser 监听 preDrawListener.

核心代码如下：

```java
/**
 * CnPeng:2019-06-27 19:09 设置最大宽高
 */
public void setMaxLayoutParams(final int maxH, final int maxW) {
    if (null != mPopContentView) {

        ViewTreeObserver viewTreeObserver = mPopContentView.getViewTreeObserver();
        viewTreeObserver.addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
            @Override
            public void onGlobalLayout() {

                if (0 != maxH) {
                    int popContentHeight = mPopContentView.getHeight();
                    if (popContentHeight > maxH) {
                        LayoutParams layoutParams = mPopContentView.getLayoutParams();
                        layoutParams.height = maxH;
                        mPopContentView.setLayoutParams(layoutParams);
                    }
                }

                if (0 != maxW) {
                    int popContentWidth = mPopContentView.getWidth();
                    if (popContentWidth > maxW) {
                        LayoutParams layoutParams = mPopContentView.getLayoutParams();
                        layoutParams.height = maxH;
                        mPopContentView.setLayoutParams(layoutParams);
                 	}
             	  }

             	  mPopContentView.getViewTreeObserver().removeOnGlobalLayoutListener(this);
             }
         });
     }
 }
```

## 三、完整示例

### 1、自定义 PopupWindow 

不自定义直接使用 PopupWindow 也行，核心是 `setMaxLayoutParams(h,w)` 方法。

下面的内容是直接从项目中摘录的。

```java
/**
 * CnPeng 2019-06-28 11:04 可以动态控制最大高度和宽度的 pop
 */
public class AntPopupWindow extends PopupWindow {
    /**
     * 全屏
     */
    public static final int TYPE_FULL_SCREEN  = 1;
    /**
     * 包裹内容
     */
    public static final int TYPE_WRAP_CONTENT = 2;
    /**
     * 宽度满屏(课程表，选周的时候使用到)
     */
    public static final int TYPE_MATCH_WIDTH  = 3;

    private View mPopContentView;

    public AntPopupWindow(Context context, View view) {
        this(context, view, TYPE_WRAP_CONTENT);
    }

    public AntPopupWindow(Context context, View view, int popLayoutParamsType) {
        super(context);
        mPopContentView = view;
        // 设置按钮监听，设置SelectPicPopupWindow的View
        setContentView(mPopContentView);

        setLayoutParams(popLayoutParamsType);

        // 设置SelectPicPopupWindow弹出窗体可点击
        setFocusable(true);
        // 实例化一个ColorDrawable颜色为半透明
        ColorDrawable dw = new ColorDrawable(0000000000);
        setBackgroundDrawable(dw);
        setOutsideTouchable(true);
    }

    /**
     * CnPeng:2019-06-27 19:09 设置最大宽高
     */
    public void setMaxLayoutParams(final int maxH, final int maxW) {
        if (null != mPopContentView) {

            ViewTreeObserver viewTreeObserver = mPopContentView.getViewTreeObserver();
            viewTreeObserver.addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
                @Override
                public void onGlobalLayout() {

                    if (0 != maxH) {
                        int popContentHeight = mPopContentView.getHeight();
                        if (popContentHeight > maxH) {
                            LayoutParams layoutParams = mPopContentView.getLayoutParams();
                            layoutParams.height = maxH;
                            mPopContentView.setLayoutParams(layoutParams);
                        }
                    }

                    if (0 != maxW) {
                        int popContentWidth = mPopContentView.getWidth();
                        if (popContentWidth > maxW) {
                            LayoutParams layoutParams = mPopContentView.getLayoutParams();
                            layoutParams.height = maxH;
                            mPopContentView.setLayoutParams(layoutParams);
                        }
                    }

                    mPopContentView.getViewTreeObserver().removeOnGlobalLayoutListener(this);
                }
            });
        }
    }

    @Override
    public void setWindowLayoutType(int layoutType) {
        super.setWindowLayoutType(layoutType);
    }

    /**
     * 根据传递的类型动态设置布局参数
     * @param type 取值有：TYPE_FULL_SCREEN，TYPE_WRAP_CONTENT，TYPE_MATCH_WIDTH
     */
    private void setLayoutParams(int type) {
        switch (type) {
            case TYPE_FULL_SCREEN:
                // 如果想要popupWindow 遮挡住状态栏可以加上这句代码
                //                setClippingEnabled(false);
                setWidth(LayoutParams.MATCH_PARENT);
                setHeight(LayoutParams.MATCH_PARENT);
                break;
            case TYPE_WRAP_CONTENT:
                setWidth(LayoutParams.WRAP_CONTENT);
                setHeight(LayoutParams.WRAP_CONTENT);
                break;
            case TYPE_MATCH_WIDTH:
                setWidth(LayoutParams.MATCH_PARENT);
                setHeight(LayoutParams.WRAP_CONTENT);
            default:
                break;
        }
    }
}
```

### 2、调用示例

```kotlin
AntPopupWindow pop = AntPopupWindow(mActivity, popBinding.root);
pop.showAsDropDown(anhorView);
pop.setMaxLayoutParams(CommUtil.dp2px(350), 0);
```