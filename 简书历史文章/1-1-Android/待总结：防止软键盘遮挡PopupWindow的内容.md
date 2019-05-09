popwindow.setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_RESIZE);   //防止软键盘遮挡内容

```
 popwindow = new PopupWindow(conentView, WindowManager.LayoutParams.MATCH_PARENT, WindowManager.LayoutParams
                .WRAP_CONTENT);
        popwindow.setFocusable(true);// 设置窗口的控件可以点击，很重要，要是不能点击只是个显示图片的
        popwindow.setWidth(ActionBar.LayoutParams.MATCH_PARENT);// 设置pop的宽度
        popwindow.setHeight(ActionBar.LayoutParams.MATCH_PARENT);// 高度
        ColorDrawable dw = new ColorDrawable(0xb0000000);// 实例化一个ColorDrawable颜色为半透明
        popwindow.setBackgroundDrawable(dw);// 根据实例化的对象，设置颜色为半透明
        popwindow.setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_RESIZE);   //防止软键盘遮挡内容
        popwindow.showAtLocation(conentView, Gravity.BOTTOM | Gravity.CENTER_HORIZONTAL, 0, 0);
```
