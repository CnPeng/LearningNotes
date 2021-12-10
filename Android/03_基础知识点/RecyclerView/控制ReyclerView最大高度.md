# 1. 控制ReyclerView最大高度


## 1.1. 问题

期望实现的效果：为 RecyclerView 设置一个最大高度值，当条目内容总高度达不到最大高度时，按照条目内容的总高度进行展示；当条目内容总高度超过最大高度时，按照最大高度展示，更多条目内容需要滚动才可以查看。

直接在 xml 布局文件中为 RecyclerView 设置 `android:maxHeight="100dp"` 并不能生效。

## 1.2. 问题原因

从 RecyclerView 的源码中可以获知，在处理 attr 时，并没有读取和处理 `maxHeight` 属性。

截至 android-30 ，支持 `maxHeight` 属性的只有三个：`TextView`、`ImageView`、`ProgressBar`

## 1.3. 解决方案

### 1.3.1. 方案1

参考链接：[《maxHeight does not work on RecyclerView》](https://stackoverflow.com/questions/49449828/maxheight-does-not-work-on-recyclerview)

>版本信息：Android Studio Arctic Fox | 2020.3.1 Patch 3
>    implementation 'androidx.appcompat:appcompat:1.3.1'
>    implementation 'androidx.constraintlayout:constraintlayout:2.0.4'

将 RecyclerView 的父布局设置为 `ConstraintLayout`, 通过约束条件控制 RecyclerView 的最大高度。

核心属性有如下三项：

```xml
android:layout_height="0dp"
app:layout_constraintHeight_default="wrap"
app:layout_constraintHeight_max="280dp"
```

完整示例：

```xml
     <androidx.recyclerview.widget.RecyclerView
         android:id="@+id/rv_building"
         android:layout_width="0dp"
         android:layout_height="0dp"
         app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
         app:layout_constraintHeight_max="300dp"
         app:layout_constraintHeight_default="wrap"
         app:layout_constraintLeft_toLeftOf="parent"
         app:layout_constraintRight_toRightOf="parent"
         app:layout_constraintTop_toTopOf="parent"
         tools:listitem="@layout/item_traffic_building" />
 </androidx.constraintlayout.widget.ConstraintLayout>
```

**注意**：基于前述的版本信息，实测下面的设置方式也可以生效：

```xml
android:layout_height="wrap_content"
app:layout_constraintHeight_max="280dp"
```

### 1.3.2. 方案2

自定义 RecyclerView 及  `maxHeight` 属性，重写 `onMeasure()` 方法.

示例如下：

```java
public class MaxHeightRecyclerView extends RecyclerView {
    private int mMaxHeight;

    public MaxHeightRecyclerView(Context context) {
        super(context);
    }

    public MaxHeightRecyclerView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init(context, attrs);
    }

    public MaxHeightRecyclerView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init(context, attrs);
    }

    private void init(Context context, AttributeSet attrs) {
        TypedArray a = context.obtainStyledAttributes(attrs, R.styleable.MaxHeightRecyclerView);
        mMaxHeight = arr.getLayoutDimension(R.styleable.MaxHeightRecyclerView_maxHeight, mMaxHeight);
        a.recycle();
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        if (mMaxHeight > 0) {
            heightMeasureSpec = MeasureSpec.makeMeasureSpec(mMaxHeight, MeasureSpec.AT_MOST);
        }
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }
}
```

编辑完上述 java 代码之后，在 `style.xml` 文件中声明 `maxHeight` 属性：

```xml
<declare-styleable name="MaxHeightRecyclerView">
    <attr name="maxHeight" format="dimension" />
</declare-styleable>
```

在 xml 布局文件中使用引用 `maxHeight` 属性：

```xml
app:maxHeight="400dp"
```

**补充：** kotlin 版本代码：

```kotlin
/**
 * 设置 recyclerView 最大高度
 * 摘自：https://www.jianshu.com/p/afab045c34d0
 */
class MaxHeightRecyclerView @JvmOverloads constructor(
        context: Context,
        attributeSet: AttributeSet? = null,
        defStyleAttr: Int = 0
) : RecyclerView(context, attributeSet, defStyleAttr) {

    companion object {
        const val TAG = "MaxHeightRecyclerView"
    }

    var MAX_HEIGHT: Float

    init {
        val typeArray = context.obtainStyledAttributes(attributeSet, R.styleable.MaxHeightRecyclerView)
        MAX_HEIGHT = typeArray.getDimension(R.styleable.MaxHeightRecyclerView_recyclerViewMaxHeight, 200f)
        LogUtils.d(TAG, "MAX_HEIGHT=$MAX_HEIGHT")

        typeArray.recycle()
    }

    override fun onMeasure(widthSpec: Int, heightSpec: Int) {
        val heightSpec = MeasureSpec.makeMeasureSpec((MAX_HEIGHT.toInt()), MeasureSpec.AT_MOST)
        super.onMeasure(widthSpec, heightSpec)
    }
}
```

## 1.4. 参考

* [stackoverflow: 《maxHeight does not work on RecyclerView》](https://stackoverflow.com/questions/49449828/maxheight-does-not-work-on-recyclerview)
* [RecyclerView 设置最大高度](https://www.jianshu.com/p/afab045c34d0)
