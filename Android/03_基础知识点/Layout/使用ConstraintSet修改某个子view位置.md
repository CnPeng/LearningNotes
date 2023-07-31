# 1. 使用ConstraintSet修改某个子view位置

如果父 View 是 `ConstraintLayout` 约束布局，当需要修改该 View 的位置时候 ， 不能直接使用 `ConstraintLayout.LayoutParams`  ， 而应该使用 `ConstraintSet` 。

```java
ConstraintSet set= new ConstraintSet();

//mConstraintLayout 是你当前使用的约束布局
set.clone(mConstraintLayout);

//重新设置边距，当然也可以重新设置这个view的任何约束，第一个参数为需要调整的视图id，第二个是需要调整的属性取值。
set.setMargin(R.id.id, 1,Utils.dip2px(20)); 
set.setMargin(R.id.id, 3,imageTopMare+Utils.dip2px(20));

//设置新的ConstraintLayout约束 这个必须设置;
set.applyTo(mConstraintLayout);
```

注意：

**使用 `ConstraintSet` 的时候，约束布局里面的所有子 view 都必须有个 id ，不然就会报错 `All children of ConstraintLayout must have ids to use ConstraintSet`。**