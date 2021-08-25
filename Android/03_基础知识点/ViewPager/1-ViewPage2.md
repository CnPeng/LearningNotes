2021-07-02

ViewPager2 是 ViewPager 库的改进版本，底层基于 RecyclerView 实现，可以支持垂直分页、从右到左布局，还支持更多特效。当需要展示的数据发生变化时，可以通过 `notifyDatasetChanged()` 或 `DiffUtil` 实现更新。 

## 1. 基本使用

### 1.1. 添加依赖

```groovy
dependencies {
    implementation "androidx.viewpager2:viewpager2:1.0.0"
}
```

### 1.2. xml中引用

#### 1.2.1. 基本使用-水平分页

```xml
<androidx.viewpager2.widget.ViewPager2
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/pager"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
```

#### 1.2.2. 垂直分页

除了传统的水平分页之外，通过 ViewPager2 的 `android:orientation="vertical" `属性可以将其设置为垂直分页：

```xml
<androidx.viewpager2.widget.ViewPager2
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/pager"
        android:orientation="vertical" />
```

也可以使用 `setOrientation(ViewPager2.ORIENTATION_VERTICAL)` 方法以编程方式设置此属性。

#### 1.2.3. 从右到左分页

ViewPager2 支持从右到左 (RTL) 分页。默认情况下，系统会根据语言区域在适当的情况下自动启用 RTL 分页，我们也可以通过 `android:layoutDirection`  属性手动启用 RTL 分页：

```xml
<androidx.viewpager2.widget.ViewPager2
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/pager"
        android:layoutDirection="rtl" />
```

您还可以使用 `setLayoutDirection(View.LAYOUT_DIRECTION_RTL)` 方法，以编程方式设置此属性。

### 1.3. adapter适配器

#### 1.3.1. adapter 比较

ViewPager中adapter | ViewPager2中的adapter | 适用场景
---|---|---
PagerAdapter | RecyclerView.Adapter |  分页浏览视图
FragmentPagerAdapter | FragmentStateAdapter | 分页浏览固定数量的较少 Fragment 
FragmentStatePagerAdapter | FragmentStateAdapter | 分页浏览大量或未知数量的 Fragment 

#### 1.3.2. FragmentStateAdapter 中的函数

当我们为 ViewPager2 设置 FragmentStateAdapter 的子类作为适配器时，该子类需要提供构造函数，如：

```java
/**
 * CnPeng 2021/6/30
 * 功用： JavaFragment 页面中 ViewPager2 的适配器
 */
public class JavaPagerAdapter extends FragmentStateAdapter {

    // 构造函数——传入 FragmentActivity 实例
    public JavaPagerAdapter(@NonNull @NotNull FragmentActivity fragmentActivity) {
        super(fragmentActivity);
    }

    // 构造函数——传入 Fragment 实例
    public JavaPagerAdapter(@NonNull @NotNull Fragment fragment) {
        super(fragment);
    }

    // 构造函数——传入 FragmentManager 和 Lifecycle 实例
    public JavaPagerAdapter(@NonNull @NotNull FragmentManager fragmentManager, @NonNull @NotNull Lifecycle lifecycle) {
        super(fragmentManager, lifecycle);
    }

    @NonNull
    @NotNull
    @Override
    public Fragment createFragment(int position) {
        return ActListFragment.newInstance(mTitles.get(position).getKey(), PageTypeConst.MAIN_JAVA);
    }

    @Override
    public int getItemCount() {
        return null == mTitles ? 0 : mTitles.size();
    }

    private List<MainTabTitleBean> mTitles;

    public void setTitles(List<MainTabTitleBean> titles) {
        mTitles = titles;
    }
}
```

在上述代码中我们还发现，重写了 `createFragment` 和 `getItemCount` 函数。

* `getItemCount` 提供条目数量
* `createFragment` 提供需要展示的 Fragment 实例。需要注意：**必须确保每次调用该函数时总会提供新的 Fragment 实例，而不会重复使用实例**。


如果将直接继承自 RecyclerView.Adapter 的适配器设置给 ViewPager2 , 则不需要显示提供构造函数。

### 1.4. 注意事项

