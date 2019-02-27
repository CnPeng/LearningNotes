## 错误如下图：
![](https://gitee.com/uploads/images/2018/0328/160359_367c1911_930142.png "屏幕截图.png")

在携带数据重新进入编辑界面时，为了将光标置于末位，使用了setSelection（）
但是出现了上述错误。

## 问题原因：
原因是：setSelection() 方法在回填数据的setText()之前调用的