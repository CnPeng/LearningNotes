![输入图片说明](https://images.gitee.com/uploads/images/2018/0705/194624_fc49ea46_930142.png "屏幕截图.png")

ListView底层直接报角标越界，

### 问题原因：

getItemViewType 时 Type的定义有问题。正常情况下，TYPE 必须是从0开始的int数值。两个TYPE之间的跨度必须是1.

### 问题解决：

出现这个问题是因为，在定义TYPE时，先定义了一个0，然后又定义了一个2，然后在getItemViewType 的时候就爆了上面的错误。—— 之前其实也定义了1，但是改代码时把1给注释掉了。。。。