* [DiffUtil](https://developer.android.google.cn/reference/kotlin/androidx/recyclerview/widget/DiffUtil?hl=zh_cn) 实用程序类依靠 ID 来标识项。如果使用 ViewPager2 分页浏览可变集合，则还必须替换 getItemId() 和 containsItem()。
* 不支持将 getPageWidth() 方法用于 ViewPager2。如果您目前将 getPageWidth() 用于 ViewPager 以便快速查看相邻页面，则必须改用 RecyclerView 的 clipToPadding 属性，如[示例应用](https://github.com/android/views-widgets-samples/blob/87e58d1c6d0c832c5b362d33390148679182d314/ViewPager2/app/src/main/java/androidx/viewpager2/integration/testapp/PreviewPagesActivity.kt)所示。

## 2. 配合 TabLayout 使用

### 2.1. 引入 TabLayout

TabLayout 目前在 material 包中，所以，使用时需要在 gradle 文件中导入依赖：

```groovy
implementation 'com.google.android.material:material:1.3.0'
```

### 2.2. xml 中定义布局

在使用 ViewPager 和 TabLayout 时，它们二者既可以是平级的，也可以将 TabLayout 作为 ViewPager 的子元素。

但是，**ViewPage2 和 TabLayout 配合使用时，二者必须是平级的**，示例如下：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tab_layout"
        android:layout_width="match_parent"
        android:layout_height="@dimen/dp40"
        app:tabIndicatorColor="@color/major"
        app:tabIndicatorFullWidth="false"
        app:tabIndicatorHeight="@dimen/dp1"
        app:tabMode="scrollable"
        app:tabSelectedTextColor="@color/major"
        app:tabTextAppearance="@style/TabLayoutTextStyleV2"
        app:tabTextColor="@color/text3"
        tools:background="#ffc" />

     <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />
</LinearLayout>
```

上述代码中，TabLayout 中 `app:tabTextAppearance` 的取值 `@style/TabLayoutTextStyleV2` 用来设置 Tab 文本的样式，如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <style name="TabLayoutTextStyle" parent="TextAppearance.AppCompat.Button">
        <item name="android:textSize">14sp</item>
    </style>

    <style name="TabLayoutTextStyleV2" parent="TextAppearance.AppCompat.Button">
        <item name="android:textSize">@dimen/sp15</item>
        <item name="android:textAllCaps">false</item>
    </style>

    <style name="TabLayoutTextStyle3" parent="TextAppearance.AppCompat.Button">
        <item name="android:textSize">16sp</item>
        <item name="android:textStyle">bold</item>
    </style>
</resources>
```

### 2.3. 将 ViewPage2 与 TabLayout 关联

* 关联之后滑动 ViewPage2 时才能正常切换 TabLayout 中的 Tab。
* 关联时，需要构建 TabLayoutMediator 对象，传递的三个参数依次为：TabLayout 对象、ViewPage2 对象、TabConfigurationStrategy 监听对象。
    * TabConfigurationStrategy 监听对象的 `onConfigureTab` 回调函数中设置 Tab 的标题
    * 调用 TabLayoutMediator 的 `attach() ` 函数完成关联。
* **必须先为 ViewPage2 设置适配器，然后再添加关联代码**，否则会报错，提示信息为：IllegalStateException: TabLayoutMediator attached before ViewPager2 has an adapter

```java
// viewpager 先设置适配器，然后在与 tabLayout 关联，
// 否则 ：IllegalStateException: TabLayoutMediator attached before ViewPager2 has an adapter
JavaPagerAdapter pagerAdapter = new JavaPagerAdapter(this);
pagerAdapter.setTitles(mViewModel.getTitles());
mBinding.viewPager.setAdapter(pagerAdapter);

// 将 viewPager2 与 tabLayout 关联，并设置标题
new TabLayoutMediator(mBinding.tabLayout, mBinding.viewPager, new TabLayoutMediator.TabConfigurationStrategy() {
    @Override
    public void onConfigureTab(@NonNull @NotNull TabLayout.Tab tab, int position) {
        tab.setText(mViewModel.getTitle(position));
    }
}).attach();
```


## 3. 页面切换动画

### 3.1. PageTransFormer 

#### 3.1.1. PageTransFormer 介绍

要显示非默认的屏幕滑动动画，需要实现 `ViewPager2.PageTransformer` 接口并将其提供给 ViewPager2 对象。该接口只有一个 `transformPage()` 方法。

在屏幕切换的每个点，系统会针对每个可见页面（通常只有一个可见页面）和刚刚离开屏幕的相邻页面调用此方法。 例如，如果第 3 页可见且用户拖向第 4 页，则在手势的每个步骤，会针对第 2 页、第 3 页和第 4 页调用 `transformPage()`。

然后在 `transformPage()` 实现中，可以根据页面在屏幕上的位置确定需要切换哪些页面，从而创建自定义滑动动画。

页面位置取自 `transformPage()` 方法的 `position` 参数。

#### 3.1.2. position 参数

`position` 参数表示**指定页面相对于屏幕中心的位置**。此参数是一个动态属性，会随着用户滚动浏览一系列页面而变化:

* 当页面填满整个屏幕时，其位置值为 0。 
* 当页面刚刚离开屏幕右侧时，其位置值为 1。
* 如果用户在第一页和第二页之间滚动到一半，则第一页的位置为 -0.5，第二页的位置为 0.5。
 
根据页面在屏幕上的位置，我们可以使用 `setAlpha()`、`setTranslationX()` 或 `setScaleY()` 之类的方法设置页面属性，从而创建自定义滑动动画。

#### 3.1.3. 应用自定义 PageTransFormer

通过 setPageTransformer() 函数为 ViewPager2 设置切换动画。

```java
ViewPager2 viewPager = findViewById(R.id.pager);
// ...
viewPager.setPageTransformer(new ZoomOutPageTransformer());
```

### 3.2. MarginPageTransformer



### 3.3. CompositePageTransformer

### 3.4. 自定义 ZoomOutPageTransformer



### 3.5. 自定义 DepthPageTransformer



## 4. 嵌套可滚动元素

当 ViewPager2 内嵌套可滚动视图时，如果滚动方向一致，则滚动不生效。例如，在垂直方向的 ViewPager2 对象内，垂直滚动视图无法滚动。

要想实现这种同方向的嵌套滚动，必须对 ViewPager2 对象调用 `requestDisallowInterceptTouchEvent()`。

[ViewPager2 嵌套滚动示例](https://github.com/android/views-widgets-samples/blob/master/ViewPager2/app/src/main/res/layout/item_nested_recyclerviews.xml#L43)展示了一种使用通用[自定义封装容器布局](https://github.com/android/views-widgets-samples/blob/master/ViewPager2/app/src/main/java/androidx/viewpager2/integration/testapp/NestedScrollableHost.kt)解决此问题的办法。

## 5. 其他补充

### 5.1. 获取和设置 CurrentItem

如果是在 Activity 中使用 ViewPager2 ，我们还可以监听返回键的事件，然后判断当前是不是展示的 ViewPager2 的第一个条目，如果是则执行默认的返回事件，如果不是，则展示前一个条目页面。摘自 [官方文档](https://developer.android.google.cn/training/animation/screen-slide-2?hl=zh_cn#viewpager)

```java
 @Override
public void onBackPressed() {
   if (viewPager.getCurrentItem() == 0) {
       // If the user is currently looking at the first step, allow the system to handle the
       // Back button. This calls finish() on this activity and pops the back stack.
       super.onBackPressed();
   } else {
       // Otherwise, select the previous step.
       viewPager.setCurrentItem(viewPager.getCurrentItem() - 1);
   }
}
```

### 5.2. 禁用手势滑动

```java
// 默认为 true,表示可以响应用户的滑动手势，即滑动切换 TAB;设置为 false 之后将无法响应滑动手机。
mBinding.viewPager.setUserInputEnabled(false);
```

### 5.3. 预加载

通过 `setOffscreenPageLimit` 可以设置预加载的量，默认为 `ViewPager2.OFFSCREEN_PAGE_LIMIT_DEFAULT`, 其值为 -1。在设置该值时，要么使用默认值，要么必须大于 0.

### 5.4. 滑动监听

通过 `registerOnPageChangeCallback` 可以设置滑动监听

## 6. 参考资料

* [【Android】ViewPager2简单了解](https://juejin.im/post/5e8147a3518825737067a2f7?utm_source=gold_browser_extension#heading-14)
* [从 ViewPager 迁移到 ViewPager2](https://developer.android.google.cn/training/animation/vp2-migration?hl=zh_cn)


