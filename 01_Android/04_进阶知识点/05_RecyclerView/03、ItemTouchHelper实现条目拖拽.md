# ItemTouchHelper 实现拖拽和侧滑

ItemTouchHelper 用来处理 RecyclerView 条目的拖拽或者侧滑事件，借助它可以实现类似支付宝首页条目拖拽的效果（目前很多新闻资讯类的APP也有类似的拖拽功能)。


## 一、使用步骤分析

使用步骤大致如下：

* 创建 ItemTouchHelper.Callback 的实现类
* 构建 ItemTouchHelper ，构建时传递上一步中 Callback 的实现类
* 通过 ItemTouchHelper 对象的 `attachToRecyclerView(rv)` 与 Rv 绑定


## 二、SimpleCallback

SimpleCallBack 是对 Callback 的一个简单包装，我们可以直接在其构造中传入响应拖拽或侧滑的事件方向。然后直接重写相应的方法实现对应侧滑或者删除事件。

除此以外，我们依旧可以重写其他更多方法得到不同的效果，具体参考下一节内容。

```java
ItemTouchHelper mIth = new ItemTouchHelper(
    new ItemTouchHelper.SimpleCallback(ItemTouchHelper.UP | ItemTouchHelper.DOWN,
        ItemTouchHelper.LEFT) {
        public abstract boolean onMove(RecyclerView recyclerView,
            ViewHolder viewHolder, ViewHolder target) {
            final int fromPos = viewHolder.getAdapterPosition();
            final int toPos = target.getAdapterPosition();
            // move item in `fromPos` to `toPos` in adapter.
            return true;// true if moved, false otherwise
        }
        public void onSwiped(ViewHolder viewHolder, int direction) {
            // remove from adapter
        }
});
```

## 三、自定义 Callback

自定义 Callback 时，各主要函数的作用如下：

### 1、getMovementFlags(,) 

```java
@Override
public int getMovementFlags(@NonNull RecyclerView recyclerView, @NonNull RecyclerView.ViewHolder viewHolder) {
    int dragFlags = 0;
    int swipeFlags = 0;

    RecyclerView.LayoutManager layoutManager = recyclerView.getLayoutManager();

    //CnPeng 2019/4/26 9:08 AM 非 LinearLayoutManager 不允许侧滑删除
    if (layoutManager instanceof GridLayoutManager || layoutManager instanceof StaggeredGridLayoutManager 
    || layoutManager instanceof FlexboxLayoutManager) {
        dragFlags = ItemTouchHelper.LEFT | ItemTouchHelper.UP | ItemTouchHelper.RIGHT | ItemTouchHelper.DOWN;
    } else if (layoutManager instanceof LinearLayoutManager) {
        dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN;
        swipeFlags = ItemTouchHelper.START | ItemTouchHelper.END;
    }

    return makeMovementFlags(dragFlags, swipeFlags);
}
```

上述方法中通过 `makeMovementFlags(dragFlags, swipeFlags)` 获取了一个 int 值。

dragFlags 决定了响应拖拽的事件方向，swipeFlags 决定了响应侧滑删除的事件方向。如果这两个 flag 的值为 0 ，则表示不响应对应的事件。


### 2、onMove(,,)

拖拽过程中不停移动的事件，返回值表示该事件是否已经被处理/消费。

```java
@Override
public boolean onMove(@NonNull RecyclerView recyclerView, @NonNull RecyclerView.ViewHolder viewHolder, @NonNull RecyclerView.ViewHolder target) {
    if (viewHolder.getItemViewType() == target.getItemViewType()) {
        int srcPosition = viewHolder.getAdapterPosition();
        int targetPosition = target.getAdapterPosition();

        if (null != mItemDragListener) {
        	  // 这是自定义的监听
            mItemDragListener.onItemMoved(srcPosition, targetPosition);
            return true;
        }
    }
    return false;
}
```

上述示例代码中，第二个参数 `viewHolder` 表示当前被拖拽的条目 Holder，`tagert` 则表示目标条目的 Holder.

在该方法中，通常用来做数据交换操作，通过 `Collections.swap(list,srcPostiion,targetPosition)`。需要注意的是，记得要比对 itemViewType 是否一致。

### 3、onSwiped(,)

触发侧滑删除时的回调处理

```java
@Override
public void onSwiped(@NonNull RecyclerView.ViewHolder viewHolder, int direction) {
    // CnPeng 2019/4/26 8:51 AM 删除事件
    if (null != mItemDragListener) {
        mItemDragListener.onItemSwiped(viewHolder.getAdapterPosition(), direction);
    }
}
```

