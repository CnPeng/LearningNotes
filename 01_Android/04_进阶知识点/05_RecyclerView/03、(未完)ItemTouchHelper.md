ItemTouchHelper 用来处理 RecyclerView 条目的拖拽或者侧滑事件，借助它可以实现类似支付宝首页条目拖拽的效果（目前很多新闻资讯类的APP也有类似的拖拽功能)。

## 一、使用步骤分析

使用步骤大致如下：

* 创建 ItemTouchHelper.Callback 的实现类
* 构建 ItemTouchHelper ，构建时传递上一步中 Callback 的实现类
* 通过 ItemTouchHelper 对象的 `attachToRecyclerView(rv)` 与 Rv 绑定


## 二、使用默认的 Callback 实现

## 三、自定义 Callback

自定义 Callback 时，各主要函数的作用如下：

函数|作用|备注
---|---|---



## 四、自定义 Callback 的完整示例代码

*  AntRvItemDragCallBack

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

* AntRvItemDragListener

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

* 由对应的 RecyclcerViewAdapter 实现 上述的监听

当 RecyclerView Adapter 中的条目 view 触发 长按事件时，通过 itemTouchHelper 去调用 startDrag() 即可执行拖拽操作。


* 创建 ItemTouchHelper ，并将其与 RecyclerView 绑定

```java
AntRvItemDragCallBack itemDragHelper = new AntRvItemDragCallBack(adapter);
ItemTouchHelper itemTouchHelper = new ItemTouchHelper(itemDragHelper);
itemTouchHelper.attachToRecyclerView(dialogBinding.rvTabs);
adapter.setItemTouchHelper(itemTouchHelper);
```


---

## 参考链接：

[官方文档--API](https://developer.android.com/reference/android/support/v7/widget/helper/ItemTouchHelper)

[ItemTouchHelper 使用RecyclerView打造可拖拽的GridView](https://blog.csdn.net/liaoinstan/article/details/51200618)

[Android ItemTouchHelper 实践](http://wuxiaolong.me/2016/12/10/ItemTouchHelper/)

[Android swipe menu with RecyclerView](https://codeburst.io/android-swipe-menu-with-recyclerview-8f28a235ff28)

[RecyclerView的拖动和滑动 第一部分 ：基本的ItemTouchHelper示例](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0630/3123.html)

[使用ItemTouchHelper高效地实现 今日头条 、网易新闻 的频道排序、移动](https://www.jianshu.com/p/d30fd8da4eac)

[Android实现RecyclerView侧滑删除和长按拖拽-ItemTouchHelper](https://blog.csdn.net/u010687392/article/details/47950199)

[https://blog.csdn.net/a553181867/article/details/54799391](https://blog.csdn.net/a553181867/article/details/54799391)

[RecyclerView进阶：使用ItemTouchHelper实现拖拽和侧滑删除](https://segmentfault.com/a/1190000005645907)

[类似支付宝应用管理界面——RecyclerView+ItemTouchHelper实现拖拽滑动](http://lruheng.com/2017/02/14/%E7%B1%BB%E4%BC%BC%E6%94%AF%E4%BB%98%E5%AE%9D%E5%BA%94%E7%94%A8%E7%AE%A1%E7%90%86%E7%95%8C%E9%9D%A2%E2%80%94%E2%80%94RecycleView+ItemTouchHelper%E5%AE%9E%E7%8E%B0%E6%8B%96%E6%8B%BD%E6%BB%91%E5%8A%A8/)

[Swipe ⇆ Drag ⇅ Bind RecyclerView](https://medium.com/fueled-engineering/swipe-drag-bind-recyclerview-817408125530)

[Android Gestures, Animations & Flexible UIs ItemTouchHelper Setup](https://www.learnhowtoprogram.com/android/gestures-animations-flexible-uis/itemtouchhelper-setup)

[ANDROID RECYCLEVIEW SWIPE TO DISMISS AND ITEM SORTER USING ITEMTOUCHHELPER AND ITEMTOUCHHELPER.SIMPLECALLBACK](https://inducesmile.com/android/android-recycleview-swipe-to-dismiss-and-item-sorter-using-itemtouchhelper-and-itemtouchhelper-simplecallback/)

[How to add Swipe and Drag&drop support to RecyclerView](https://en.proft.me/2017/12/14/how-add-swipe-and-dragdrop-support-recyclerview/)

