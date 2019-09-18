

https://stackoverflow.com/questions/52673053/cant-use-androidbackground-with-button-from-the-new-material-components

```java
WindowManager.LayoutParams lp = new WindowManager.LayoutParams();
lp.copyFrom(dialog.getWindow().getAttributes());
int dialogWidth = lp.width;
int dialogHeight = lp.height;

if(dialogHeight > MAX_HEIGHT) {
   d.getWindow().setLayout(width,MAX_HEIGHT);
} 
```


在项目中，之前我限定最大高度时，使用的方法与上述方法不一致，上述方法等待确认。
目前项目中的对应文件为 CustomtAlertDialog