### 4、onSelectedChanged(,)

选中状态发生变化时的回调处理。通过 `actionState` 可以判断是将要触发拖拽还是删除。

通常在触发对应操作时会改变条目背景色

```java
@Override
public void onSelectedChanged(@Nullable RecyclerView.ViewHolder viewHolder, int actionState) {
    if (null != mItemDragListener) {
        if (ItemTouchHelper.ACTION_STATE_DRAG == actionState) {
            mItemDragListener.onItemBeingMove();
        } else if (ItemTouchHelper.ACTION_STATE_SWIPE == actionState) {
            mItemDragListener.onItemBeingSwipe();
        }
    }

    super.onSelectedChanged(viewHolder, actionState);
}
```

### 5、clearView(,)

拖拽或者侧滑事件已经执行完毕，此时，通常会做一些界面恢复的操作。

```java
@Override
public void clearView(@NonNull RecyclerView recyclerView, @NonNull RecyclerView.ViewHolder viewHolder) {
    if (null != mItemDragListener) {
        mItemDragListener.onItemDragEventOver();
    }

    super.clearView(recyclerView, viewHolder);
}
```

### 6、isItemViewSwipeEnabled()

是否默认开启侧滑删除。不重写时，默认 true——开启，

```java
@Override
public boolean isItemViewSwipeEnabled() {
    // 返回 false 表示由外部动态通过 startSwipe()控制删除事件
    return false;
}
```

### 7、isLongPressDragEnabled() 

是否默认开启长按拖拽事件。不重写时，默认 true——开启。

```java
@Override
public boolean isLongPressDragEnabled() {
    // 返回 fasle 表示由外部动态通过 startDrag() 开启拖拽事件
    return false;
}
```

### 8、onChildDraw(,,,,,,)

子 View 正在重绘，通常用来响应一些用户的交互行为，比如，增加一个动画

```java
@Override
public void onChildDraw(Canvas c, RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder
, float dX, float dY, int actionState, boolean isCurrentlyActive) {
    if (actionState == ItemTouchHelper.ACTION_STATE_SWIPE) {
        //自定义滑动动画
        final float alpha = ALPHA_FULL - Math.abs(dX) / (float) viewHolder.itemView.getWidth();
        viewHolder.itemView.setAlpha(alpha);
        viewHolder.itemView.setTranslationX(dX);
    } else {
        super.onChildDraw(c, recyclerView, viewHolder, dX, dY, actionState, isCurrentlyActive);
    }
}
```

## 四、自定义 Callback 的完整示例代码

### 1、AntRvItemDragCallBack.java

