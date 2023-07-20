RecyclerView 中将指定条目滚动到 (0,0) 位置。

有两种方案：

* **方案一：**`layoutManager.scrollToPositionWithOffset(i, 0)` ，第一个参数为指定条目的索引，第二个参数为距离 Rv 父布局顶部的距离。这种方式不是平滑滚动的，是直接切换的。
* **方案二：**重写 `LinearSmoothScroller`, 指定其 Snap 方式为 `SNAP_TO_START`, 然后调用 
`layoutManager.startSmoothScroll(linearSmoothScroller)`, 这种方式参考了 `startSmoothScroll` 的源码内容，代码多一些，但是具有平滑滚动的效果。

```java
LinearLayoutManager layoutManager = (LinearLayoutManager) mBinding.rvDept.getLayoutManager();
//CnPeng 2019-06-10 18:20 使用这种没有平滑滚动效果，是直接跳到 0，0 的
//  layoutManager.scrollToPositionWithOffset(i, 0);

//CnPeng 2019-06-10 18:20 下面这种方式带有一个平滑滚动效果——参考了 LinearLayoutManager 的 smoothScrollToPosition()
LinearSmoothScroller linearSmoothScroller = new LinearSmoothScroller(mActivity) {
     @Override
     protected int getVerticalSnapPreference() {
         return SNAP_TO_START;
     }

     @Override
     protected int getHorizontalSnapPreference() {
         return SNAP_TO_START;
     }
 };
linearSmoothScroller.setTargetPosition(i);
if (layoutManager != null) {
    layoutManager.startSmoothScroll(linearSmoothScroller);
}
```