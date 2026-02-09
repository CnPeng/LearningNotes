# 1. 01-Android CoordinatorLayout使用

[点击查看原文](https://juejin.cn/post/7100890423601659912#heading-7)(本文基于原文做了结构调整，方便观看)

CoordinatorLayout是一个超级强大的 FrameLayout，结合 AppBarLayout、 CollapsingToolbarLayout 等可产生各种炫酷的效果。

## 1.1. 引入依赖

```xml
 implementation 'com.google.android.material:material:1.10.0'
```

## 1.2. 配合AppBarLayout使用

### 1.2.1. 使用示例

![](pics/20251222162130191_545849191.image)


```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="100dp"
            android:background="#222222"
            android:gravity="center"
            android:text="该区域可折叠"
            android:textColor="@android:color/white"
            android:textSize="30sp"
            app:layout_scrollFlags="scroll" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="50dp"
            android:background="#DD012D"
            android:gravity="center"
            android:text="该区域为上滑至头部固定区域"
            android:textColor="@android:color/white"
            android:textSize="20sp" />
    </com.google.android.material.appbar.AppBarLayout>
    <androidx.core.widget.NestedScrollView
        android:id="@+id/rv_demo1_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
        <TextView
            android:layout_width="match_parent"
            android:text="这是一个滚动布局"
            android:textSize="200sp"
            android:background="#00ff00"
            android:layout_height="wrap_content"/>
    </androidx.core.widget.NestedScrollView>
</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

`CoordinatorLayout` 须要作为顶层父 View ， 子 View 想要与 `CoordinatorLayout` 实现"联动性"效果的首要条件是：这个子 View 必须实现了 `NestedScrollingChild` 接口。(例如：NestedScrollView 、RecyclerView 等控件)。

`CoordinatorLayout` 子控件如果需要联动，需要设置 `app:layout_behavior` 属性，上面 AppBarLayout 没有设置是因为本身有个默认的 `app:layout_behavior` 查看源码如下：

```java
@CoordinatorLayout.DefaultBehavior(AppBarLayout.Behavior.class)
public class AppBarLayout extends LinearLayout {
```

### 1.2.2. AppBarLayout中ScrollFlags值

XML 使用 `app:layout_scrollFlags` 设置，java 代码中先获取该控件的 `AppBarLayout.LayoutParams` 再使用 `setScrollFlags(int)` 设置

* XML设置方法：

```xml
app:layout_scrollFlags="scroll|enterAlways" 
```

* 代码中设置方法：

```java
TextView text= ... //确保该View是被AppBarLayout包裹的
AppBarLayout.LayoutParams params = (AppBarLayout.LayoutParams) text.getLayoutParams();
params.setScrollFlags(AppBarLayout.LayoutParams.SCROLL_FLAG_SCROLL | AppBarLayout.LayoutParams.SCROLL_FLAG_ENTER_ALWAYS);
```

ScrollFlags 的取值如下：

#### 1.2.2.1. scroll (SCROLL_FLAG_SCROLL)

视图将与滚动事件直接相关。需要设置此标志才能使任何其他标志生效。如果此视图之前的任何同级视图没有此标志，则此值无效。

```xml
app:layout_scrollFlags="scroll"
```

![](pics/20251222160223168_860474216.image)


#### 1.2.2.2. enterAlways (SCROLL_FLAG_ENTER_ALWAYS)

当进入（在屏幕上滚动）时，视图将在任何向下滚动事件上滚动，无论滚动视图是否也在滚动。这通常被称为“快速推出”模式。

```xml
app:layout_scrollFlags="scroll|enterAlways"
```

![](pics/20251222162605445_177699852.image)



#### 1.2.2.3. enterAlwaysCollapsed (SCROLL_FLAG_ENTER_ALWAYS_COLLAPSED)

“enterAlways”的另一个标志，它将返回的视图修改为仅在最初滚动回其折叠高度。一旦滚动视图到达其滚动范围的末尾，此视图的其余部分将滚动到视图中。折叠高度由视图的最小高度定义。

```xml
android:minHeight="30dip"
app:layout_scrollFlags="scroll|enterAlways|enterAlwaysCollapsed" 
```

![](pics/20251222162619886_727376301.image)


#### 1.2.2.4. exitUntilCollapsed (SCROLL_FLAG_EXIT_UNTIL_COLLAPSED)

退出时（从屏幕上滚动），视图将滚动到“折叠”为止。折叠高度由视图的最小高度定义。

```xml
android:minHeight="30dip"
app:layout_scrollFlags="scroll|exitUntilCollapsed" />
```

![](pics/20251222162632400_1984398458.image)


#### 1.2.2.5. snap (SCROLL_FLAG_SNAP)

在滚动结束时，如果视图仅部分可见，则它将被捕捉并滚动到最近的边。例如，如果视图只显示其底部的25%，则它将完全从屏幕上滚下。相反，如果它的底部75%是可见的，那么它将完全滚动到视图中。

```xml
app:layout_scrollFlags="scroll|snap"

```

![](pics/20251222162643016_1875242991.image)

### 1.2.3. 配合 CollapsingToolbarLayout 使用

#### 1.2.3.1. 实现效果

![](pics/20251222162721056_1762010462.image)

#### 1.2.3.2. 布局文件：

```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/main_content"
    android:layout_width="match_parent"
    android:fitsSystemWindows="true"
    android:layout_height="match_parent"
    >
    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/appbar"
        android:layout_width="match_parent"
        android:fitsSystemWindows="true"
        android:layout_height="wrap_content"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:id="@+id/toolbar_layout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:contentScrim="#ff0000"
            app:collapsedTitleGravity="center"
            app:expandedTitleGravity="left|bottom"
            app:layout_scrollFlags="scroll|exitUntilCollapsed"
            app:title="杨幂"
            app:toolbarId="@+id/toolbar">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:scaleType="centerCrop"
                app:layout_collapseMode="parallax"
                app:srcCompat="@mipmap/ym" />
            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="50dp"
                app:layout_collapseMode="pin"
                />
        </com.google.android.material.appbar.CollapsingToolbarLayout>
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.core.widget.NestedScrollView
        android:id="@+id/rv_demo1_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
            <TextView
                android:layout_width="match_parent"
                android:text="这是一个滚动布局"
                android:textSize="200sp"
                android:background="#00ff00"
                android:layout_height="wrap_content"/>
    </androidx.core.widget.NestedScrollView>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

#### 1.2.3.3. CollapsingToolbarLayout 部分属性

属性名称|属性含义
---|---
app:contentScrim CollapsingToolbarLayout |完全折叠后的背景颜色
app:titleEnabled  | 是否显示标题 app:title 标题 app:toolbarId toolbar 对应的view id
app:statusBarScrim  | 折叠后状态栏的背景
app:scrimVisibleHeightTrigger  |  设置收起多少高度时，显示ContentScrim的内容
app:scrimAnimationDuration   | 展开状态和折叠状态之间，内容转换的动画时间
app:expandedTitleTextAppearance   | 布局张开的时候title的样式
app:expandedTitleMarginTop   | 布局张开的时候title的margin top
app:expandedTitleMarginStart   | 布局张开的时候title的margin start
app:expandedTitleMarginEnd   | 布局张开的时候title的margin end
app:expandedTitleMarginBottom   | 布局张开的时候title的margin bottom
app:expandedTitleMargin   | 布局张开的时候title的margin
app:expandedTitleGravity   | 布局张开的时候title的位置
app:collapsedTitleTextAppearance   | 布局折叠的时候title的样式
app:collapsedTitleGravity  |  布局折叠的时候title的gravity

### 1.2.4. CoordinatorLayout 中的 Behavior

Behavior行为控制器：实现用户在子视图上进行的一个或多个交互。这些交互可能包括拖动，滑动，甩动或任何其他手势。

#### 1.2.4.1. Behavior中常用的重写的方法：

```java
/**
  * 确定使用Behavior的View要依赖的View的类型
  * 只要是CoordinatorLayout内的View的状态发送了变化,该方法就会执行
  * @param parent     顶层父控件CoordinatorLayout
  * @param child      我们设置这个Behavior的View
  * @param dependency 值会不断的变化,他会轮询CoordinatorLayout下所有所属的子View
  * @return 这里判断dependency所属的View是哪一个, 返回true，onDependentViewChanged才执行,否则不执行
  */
@Override
public boolean layoutDependsOn(CoordinatorLayout parent, View child, View dependency)

/**
  * 当被依赖的View状态改变时回调
  * @param parent     顶层父控件CoordinatorLayout
  * @param child      我们设置这个Behavior的View
  * @param dependency 值会不断的变化,他会轮询CoordinatorLayout下所有所属的子View
  * @return 当我们改变了child的大小或者位置的时候我们需要返回true
  */
@Override
public boolean onDependentViewChanged(CoordinatorLayout parent, View child, View dependency)

/**
  * 当被依赖的View移除时回调
  * @param parent  顶层父控件CoordinatorLayout
  * @param child 我们设置这个Behavior的View
  * @param dependency 值会不断的变化,他会轮询CoordinatorLayout下所有所属的子View
  */
@Override
public void onDependentViewRemoved(@NonNull CoordinatorLayout parent, @NonNull View child, @NonNull View dependency) 

```

#### 1.2.4.2. 简单案例

效果描述：向上滑动时底部控件渐渐隐藏，向下滑动时底部控件渐渐显示

* 效果动图:

![](pics/20251222163136568_2041611393.image)


* 自定义一个Behavior

```java
public class Demo1Behavior extends CoordinatorLayout.Behavior<View> {
    public Demo1Behavior() {
        super();
    }
    public Demo1Behavior(Context context, AttributeSet attrs) {
        super(context, attrs);
    }
    @Override
    public boolean layoutDependsOn(CoordinatorLayout parent, View child, View dependency) {
        //这里判断dependency所属的View是哪一个,返回true，onDependentViewChanged才执行,否则不执行
        return dependency instanceof AppBarLayout;
    }
    @Override
    public boolean onDependentViewChanged(CoordinatorLayout parent, View child, View dependency) {
        /*
         *这里获取dependency的top值,也就是AppBarLayout的top,因为AppBarLayout
         *在是向上滚出界面的,我们的因为是和AppBarLayout相反,所以取绝对值.
         */
        float translationY = Math.abs(dependency.getTop());
        child.setTranslationY(translationY);
        return true;
    }
}
```

* 布局文件:

```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
	 <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="100dp"
            android:background="#222222"
            android:gravity="center"
            android:text="该区域可折叠"
            android:textColor="@android:color/white"
            android:textSize="30sp"
            app:layout_scrollFlags="scroll" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="50dp"
            android:background="#DD012D"
            android:gravity="center"
            android:text="该区域为上滑至头部固定区域"
            android:textColor="@android:color/white"
            android:textSize="20sp" />
    </com.google.android.material.appbar.AppBarLayout>
    <androidx.core.widget.NestedScrollView
        android:id="@+id/rv_demo1_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
        <TextView
            android:layout_width="match_parent"
            android:text="这是一个滚动布局"
            android:textSize="200sp"
            android:background="#00ff00"
            android:layout_height="wrap_content"/>
    </androidx.core.widget.NestedScrollView>
	//行为控制器引用 app:layout_behavior=".demo1.Demo1Behavior"
    <TextView
        android:layout_gravity="bottom"
        app:layout_behavior=".demo1.Demo1Behavior"
        android:layout_width="match_parent"
        android:background="#ff00ff"
        android:layout_height="50dip"/>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

## 1.3. 复杂案例

### 1.3.1. 实现效果

* 效果描述：上滑顶部区域隐藏，导航条悬浮，点击导航条可快速定位。

* 效果动图：

![](pics/20251222163507560_950921664.image)

### 1.3.2. 布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".demo5.Demo5Activity">

    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/abl_demo5_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="100dp"
            android:background="#222222"
            android:gravity="center"
            android:text="该区域可折叠"
            android:textColor="@android:color/white"
            android:textSize="30sp"
            app:layout_scrollFlags="scroll|enterAlwaysCollapsed" />

        <com.google.android.material.tabs.TabLayout
            android:id="@+id/tb_demo5_content"
            android:layout_width="match_parent"
            android:layout_height="50dip"
            android:background="#ffffff">
        </com.google.android.material.tabs.TabLayout>
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rv_demo5_content"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### 1.3.3. 页面及适配器代码

#### 1.3.3.1. 页面代码

```java
public class Demo5Activity extends AppCompatActivity {
    private RecyclerView rv_demo5_content;
    private List<Demo5Bean> data;
    private TabLayout tb_demo5_content;
    private AppBarLayout abl_demo5_content;
    private GridLayoutManager gridLayoutManager;
    private RecyclerView.SmoothScroller smoothScroller;
    private List<Integer> titlePosition;
    //是否正在滚动
    private boolean isScroll;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_demo5);
        tb_demo5_content = findViewById(R.id.tb_demo5_content);
        rv_demo5_content = findViewById(R.id.rv_demo5_content);
        abl_demo5_content = findViewById(R.id.abl_demo5_content);
        initData();
        initTabLayout();
        initRecyclerView();
    }

    private void initData() {
        data = new ArrayList<Demo5Bean>();
        titlePosition = new ArrayList<Integer>();
        Demo5Bean bean = null;
        for (int i = 0; i < 5; i++) {
            bean = new Demo5Bean("标题" + i, Demo5Adapter.VIEW_TYPE_TITLE);
            data.add(bean);
            titlePosition.add(i + (i * 10));
            for (int i1 = 0; i1 < 10; i1++) {
                bean = new Demo5Bean(i + "_内容" + i1, Demo5Adapter.VIEW_TYPE_MENU);
                data.add(bean);
            }
        }
    }

    private void initTabLayout() {
        for (Demo5Bean datum : data) {
            if (datum.getItemType() == Demo5Adapter.VIEW_TYPE_TITLE) {
                  tb_demo5_content.addTab(tb_demo5_content.newTab().setText(datum.getName()));
            }
        }
        tb_demo5_content.addOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
            @Override
            public void onTabSelected(TabLayout.Tab tab) {
                if(!isScroll) {
                    //收缩折叠区
                    abl_demo5_content.setExpanded(false);
                    int tabPosition = tab.getPosition();
                    int titlePosition = getTitlePosition(tabPosition);
                    smoothScroller.setTargetPosition(titlePosition);
                    gridLayoutManager.startSmoothScroll(smoothScroller);
                }
            }
            @Override
            public void onTabUnselected(TabLayout.Tab tab) {
            }
            @Override
            public void onTabReselected(TabLayout.Tab tab) {
            }
        });
    }

    private void initRecyclerView() {
        gridLayoutManager = new GridLayoutManager(this, 4);
        gridLayoutManager.setSpanSizeLookup(new GridLayoutManager.SpanSizeLookup() {
            @Override
            public int getSpanSize(int position) {
                if (position < data.size()) {
                    Demo5Bean bean = data.get(position);
                    if (bean.getItemType() == Demo5Adapter.VIEW_TYPE_TITLE) {
                        //是标题则占4个item
                        return 4;
                    } else if (bean.getItemType() == Demo5Adapter.VIEW_TYPE_MENU) {
                        //是menu则正常占用一个item
                        return 1;
                    } else {
                        return 0;
                    }
                } else {
                    //FooterView 占4个item
                    return 4;
                }
            }
        });
        //计算最后填充FooterView填充高度，全屏高度-状态栏高度-tablayout的高度(这里固定高度50dp)-title标题高度（40）-最后分组高度（3排menu每个70），用于recyclerView的最后一个item FooterView填充高度
        int screenH = getScreenHeight();
        int statusBarH = getStatusBarHeight(this);
        int tabH = dip2px(this,50);
        int titleH = dip2px(this,40);
        int lastMenusH= dip2px(this,70)*3;
        int lastH = screenH - statusBarH - tabH -titleH-lastMenusH;
        if(lastH<=0){
            lastH=0;
        }
        Demo5Adapter mAdapter = new Demo5Adapter(data, this,lastH);
        rv_demo5_content.setLayoutManager(gridLayoutManager);
        rv_demo5_content.setAdapter(mAdapter);
        //RecyclerView平滑Scroller
        smoothScroller = new LinearSmoothScroller(this) {
                    @Override
                    protected int getVerticalSnapPreference() {
                        return LinearSmoothScroller.SNAP_TO_START;
                    }
                    @Nullable
                    @Override
                    public PointF computeScrollVectorForPosition(int targetPosition) {
                        return gridLayoutManager.computeScrollVectorForPosition(targetPosition);
                    }
                };
        rv_demo5_content.addOnScrollListener(new RecyclerView.OnScrollListener() {
            @Override
            public void onScrollStateChanged(@NonNull RecyclerView recyclerView, int newState) {
                super.onScrollStateChanged(recyclerView, newState);
                if(newState==RecyclerView.SCROLL_STATE_IDLE){
                    isScroll=false;
                }else{
                    isScroll=true;
                }
            }
            @Override
            public void onScrolled(@NonNull RecyclerView recyclerView, int dx, int dy) {
                super.onScrolled(recyclerView, dx, dy);
                TabLayout.Tab tabAt = tb_demo5_content.getTabAt(getTabPosition(gridLayoutManager.findFirstVisibleItemPosition()));
                if (tabAt != null && !tabAt.isSelected()) {
                    tabAt.select();
                }
            }
        });
    }

    private int getTitlePosition(int tabPosition) {
        //根据tabPosition找出TitlePosition
        return titlePosition.get(tabPosition);
    }

    private int getTabPosition(int menuPosition) {
       return titlePosition.indexOf(menuPosition);
    }

    private int getScreenHeight() {
        return getResources().getDisplayMetrics().heightPixels;
    }

    public int getStatusBarHeight(Context context) {
        int result = 0;
        int resourceId = context.getResources()
                .getIdentifier("status_bar_height", "dimen", "android");
        if (resourceId > 0) {
            result = context.getResources().getDimensionPixelSize(resourceId);
        }
        return result;
    }

    public static int dip2px(Context context, float dpValue) {
        final float scale = context.getResources().getDisplayMetrics().density;
        return (int) (dpValue * scale + 0.5f);
    }
}
```


#### 1.3.3.2. 适配器

```java
public class Demo5Adapter extends RecyclerView.Adapter {
    public static final int VIEW_TYPE_TITLE = 0;
    public static final int VIEW_TYPE_MENU = 1;
    public static final int VIEW_TYPE_FOOTER = 2;
    private final LayoutInflater inflater;
    private List<Demo5Bean> data;
    private Context context;
    private int lastH;
    public Demo5Adapter(List<Demo5Bean> data, Context context,int lastH) {
        this.data = data;
        this.context = context;
        this.lastH = lastH;
        inflater = LayoutInflater.from(context);
    }
    @Override
    public int getItemViewType(int position) {
        if (position == data.size()) {
            return VIEW_TYPE_FOOTER;
        } else {
            Demo5Bean demo5Bean = data.get(position);
            return demo5Bean.getItemType();
        }
    }
    @NonNull
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        RecyclerView.ViewHolder viewHolder = null;
        switch (viewType) {
            case VIEW_TYPE_TITLE:
                viewHolder = new Demo5TitleViewHolder(inflater.inflate(R.layout.itme_demo5_title, parent, false));
                break;
            case VIEW_TYPE_MENU:
                viewHolder = new Demo5MenuViewHolder(inflater.inflate(R.layout.itme_demo5_menu, parent, false));
                break;
            case VIEW_TYPE_FOOTER:
                View view = new View(parent.getContext());
                view.setLayoutParams(new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, lastH));
                viewHolder = new Demo5FooterViewHolder(view);
                break;
        }
        return viewHolder;
    }
    @Override
    public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int position) {
        switch (getItemViewType(position)) {
            case VIEW_TYPE_TITLE:
                ((Demo5TitleViewHolder) holder).tv_item_demo5_title.setText(data.get(position).getName());
                Log.i("TTT",holder.itemView.getMeasuredHeight()+"VIEW_TYPE_TITLE");

                break;
            case VIEW_TYPE_MENU:
                ((Demo5MenuViewHolder) holder).tv_item_demo5_menu.setText(data.get(position).getName());
                Log.i("TTT",holder.itemView.getMeasuredHeight()+"VIEW_TYPE_MENU");
                break;
        }
    }
    @Override
    public int getItemCount() {
        return data.size() + 1;
    }
    public static class Demo5TitleViewHolder extends RecyclerView.ViewHolder {
        private final TextView tv_item_demo5_title;
        public Demo5TitleViewHolder(@NonNull View itemView) {
            super(itemView);
            tv_item_demo5_title = itemView.findViewById(R.id.tv_item_demo5_title);
        }
    }
    public static class Demo5MenuViewHolder extends RecyclerView.ViewHolder {
        private final TextView tv_item_demo5_menu;
        public Demo5MenuViewHolder(@NonNull View itemView) {
            super(itemView);
            tv_item_demo5_menu = itemView.findViewById(R.id.tv_item_demo5_menu);
        }
    }
    public static class Demo5FooterViewHolder extends RecyclerView.ViewHolder {
        public Demo5FooterViewHolder(@NonNull View itemView) {
            super(itemView);
        }
    }
}
```

#### 1.3.3.3. 数据bean

```java
public class Demo5Bean {
    private String name;
    private int itemType;
    public Demo5Bean(String name, int itemType) {
        this.name = name;
        this.itemType = itemType;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getItemType() {
        return itemType;
    }
    public void setItemType(int itemType) {
        this.itemType = itemType;
    }
}
```

### 1.3.4. 补充说明

AppBarLayout代码主动折叠与打开:

```java
//打开AppBarLayout
appBarLayout.setExpanded(true);
//关闭AppBarLayout
appBarLayout.setExpanded(false);

```

## 1.4. 其他参考


[Android CoordinatorLayout使用](https://juejin.cn/post/7100890423601659912)

[CoordinatorLayout 完全解析](https://blog.csdn.net/zhangmiaoping23/article/details/121980259)

[Android CoordinatorLayout使用示例记录](https://www.cnblogs.com/stars-one/p/18543723)

[揭秘 Android CoordinatorLayout：从源码深度解析其协同工作原理](https://juejin.cn/post/7497317090660286514)