```java
import com.google.android.flexbox.FlexboxLayoutManager;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.recyclerview.widget.GridLayoutManager;
import androidx.recyclerview.widget.ItemTouchHelper;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;
import androidx.recyclerview.widget.StaggeredGridLayoutManager;

/**
 * 作者：CnPeng
 * 时间：2019/4/26
 * 功用：RecyclerView 条目拖拽/侧滑 的回调
 * 其他：
 */
public class AntRvItemDragCallBack extends ItemTouchHelper.Callback {

    private AntRvItemDragListener mItemDragListener;

    /**
     * @param itemDragListener 条目移动/侧滑的监听，建议由 Rv 的 adapter 去实现
     */
    public AntRvItemDragCallBack(AntRvItemDragListener itemDragListener) {
        mItemDragListener = itemDragListener;
    }

    @Override
    public int getMovementFlags(@NonNull RecyclerView recyclerView, @NonNull RecyclerView.ViewHolder viewHolder) {
        int dragFlags = 0;
        int swipeFlags = 0;

        RecyclerView.LayoutManager layoutManager = recyclerView.getLayoutManager();

        //CnPeng 2019/4/26 9:08 AM 非 LinearLayoutManager 不允许侧滑删除
        if (layoutManager instanceof GridLayoutManager || layoutManager instanceof StaggeredGridLayoutManager || layoutManager instanceof FlexboxLayoutManager) {
            dragFlags = ItemTouchHelper.LEFT | ItemTouchHelper.UP | ItemTouchHelper.RIGHT | ItemTouchHelper.DOWN;
        } else if (layoutManager instanceof LinearLayoutManager) {
            dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN;
            swipeFlags = ItemTouchHelper.START | ItemTouchHelper.END;
        }

        return makeMovementFlags(dragFlags, swipeFlags);
    }

    @Override
    public boolean onMove(@NonNull RecyclerView recyclerView, @NonNull RecyclerView.ViewHolder viewHolder, @NonNull RecyclerView.ViewHolder target) {

        if (viewHolder.getItemViewType() == target.getItemViewType()) {
            int srcPosition = viewHolder.getAdapterPosition();
            int targetPosition = target.getAdapterPosition();

            if (null != mItemDragListener) {
                mItemDragListener.onItemMoved(srcPosition, targetPosition);
                return true;
            }
        }

        return false;
    }

    @Override
    public void onSwiped(@NonNull RecyclerView.ViewHolder viewHolder, int direction) {
        // CnPeng 2019/4/26 8:51 AM 删除事件
        if (null != mItemDragListener) {
            mItemDragListener.onItemSwiped(viewHolder.getAdapterPosition(), direction);
        }
    }

    @Override
    public void onSelectedChanged(@Nullable RecyclerView.ViewHolder viewHolder, int actionState) {
        if (null != mItemDragListener) {
            if (ItemTouchHelper.ACTION_STATE_DRAG == actionState) {
                mItemDragListener.onItemBeingMove();
            } else if (ItemTouchHelper.ACTION_STATE_SWIPE == actionState) {
                mItemDragListener.onItemBeingSwipe();
            }
        }

        super.onSelectedChanged(viewHolder, actionState);
    }

    @Override
    public void clearView(@NonNull RecyclerView recyclerView, @NonNull RecyclerView.ViewHolder viewHolder) {
        if (null != mItemDragListener) {
            mItemDragListener.onItemDragEventOver();
        }

        super.clearView(recyclerView, viewHolder);
    }

    @Override
    public boolean isItemViewSwipeEnabled() {
        // 返回 false 表示由外部动态通过 startSwipe()控制删除事件
        return false;
    }

    @Override
    public boolean isLongPressDragEnabled() {
        // 返回 fasle 表示由外部动态通过 startDrag() 开启拖拽事件
        return false;
    }
}
```

### 2、AntRvItemDragListener.java

```java
/**
 * 作者：CnPeng
 * 时间：2019/4/26
 * 功用：RecyclerView 条目的拖拽或者侧滑监听回调
 */
public interface AntRvItemDragListener {

    /**
     * 被移动了（产生了拖拽交换事件）
     * 注意：在 RV 中通知刷新时，最好使用 notifyItemMoved(srcPosition, targetPosition);
     *
     * @param srcPosition    源 item 的 position
     * @param targetPosition 目标 item 的 position
     */
    void onItemMoved(int srcPosition, int targetPosition);

    /**
     * 已经被侧滑删除
     *
     * @param position  被删除的 item 的 position
     * @param direction {@link androidx.recyclerview.widget.ItemTouchHelper 中的 onSwipe 的 direction}
     */
    void onItemSwiped(int position, int direction);

    /**
     * 侧滑删除事件将要发生
     */
    void onItemBeingSwipe();

    /**
     * 将要被拖拽移动
     */
    void onItemBeingMove();

    /**
     * 拖拽/侧滑事件处理完毕
     */
    void onItemDragEventOver();
}
```

### 3、由对应的 RecyclcerView Adapter 实现上述的监听

当 RecyclerView Adapter 中的条目 view 触发 长按事件时，通过 itemTouchHelper 去调用 startDrag() 即可执行拖拽操作。

(下面示例中的 BaseRvAdapter 是自定义的 RecyclerView 的适配器基类)

