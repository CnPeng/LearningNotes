直接通过 `PopupWindow(view)` 构造的 popup 对象在调用 showXX 方法时不显示？

**那就换个带有宽高信息的构造函数，很多时候，不设置宽高会导致不显示！！**

或者为 popup 对象调用 `setWidth(w)` 和 `setHeight(h)` 方法