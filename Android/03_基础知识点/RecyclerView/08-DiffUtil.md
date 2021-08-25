# 1. 08-DiffUtil

在刷新 RecyclerView 列表时，最常用的可能是用 notifyDataSetChanged() 直接一股脑儿的刷新整个列表，但这样渲染效率比较低，而且界面交互生硬。而借助 DiffUitl 则可以提高渲染效率，并且会有较流畅的交互效果（会提供系统默认的交互动效）。

## 1.1. DiffUtil 介绍

[androidx.recyclerview.widget.DiffUtil](https://developer.android.google.cn/reference/kotlin/androidx/recyclerview/widget/DiffUtil?hl=zh_cn) 是一个工具类，用来计算两个 List （假设一个称为旧List，一个称为新List）中数据差异，并输出将旧 List 转换为新 List  时所需要的操作。

DiffUtil 使用 Eugene W. Myers 差分算法来计算将一个 List 转换为另一个 List 的最少更新次数。**Myers 算法不处理移动的项目**，因此 DiffUtil 会对结果进行二次检测，从而判断哪些条目被移动了。

使用 DiffUtil 时，如果启用条目移动检测，则需要额外的时间。如果我们的 List 已经按相同的约束做过排序（例如为帖子列表创建时间戳），则可以禁用移动检测以提高性能。（ DiffUtil 的 calculateDiff 有两个，一个是单参数的，一个是双参数的。前者默认启用移动检测，后者通过第二个参数决定是否启用移动检测。 ）

## 1.2. DiffUtil 相关类和方法

### 1.2.1. 成员方法-calculateDiff()

calculateDiff() 有两个重载形式，如下：

```java
@NonNull
public static DiffResult calculateDiff(@NonNull Callback cb) {
   return calculateDiff(cb, true);
}

@NonNull
public static DiffResult calculateDiff(@NonNull Callback cb, boolean detectMoves){
    //......
}
```

这两个方法都返回 `@NonNull`——非空的 DiffResult，获取到该结果之后，我们就可以调用其内部的 `dispatchUpdatesTo()` 方法实现 RecyclerView 列表的更新。

在上述方法定义中，

* 都接收 Callback 参数，在计算两个 List 的差异时，会触发其成员函数。
* 两个参数的方法中，第二个参数 detectMoves 表示是否对 List 中条目的移动进行检测
* 一个参数的方法中，默认 detectMoves 为 true.

**注意：当被比较的两个 List 比较大时，最好在子线程中进行比较，然后再从主线程触发 RecyclerView 的更新**

### 1.2.2. DiffUtil.Callback

[androidx.recyclerview.widget.DiffUtil.Callback](https://developer.android.google.cn/reference/kotlin/androidx/recyclerview/widget/DiffUtil.Callback?hl=zh_cn) 是计算两个 List 差异时所需的回调参数。DiffUtil 会根据需要调用其中的成员函数，其成员函数如下：

#### 1.2.2.1. getNewListSize()

返回新 List 的成员数量

#### 1.2.2.2. getOldListSize()

返回旧 List 的成员数量

#### 1.2.2.3. areItemsTheSame(,)

`areItemsTheSame(oldItemPosition: Int, newItemPosition: Int)`

判断新旧 List 中指定位置的 item 是否一致。

通常我们填充到列表的 item 数据都会有 id 或者其他唯一标识，可以通过该 id 或其他唯一标识判断 item 是否一致。id 相同则可以认为一致，返回 true；不同则不一致，返回 false。

#### 1.2.2.4. areContentsTheSame(,)

`areContentsTheSame(oldItemPosition: Int, newItemPosition: Int) `

判断 item 中的内容是否一致。也就是说当 areItemsTheSame 返回 true 时，进一步判断两个 item 的其他内容向是否一致。

#### 1.2.2.5. getChangePayload(,)

`getChangePayload(oldItemPosition: Int, newItemPosition: Int) `

当 areItemsTheSame 为 true，areContentsTheSame 为 false 时，通过该函数将新 List 中的差异内容暴露出去，返回值类型为 Object 。

通常我们会以 Bundle 用 key-value 的形式暴露数据。暴露的数据最终会被 RecyclerView.Adapter 的 `onBindViewHolder(@NonNull DiffUtilHolder holder, int position, @NotNull List<Object> payloads)` 中的 payloads 接收。

**注意：** 在实现 DiffUtil.Callback 时，getChangePayload() 不是必须的，但如果提供了该函数的实现，配合 `onBindViewHolder(,, @NotNull List<Object> payloads)` 可以让 RecyclerView 的刷新效率更高。——实现该函数后，会触发 item 视图内的局部更新。

### 1.2.3. DiffUtil.DiffResult

[androidx.recyclerview.widget.DiffUtil.DiffResult](https://developer.android.google.cn/reference/kotlin/androidx/recyclerview/widget/DiffUtil.DiffResult?hl=zh_cn) 表示 DiffUtil.calculateDiff() 的结果。获取到该结果之后，我们就可以直接通过其内部的 `dispatchUpdatesTo()` 去更新视图或者进行其他操作。其成员函数如下：

#### 1.2.3.1. dispatchUpdatesTo()

* `dispatchUpdatesTo(@NonNull updateCallback: ListUpdateCallback)` 触发 ListUpdateCallback 中的更新事件。

* `dispatchUpdatesTo(@NonNull adapter: RecyclerView.Adapter<RecyclerView.ViewHolder!>)` 触发 RecyclerView.Adapter 的更新事件（**注意：在调用该函数的同时，还需要通过传统手段将新的 List 传递给 Adapter**）

上面两个方法中，后者本质上也是对前者的调用，其内部实现为：

```java
public void dispatchUpdatesTo(@NonNull final RecyclerView.Adapter adapter) {
    dispatchUpdatesTo(new AdapterListUpdateCallback(adapter));
}
```

而 AdapterListUpdateCallback 的完整实现如下：

```java
/**
 * ListUpdateCallback that dispatches update events to the given adapter.
 *
 * @see DiffUtil.DiffResult#dispatchUpdatesTo(RecyclerView.Adapter)
 */
public final class AdapterListUpdateCallback implements ListUpdateCallback {
    @NonNull
    private final RecyclerView.Adapter mAdapter;

    /**
     * Creates an AdapterListUpdateCallback that will dispatch update events to the given adapter.
     *
     * @param adapter The Adapter to send updates to.
     */
    public AdapterListUpdateCallback(@NonNull RecyclerView.Adapter adapter) {
        mAdapter = adapter;
    }

    /** {@inheritDoc} */
    @Override
    public void onInserted(int position, int count) {
        mAdapter.notifyItemRangeInserted(position, count);
    }

    /** {@inheritDoc} */
    @Override
    public void onRemoved(int position, int count) {
        mAdapter.notifyItemRangeRemoved(position, count);
    }

    /** {@inheritDoc} */
    @Override
    public void onMoved(int fromPosition, int toPosition) {
        mAdapter.notifyItemMoved(fromPosition, toPosition);
    }

    /** {@inheritDoc} */
    @Override
    public void onChanged(int position, int count, Object payload) {
        mAdapter.notifyItemRangeChanged(position, count, payload);
    }
}
```

根据上述代码可知，AdapterListUpdateCallback 是 ListUpdateCallback 的实现类，在需要触发界面更新操作时，最终还是调用了 Adapter 的相关方法。

#### 1.2.3.2. convertNewPositionToOld()

`convertNewPositionToOld(@IntRange(0) newListPosition: Int)`

根据给定的新 List 中的 position , 在旧 List 中查找对应的 position。如果找不到就返回 NO_POSITION，其值为 -1.

#### 1.2.3.3. convertOldPositionToNew()

`convertOldPositionToNew(@IntRange(0) oldListPosition: Int)`

根据给定旧 List 中 position，在新 List 中查找对应的 position。如果找不到就返回 NO_POSITION，其值为 -1.

### 1.2.4. ItemCallback

[androidx.recyclerview.widget.DiffUtil.ItemCallback](https://developer.android.google.cn/reference/kotlin/androidx/recyclerview/widget/DiffUtil.ItemCallback?hl=zh_cn) 是计算 List 中两个非空 item 的差异时使用的回调。

DiffUtil.Callback 提供两条判断规则：List 的大小（索引）、Item 的差异。而 DiffUtil.ItemCallback 则只关心 Item 的差异。

ItemCallback 的成员函数如下：

#### 1.2.4.1. areItemsTheSame

`areItemsTheSame(@NonNull oldItem: T, @NonNull newItem: T)`

检查两个 Item 是否一致，在检查时通常会使用 Item 的 id 或者其他唯一标识进行判断（参考 Callback 中的该函数）

#### 1.2.4.2. areContentsTheSame

`areContentsTheSame(@NonNull oldItem: T, @NonNull newItem: T)`

当 areItemsTheSame 返回 true 时，调用该方法检查两个 Item 中的内容是否一致，

#### 1.2.4.3. getChangePayload

`getChangePayload(@NonNull oldItem: T, @NonNull newItem: T)`

当 areItemsTheSame(T, T) 返回 true 并且 areContentsTheSame(T, T) 返回 false 时，通过该函数返回差异项内容。（参考 Callback 中的同名函数）

## 1.3. 实现 RecyclerView 列表刷新

借助 DiffUtil 实现 RecyclerView 列表刷新时有两种方式：

* DiffUtil.ItemCallback + ListAdapter
* DiffUtil.Callback + RecyclerView.Adapter

### 1.3.1. DiffUtil.ItemCallback + ListAdapter

这种方式整体比较简单，将数据更新到 Adapter 时，直接调用其 submitList() 即可，其内部会自动比较并执行刷新操作。

#### 1.3.1.1. ListAdapter

在介绍如何利用 DiffUtil.ItemCallback + ListAdapter 实现 RecyclerView 刷新之前，先来介绍一下 ListAdapter

ListAdapter 是官方包装的 RecyclerView.Adapter 的一个抽象子类，它拥有一个 `AsyncListDiffer<T>` 常量——mDiffer，该常量会持有通过 `submitList()` 方法传入的List 数据。当我们需要获取 List 数据时，也会由  mDiffer 提供。核心实现如下：

```java
    public void submitList(@Nullable List<T> list) {
        mDiffer.submitList(list);
    }

    public void submitList(@Nullable List<T> list, @Nullable final Runnable commitCallback) {
        mDiffer.submitList(list, commitCallback);
    }

    protected T getItem(int position) {
        return mDiffer.getCurrentList().get(position);
    }

    @Override
    public int getItemCount() {
        return mDiffer.getCurrentList().size();
    }

    @NonNull
    public List<T> getCurrentList() {
        return mDiffer.getCurrentList();
    }
```

#### 1.3.1.2. DiffUtilViewModel 定义数据源

先定义 DiffUtilViewModel , 在其中定义数据源，供列表展示使用

```java
/**
 * CnPeng 2021/7/5
 * 功用：DiffUtil 测试页面使用的条目 bean
 * 其他：
 */
public class DiffUtilItemBean {
    private String title;
    private String content;

    public DiffUtilItemBean(String title, String content) {
        this.title = title;
        this.content = content;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }
}
```

```java
/**
 * CnPeng 2021/7/6
 * 功用：DiffUtil 测试页面中使用的 ViewModel
 * 其他：
 */
public class DiffUtilViewModel extends ViewModel {

    private List<DiffUtilItemBean> mList1;
    private List<DiffUtilItemBean> mList2;
    private List<DiffUtilItemBean> mList3;

    public void initDataSet() {
        mList1 = new ArrayList<>();
        for (int i = 0; i < 20; i++) {
            mList1.add(new DiffUtilItemBean("list1==" + i, "这是list1的第 " + i + "个元素"));
        }

        mList2 = new ArrayList<>();
        for (int i = 0; i < 20; i++) {
            mList2.add(new DiffUtilItemBean("list2==" + i, "这是list2的第 " + i + "个元素"));
        }

        mList3 = new ArrayList<>();
        mList3.addAll(mList1);
        for (int i = 0; i < 20; i++) {
            if (i % 3 == 0) {
                DiffUtilItemBean itemBean = new DiffUtilItemBean("list3==" + i, "这是 list3 的第" + i + "个元素，\n还手动加了个换行符");
                mList3.set(i, itemBean);
            } else if (i % 2 == 0) {
                DiffUtilItemBean itemBean = mList2.get(i);
                mList3.set(i, itemBean);
            }
        }
    }

    /**
     * CnPeng:2021/7/6 11:35 选择了第几个 radio
     */
    public List<DiffUtilItemBean> getListToShow(int radioNum) {
        if (radioNum % 2 == 0) {
            return mList2;
        } else if (radioNum % 3 == 0) {
            return mList3;
        } else {
            return mList1;
        }
    }
}
```

#### 1.3.1.3. Adapter 

```java

/**
 * CnPeng 2021/7/6
 * 功用：ListAdapter + DiffUtil.ItemCallback
 * 其他：
 * - ListAdapter 是官方提供的 RecyclerView.Adapter 的抽象子类，其中通过 AsyncListDiffer 对部分函数进行了包装
 * - DiffUtil.ItemCallback 与 DiffUtil.Callback 相比，直接比较的是条目对象，而后者则比较了条目数量和条目对象。
 */
public class DiffUtilListAdapter extends ListAdapter<DiffUtilItemBean, DiffUtilListAdapter.DiffUtilViewHolder2> {

    public DiffUtilListAdapter(@NonNull @NotNull DiffUtil.ItemCallback<DiffUtilItemBean> diffCallback) {
        super(diffCallback);
    }

    @NonNull
    @NotNull
    @Override
    public DiffUtilViewHolder2 onCreateViewHolder(@NonNull @NotNull ViewGroup parent, int viewType) {
        ItemDiffutilBinding binding = DataBindingUtil.inflate(LayoutInflater.from(parent.getContext()), R.layout.item_diffutil, parent, false);
        DiffUtilViewHolder2 holder2 = new DiffUtilViewHolder2(binding.getRoot());
        holder2.setBinding(binding);
        return holder2;
    }

    @Override
    public void onBindViewHolder(@NonNull @NotNull DiffUtilViewHolder2 holder, int position) {
        holder.bindTo(getItem(position));
    }

    /**
     * CnPeng:2021/7/6 10:34 ViewHolder
     */
    public static class DiffUtilViewHolder2 extends RecyclerView.ViewHolder {

        private ItemDiffutilBinding mBinding;

        public DiffUtilViewHolder2(@NonNull @NotNull View itemView) {
            super(itemView);
        }

        public void bindTo(DiffUtilItemBean itemBean) {
            if (itemBean == null || mBinding == null) {
                return;
            }
            mBinding.setItem(itemBean);
        }

        public void setBinding(ItemDiffutilBinding binding) {
            mBinding = binding;
        }
    }
}
```

#### 1.3.1.4. 条目布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout>

    <data>

        <import type="com.cnpeng.base.diffutil.model.DiffUtilItemBean" />

        <variable
            name="item"
            type="com.cnpeng.base.diffutil.model.DiffUtilItemBean" />
    </data>

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"

        android:orientation="vertical">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@drawable/shape_bk_rect_cornor_white"
            android:backgroundTint="@color/background"
            android:orientation="vertical"
            android:padding="10dp">

            <TextView
                android:id="@+id/tv_title"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ellipsize="end"
                android:singleLine="true"
                android:text="@{item.title}"
                android:textSize="17sp"
                android:textStyle="bold"
                tools:text="这是标题" />

            <TextView
                android:id="@+id/tv_content"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="5dp"
                android:ellipsize="end"
                android:maxLines="2"
                android:text="@{item.content}"
                tools:text="这是内容，最多两行这是内容，最多两行这是内容，最多两行这是内容，最多两行这是内容，最多两行这是内容，最多两行这是内容，最多两行这是内容，最多两行这是内容，最多两行" />
        </LinearLayout>

        <View
            android:layout_width="match_parent"
            android:layout_height="5dp"
            android:background="@color/white" />
    </LinearLayout>
</layout>
```

#### 1.3.1.5. activity xml 布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout>

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        tools:context="com.cnpeng.base.diffutil.activity.DiffUtil2Activity">

        <com.cnpeng.custom.titlelayout.CpTitleLayout
            android:id="@+id/titleLayout"
            android:layout_width="match_parent"
            app:title="DiffUtil+ItemCallBack"
            android:layout_height="wrap_content" />

        <RadioGroup
            android:id="@+id/radioGroup"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:weightSum="3">

            <RadioButton
                android:id="@+id/rb1"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:checked="true"
                android:text="列表1" />

            <RadioButton
                android:id="@+id/rb2"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:text="列表2" />

            <RadioButton
                android:id="@+id/rb3"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:text="列表3" />
        </RadioGroup>

        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/rv"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:padding="@dimen/dp10"
            app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
            tools:background="#ffc"
            tools:listitem="@layout/item_diffutil" />
    </LinearLayout>
</layout>
```

#### 1.3.1.6. activity 实现

```java
/**
 * CnPeng:2021/7/6 11:13 DiffUtil.ItemCallBack 实现 RecyclerView 的数据刷新
 */
public class DiffUtil2Activity extends AppCompatActivity {

    private DiffUtilListAdapter      mAdapter2;
    private ActivityDiffUtil2Binding mBinding;
    private DiffUtilViewModel        mVm;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mBinding = DataBindingUtil.setContentView(this, R.layout.activity_diff_util2);

        initViewModel();
        initRecyclerView2();
        initRadioGroup();
    }

    private void initRadioGroup() {
        mBinding.radioGroup.setOnCheckedChangeListener(new RadioGroup.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(RadioGroup group, int checkedId) {
                if (checkedId == R.id.rb1) {
                    mAdapter2.submitList(mVm.getListToShow(1));
                } else if (checkedId == R.id.rb2) {
                    mAdapter2.submitList(mVm.getListToShow(2));
                } else {
                    mAdapter2.submitList(mVm.getListToShow(3));
                }
            }
        });

        mBinding.titleLayout.setClickListener(new CpTitleLayout.ClickListener() {
            @Override
            protected void onLeftBtnClick(View view) {
                finish();
            }
        });
    }

    private void initViewModel() {
        mVm = new ViewModelProvider(this).get(DiffUtilViewModel.class);
        mVm.initDataSet();
    }


    private void initRecyclerView2() {
        CpDiffUtilItemCallBack itemCallBack = new CpDiffUtilItemCallBack();
        mAdapter2 = new DiffUtilListAdapter(itemCallBack);
        mBinding.rv.setAdapter(mAdapter2);

        // Adapter 继承自 ListAdapter, 直接触发 submitList 即可比较并更新视图
        mAdapter2.submitList(mVm.getListToShow(1));
    }
}
```

### 1.3.2. DiffUtil.Callback+RecyclerView.Adapter 1

使用 DiffUtil.Callback+RecyclerView.Adapter 实现列表刷新时有两种版本，

* Callback 中未重写 getChangePayload 函数，直接配合 Adapter 中两个参数的 onBindViewHolder(,) 实现刷新
* Callback 中重写 getChangePayload 函数后，配合 Adapter 中三个参数的 `onBindViewHolder(,, @NonNull @NotNull List<Object> payloads)` 实现刷新

**第一种方式会刷新（重绘）整个条目内容；第二种方式可以明确获知条目中的哪些内容变更了，从而可以仅刷新（重绘）变更部分的内容，效率更高。**（前一节中的 ItemCallback 方式也可以重写 getChangePayload() 从而实现条目的局部刷新 ）

示例代码中采用的是第二种方式。

这两种方式的核心步骤和代码如下：

```java
// 1,首先将新数据集设置给Adapter
List<DiffUtilItemBean> newList = mVm.getListToShow(pos);
mAdapter.setDataList(newList);

// 2，计算新老数据集差异，将差异更新到Adapter
DiffUtil.DiffResult diffResult = DiffUtil.calculateDiff(new CpDiffUtilCallBack(mShowingList, newList));

// 3, 执行更新操作，更新时会自动根据情况调用 notifyDataSetChanged 、notifyItemMoved、notifyItemRangeRemoved、notifyItemRangeChanged 更新函数
diffResult.dispatchUpdatesTo(mAdapter);
```

列表条目布局、数据结构和 Activity 布局文件同前一节中一致，后续代码仅列举与前一节不同的内容

#### 1.3.2.1. Adapter

```java
/**
 * CnPeng 2021/7/5
 * 功用：DiffUtil 界面中使用的适配器
 * 其他：
 */
public class DiffUtilAdapter extends RecyclerView.Adapter<DiffUtilAdapter.DiffUtilHolder> {

    private List<DiffUtilItemBean> mDataList = new ArrayList<>();

    public void setDataList(List<DiffUtilItemBean> dataList) {
        mDataList = dataList;
    }

    @NotNull
    @Override
    public DiffUtilHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {

        ItemDiffutilBinding binding = DataBindingUtil.inflate(LayoutInflater.from(parent.getContext()), R.layout.item_diffutil, parent, false);

        DiffUtilHolder holder = new DiffUtilHolder(binding.getRoot());
        holder.setBinding(binding);

        return holder;
    }

    @Override
    public void onBindViewHolder(@NonNull DiffUtilHolder holder, int position) {
        ItemDiffutilBinding binding = holder.getBinding();
        DiffUtilItemBean itemBean = mDataList.get(position);
        binding.setItem(itemBean);
    }

    @Override
    public void onBindViewHolder(@NonNull @NotNull DiffUtilHolder holder, int position, @NonNull @NotNull List<Object> payloads) {
        // 配合 DiffUtil.CallBack 中的 getChangePayload 实现条目内部个别元素的局部刷新
        if (payloads.isEmpty()) {
            onBindViewHolder(holder, position);
        } else {
            ItemDiffutilBinding binding = holder.getBinding();
            DiffUtilItemBean itemBean = mDataList.get(position);

            // 读取 DiffUtil.CallBack 中通过 getChangePayload 返回的数据，并更新到视图中
            Bundle bundle = (Bundle) payloads.get(0);
            for (String key : bundle.keySet()) {
                switch (key) {
                    case "title":
                        binding.tvTitle.setText(itemBean.getTitle());
                        break;
                    case "content":
                        binding.tvContent.setText(itemBean.getContent());
                        break;
                    default:
                        break;
                }
            }
        }
    }

    @Override
    public int getItemCount() {
        return mDataList.size();
    }

    static class DiffUtilHolder extends RecyclerView.ViewHolder {

        private ItemDiffutilBinding mBinding;

        public DiffUtilHolder(@NonNull View itemView) {
            super(itemView);
        }

        public void setBinding(ItemDiffutilBinding binding) {
            mBinding = binding;
        }

        public ItemDiffutilBinding getBinding() {
            return mBinding;
        }
    }
}
```

#### 1.3.2.2. Callback

```java

/**
 * CnPeng 2021/7/5
 * 功用：DiffUtil.Callback 的实现类
 * 其他：
 */
public class CpDiffUtilCallBack extends DiffUtil.Callback {

    private final List<DiffUtilItemBean> mOldList;
    private final List<DiffUtilItemBean> mNewList;

    public CpDiffUtilCallBack(List<DiffUtilItemBean> oldList, List<DiffUtilItemBean> newList) {
        this.mOldList = oldList;
        this.mNewList = newList;
    }

    @Override
    public int getOldListSize() {
        return null == mOldList ? 0 : mOldList.size();
    }

    @Override
    public int getNewListSize() {
        return null == mNewList ? 0 : mNewList.size();
    }

    @Override
    public boolean areItemsTheSame(int oldItemPosition, int newItemPosition) {
        // 判断是不是同一个 Item ：如果Item有唯一标志的Id的话，建议此处判断id
        if (null == mOldList || null == mNewList) {
            return false;
        }

        return mOldList.get(oldItemPosition).getTitle().equals(mNewList.get(newItemPosition).getTitle());
    }

    @Override
    public boolean areContentsTheSame(int oldItemPosition, int newItemPosition) {
        // 判断两个 Item 的内容是否相同
        if (null == mOldList || null == mNewList) {
            return false;
        }

        // 如果 title 是条目的唯一标识，此处就不需要判断 title。
        if (!mOldList.get(oldItemPosition).getTitle().equals(mNewList.get(newItemPosition).getTitle())) {
            return false;
        }

        if (!mOldList.get(oldItemPosition).getContent().equals(mNewList.get(newItemPosition).getContent())) {
            return false;
        }

        return true;
    }

    @Nullable
    @Override
    public Object getChangePayload(int oldItemPosition, int newItemPosition) {
        // 当 areItemsTheSame 为 true, 但 areContentsTheSame 为 false 时，返回具体的差异项
        DiffUtilItemBean oldItem = mOldList.get(oldItemPosition);
        DiffUtilItemBean newItem = mNewList.get(newItemPosition);

        Bundle payload = new Bundle();
        if (!oldItem.getContent().equals(newItem.getContent())) {
            payload.putString("content", newItem.getContent());
        }

        if (!oldItem.getTitle().equals(newItem.getTitle())) {
            payload.putString("title", newItem.getTitle());
        }

        if (payload.size() == 0) {
            return null;
        }

        // payLoad 的数据最终会反馈到 RvAdapter 的 onBindViewHolder(@NonNull DiffUtilHolder holder, int position, @NotNull List<Object> payloads) 的 payloads
        return payload;
    }
}
```

#### 1.3.2.3. Activity

```java

/**
 * CnPeng:2021/7/5 15:36 DiffUtil 使用
 *
 * DiffUtil.CallBack 用在 RecyclerView 的界面刷新中，相较于 notifyDataSetChanged 等刷新函数来说，效率高，交互好
 *
 * 参考文章：https://www.jianshu.com/p/9636977bbc18?utm_campaign=shakespeare
 */
public class DiffUtilActivity extends AppCompatActivity {

    private ActivityDiffUtilBinding mBinding;
    private DiffUtilAdapter         mAdapter;
    private DiffUtilViewModel       mVm;
    private List<DiffUtilItemBean>  mShowingList;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mBinding = DataBindingUtil.setContentView(this, R.layout.activity_diff_util);

        initViewModel();
        initRecyclerView1();
        initBtnEvent();
    }

    private void initViewModel() {
        mVm = new ViewModelProvider(this).get(DiffUtilViewModel.class);
        mVm.initDataSet();
    }

    private void initBtnEvent() {
        mBinding.radioGroup.setOnCheckedChangeListener(new RadioGroup.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(RadioGroup group, int checkedId) {
                if (checkedId == R.id.rb1) {
                    updateByDiffUtilCallBack(1);
                } else if (checkedId == R.id.rb2) {
                    updateByDiffUtilCallBack(2);
                } else {
                    updateByDiffUtilCallBack(3);
                }
            }
        });

        mBinding.titleLayout.setClickListener(new CpTitleLayout.ClickListener() {
            @Override
            protected void onLeftBtnClick(View view) {
                finish();
            }
        });
    }

    /**
     * CnPeng:2021/7/6 10:54 基于 DiffUtilCallBack 实现数据刷新，配合 initRecyclerView1() 使用
     */
    private void updateByDiffUtilCallBack(int pos) {
        // 1,首先将新数据集设置给Adapter
        List<DiffUtilItemBean> newList = mVm.getListToShow(pos);


        // 2，计算新老数据集差异，将差异更新到Adapter
        DiffUtil.DiffResult diffResult = DiffUtil.calculateDiff(new CpDiffUtilCallBack(mShowingList, newList));
        // 更新时会自动根据情况调用 notifyDataSetChanged 、notifyItemMoved、notifyItemRangeRemoved、notifyItemRangeChanged 更新函数
        diffResult.dispatchUpdatesTo(mAdapter);

        // 3，更新本地记录的正在展示的数据
        mAdapter.setDataList(newList);
        mShowingList = newList;
    }

    private void initRecyclerView1() {
        mAdapter = new DiffUtilAdapter();
        mBinding.rv.setAdapter(mAdapter);

        mShowingList = mVm.getListToShow(1);
        mAdapter.setDataList(mShowingList);
    }
}
```

### 1.3.3. 其他补充

假设我们点击某个条目之后就可以跳转到新页面去修改其 title，修改完成后，携带新的 title 返回该页面。

这种情况下，我们明确知道是哪个条目被修改了什么数据，当我们需要将新的 title 更新到列表中时，除了前面的方式，我们还可以通过如下方式更高效的更新数据：

```java
// 1，更新item的数据源.
DiffUtilItemBean itemBean = mAdapter.getDataList().get(position);
user.setTitle(newTitle);

// 2, 传递一个 payload
Bundle payload = new Bundle();
payload.putString("title", newTitle);
mAdapter.notifyItemChanged(position, payload);
```

在上述代码中，`mAdapter.notifyItemChanged(position, payload)` 仅将变更的内容暴露出去了，这样我们就可以在 Adapter 三个参数的 `onBindViewHolder(,, @NonNull @NotNull List<Object> payloads)` 方法中实现针对于该条目的局部内容刷新。效率会比直接触发 `mAdapter.notifyItemChanged(position)` 更高。

此处需要注意，

* 当 mAdapter 直接继承自 RecyclerView.Adapter 时，需要我们在 Adapter 中提供 `getDataList()` 方法；
* 当 mAdapter 继承自 ListAdapter 时，我们直接调用 `getCurrentList()` 即可获取正在展示的 List

### 1.3.4. RecyclerView 刷新方式总结

* notifyDataSetChanged() 表示强制刷新（重绘）整个列表，效率无疑是最低的
* Callback 和 ItemCallback 方式则仅刷新（重绘）需要重绘的条目（新增或者内容发生变化的条目。当某个条目的部分内容发生变化时，也需要重绘整个条目）
* 重写 getChangePayload()+onBindViewHolder(,,,) 时，遇到内容发生变化的条目时，仅重绘其发生变化的部分。
* Callback 和 ItemCallback 刷新列表的方式会呈现默认的动画效果，交互上比较友好。