```java
/**
 * 作者：CnPeng
 * 时间：2019-04-27
 * 功用：首页 TAB 排序时使用的适配器
 * 其他：
 * 1、条目有三个类型： 我的栏目-0，栏目类型标题-1，推荐栏目-2 (不在 我的栏目 中的都是推荐栏目)
 * 2、对于 我的栏目 会有拖拽排序的操作
 * 3、所有的栏目条目都会有两个状态：编辑中，非编辑状态
 *
 * 该类使用了 DataBinding 的一些高级用法，可参考 https://github.com/CnPeng/LearningNotes 中的 《DataBinding 指南》
 */
public class TabSortAdapter extends BaseRvAdapter implements AntRvItemDragListener{
    public final  int               TYPE_TAB            = 2;
    private final int               TYPE_TITLE          = 3;
    private       List<HomeTabBean> mMyTabList          = new ArrayList<>();
    private       List<HomeTabBean> mOtherTabList       = new ArrayList<>();
    private       List<HomeTabBean> mAllHomeTabBeanList = new ArrayList<>();
    private final HomeTabBean       mOtherTitleTabBean;

    public TabSortAdapter(List<HomeTabBean> myTabList, List<HomeTabBean> otherTabList) {
        if (null == myTabList) {
            myTabList = new ArrayList<>();
        }

        if (null == otherTabList) {
            otherTabList = new ArrayList<>();
        }

        mMyTabList.clear();
        mMyTabList.addAll(myTabList);
        mOtherTabList.clear();
        mOtherTabList.addAll(otherTabList);

        mAllHomeTabBeanList.clear();
        mAllHomeTabBeanList.add(new HomeTabBean("我的栏目", "", "", -1));
        mAllHomeTabBeanList.addAll(myTabList);

        mOtherTitleTabBean = new HomeTabBean("推荐栏目", "", "", 1);
        if (!mOtherTabList.isEmpty()) {
            mAllHomeTabBeanList.add(mOtherTitleTabBean);
            mAllHomeTabBeanList.addAll(mOtherTabList);
        }
    }

    @Override
    public int getContentCount() {
        return mAllHomeTabBeanList.size();
    }

    @Override
    public void onBindContentHolder(@NonNull RecyclerView.ViewHolder holder, int position) {
        HomeTabBean curTabBean = mAllHomeTabBeanList.get(position);
        
        //...其他不相关代码已经移除

        if (holder instanceof ItemTabSortHolder) {
            ViewDataBinding dataBinding = ((ItemTabSortHolder) holder).getBinding();

            dataBinding.getRoot().setOnLongClickListener(v -> {
                if (0 == curTabBean.tabGroupType && null != mItemTouchHelper ) {
                    //CnPeng 2019-04-28 21:16 只有 我的栏目 才能响应拖拽事件
                    mItemTouchHelper.startDrag(holder);
                    return true;
                } else {
                    return false;
                }
            });

            dataBinding.executePendingBindings();
        }
    }

    @Override
    public RecyclerView.ViewHolder onCreateContentHolder(@NonNull ViewGroup parent, int viewType) {
        LayoutInflater inflater = LayoutInflater.from(parent.getContext());

        ViewDataBinding viewDataBinding;
        if (viewType == TYPE_TAB) {
            viewDataBinding = DataBindingUtil.inflate(inflater, R.layout.item_tab_sort, parent, false);
        } else {
            viewDataBinding = DataBindingUtil.inflate(inflater, R.layout.item_tab_sort_title, parent, false);
        }
        return new ItemTabSortHolder(viewDataBinding.getRoot(), viewDataBinding);
    }

    @Override
    public int getContentItemType(int position) {
        HomeTabBean tabBean = mAllHomeTabBeanList.get(position);
        if (null != tabBean) {
            return 1 == tabBean.tabGroupType || -1 == tabBean.tabGroupType ? TYPE_TITLE : TYPE_TAB;
        } else {
            return super.getContentItemType(position);
        }
    }


    @Override
    public void onItemMoved(int srcPosition, int targetPosition) {

        HomeTabBean srcBean = mAllHomeTabBeanList.get(srcPosition);
        HomeTabBean targetBean = mAllHomeTabBeanList.get(targetPosition);

        if (srcBean.tabGroupType == targetBean.tabGroupType) {
            Collections.swap(mAllHomeTabBeanList, srcPosition, targetPosition);
            notifyItemMoved(srcPosition, targetPosition);
        }
    }

    class ItemTabSortHolder extends RecyclerView.ViewHolder {

        private ViewDataBinding mBinding;

        ItemTabSortHolder(@NonNull View itemView, ViewDataBinding binding) {
            super(itemView);

            mBinding = binding;
        }

        public void setBinding(ViewDataBinding binding) {
            mBinding = binding;
        }

        public ViewDataBinding getBinding() {
            return mBinding;
        }
    }
}
```


### 4、创建 ItemTouchHelper ，并将其与 RecyclerView 绑定

```java
TabSortHelper helper = new TabSortHelper(mContext);

TabSortAdapter adapter = new TabSortAdapter(helper.getMyTabList(), helper.getOtherTabList());
dialogBinding.rvTabs.setAdapter(adapter);

AntRvItemDragCallBack itemDragHelper = new AntRvItemDragCallBack(adapter);
ItemTouchHelper itemTouchHelper = new ItemTouchHelper(itemDragHelper);
itemTouchHelper.attachToRecyclerView(dialogBinding.rvTabs);
adapter.setItemTouchHelper(itemTouchHelper);
```

---

## 五、参考链接：

[Android ItemTouchHelper 实践](http://wuxiaolong.me/2016/12/10/ItemTouchHelper/)

