## 一、为什么要修改AlertDialog的背景并动态控制其最大高度呢？

关于为啥要修改背景并控制其最大高度，其实只看下面这一张图就够了。
![image.png](http://upload-images.jianshu.io/upload_images/2551993-be69e8629871a9d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在上面的图中，我们可以看出，AlertDialog 中内容的默认背景是一张白底的具有大约 8px 圆角的背景图。然后我们**查看 `Theme.Holo.Light.Dialog `中的` windowBackground `定义，发现该背景图其实是一张名为 `dialog_full_holo_light .9 `的图**。

如果你想要一个圆角更大一点的圆角，那么你的直觉可能会告诉你去使用 AlertDialog.Builder 中的setView ( view ) , 在 view 中控制 背景为你需要的圆角图，但是，非常抱歉，这样行不通——如果你定义的圆角弧度大于 默认 .9 图的弧度，那么，你就能看到四个角上的 白色小角；而且，如果你调用了setMessage 之类的AlertDialog的原有控件，那么，这些控件的背景色也是没有变化的。也就是说，你设置的只是 view 的背景，而不是 AlertDialog 的背景。效果如下：


![image.png](http://upload-images.jianshu.io/upload_images/2551993-14c6e204c267beaa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么，为啥要控制 AlertDialog的最大高度呢？这个很明显，像上面这两个AlertDialog 实在是太不美丽了，而且超出屏幕的内容也无法再展示出来。我们希望的是，当内容过多时，让他滚动展示；当内容较少时有多少展示多少。

## 二、需求实现
不分析了， 都在下面的代码里了，注释的很明了。哪些方法好用，哪些方法不好用也都注释了。

修改背景的方式呢，网上有说修改 style 样式 的，但是实际使用之后没有达到预期效果，style 的代码如下:
```
  <style name="DialogIOS" parent="@android:style/Theme.Holo.Light.Dialog">
        <item name="layout">@layout/custom_alertdialog</item>
        <item name="android:windowBackground">@drawable/shape_bk_cnoneralert</item>
    </style>
```
然后在创建dialog的时候指定 style , 代码在下面完整代码中有体现，但是不好使啊。

关于控制最大高度的，网上很多的实现其实都是固定死了最大高度，如果内容过大的时候显示正常，但是如果内容达不到最大值，那么很抱歉，底下会有一堆空白区域，参考下面的效果图。关于这种情况的示例代码，在下面也有列出。

![底部有一块无内容的区域](http://upload-images.jianshu.io/upload_images/2551993-fa28efb0884033ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)






## 三、 完整代码
最终效果示意图：

![内容未达到最大高度时的效果](http://upload-images.jianshu.io/upload_images/2551993-ca0aa908d9da7886.gif?imageMogr2/auto-orient/strip)


![内容超过最大高度时的效果](http://upload-images.jianshu.io/upload_images/2551993-951ffb4a227c86ed.gif?imageMogr2/auto-orient/strip)

```
/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/6/28:上午10:02
 * <p>
 * 说明：实现的最终效果：
 * 1 、修改AlertDialog的那个默认的圆角很小的背景。
 * -- 使用 dialog.getWindow().setLayoutBackgroundResource()或 setBackgroundDrawable()
 * 2 、限制AlertDialog 的最大高度，未超过最大高度时内容多高就多高，超过最大高度时以最大高度为准
 * -- 使用 addOnLayoutChangeListener() 或 ViewTreeObserver
 * <p>
 * 要点：
 * 1 、展示dialog 的时候必须调用 AlertDialog 对象的show()
 * 2 、addOnLayoutChangeListener 的时候 在 show 之前或者 show 之后都可以
 * 3 、addOnLayoutChangeListener 中重新setLayoutParams 的时候必须是 FrameLayout.LayoutParams
 * 4 、ViewTreeObserver 方式需要在 show 之前调用
 */

public class CornerAlertDialogActivity extends AppCompatActivity {

    private Context context;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_conor_alertdialog);

        context = this;

        Button button = (Button) findViewById(R.id.bt_showCornorAlertDialog);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                showCornerAlertDialog();
            }
        });
    }

    private void showCornerAlertDialog() {
        //设置圆角背景的方式 1 ：
        //Dialog dialog = new Dialog(this);
        //Window window = dialog.getWindow();
        //window.setBackgroundDrawableResource(R.drawable.shape_bk_cnoneralert);
        //ViewGroup.LayoutParams layoutParams = new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, 
        //        ViewGroup.LayoutParams.MATCH_PARENT);
        //View view = LayoutInflater.from(this).inflate(R.layout.custom_alertdialog, null);
        //dialog.setContentView(view, layoutParams);
        //dialog.show();


        // 使用style模式设置的windowBackground 并不好使，依旧会有默认背景的展示
        //AlertDialog.Builder builder = new AlertDialog.Builder(this, R.style.DialogIOS); 

        //完整设置圆角和最大高度 方式1 ：
        // 还要注意：如果使用builder 去 setView，那么获取alertDialog对象的操作必须在setView完毕之后，否则，界面中只有一个背景，不显示内容。
        //或者，直接先获取AlertDialog对象，然后用alertDialog 本身的setView 去设置View.
        AlertDialog.Builder builder = new AlertDialog.Builder(this);
        final View view = LayoutInflater.from(this).inflate(R.layout.custom_alertdialog, null);
        builder.setMessage("TestMessage xxxxxxx");
        builder.setPositiveButton("ok", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {

            }
        });
        builder.setView(view);  //在setView之前调用builder的原有设置控件方法时，能展示设置的控件，之后设置的则不展示！！
        AlertDialog dialog = builder.create();
        dialog.getWindow().setBackgroundDrawable(getResources().getDrawable(R.drawable.shape_bk_cnoneralert));
        //builder.show();   //用这个的话，背景并不会改变,依旧是默认的

        dialog.show();  //必须用这个show 才能显示自定义的dialog window 的背景

        //这种设置宽高的方式也是好使的！！！-- show 前调用，show 后调用都可以！！！
        view.addOnLayoutChangeListener(new View.OnLayoutChangeListener() {
            @Override
            public void onLayoutChange(View v, int left, int top, int right, int bottom, int oldLeft, int oldTop,
                                       int oldRight, int oldBottom) {
                int height = v.getHeight();     //此处的view 和v 其实是同一个控件
                int contentHeight = view.getHeight();

                LogUtils.e("高度", height + " / " + " / " + contentHeight);
                int needHeight = 500;

                if (contentHeight > needHeight) {
                    //注意：这里的 LayoutParams 必须是 FrameLayout的！！
                    view.setLayoutParams(new FrameLayout.LayoutParams(FrameLayout.LayoutParams.MATCH_PARENT, 
                            needHeight));
                }
            }
        });


        //==============================================================================================================
        //// 完整设置圆角和最大高度 方式2： 修改AlertDialog的背景，同时控制最大的高度，超过了则展示到最大高度，没超过则有多少展示多少
        //AlertDialog.Builder builder = new AlertDialog.Builder(context);
        //builder.setCancelable(false);                   //这个true和false表示点击返回键时能否关闭dialog
        //
        //final View updateDialogView = LayoutInflater.from(context).inflate(R.layout.custom_alertdialog, null);
        //// 使用style模式设置的windowBackground 并不好使，依旧会有默认背景的展示
        ////        builder.setView(updateDialogView); 
        //AlertDialog alertDialog = builder.create();
        //alertDialog.setCanceledOnTouchOutside(false);   //点击dialog外部的区域不予许关闭
        //alertDialog.setView(updateDialogView);
        //
        //final Window window = alertDialog.getWindow();
        //if (null != window) {
        //    window.setBackgroundDrawableResource(R.drawable.shape_bk_cnoneralert);//更改dialog默认背景
        //
        //    ViewTreeObserver vto = updateDialogView.getViewTreeObserver();
        //    vto.addOnPreDrawListener(new ViewTreeObserver.OnPreDrawListener() {
        //        public boolean onPreDraw() {
        //            int height = updateDialogView.getMeasuredHeight();  //获取要绘制的高度
        //            int width = updateDialogView.getMeasuredWidth();
        //
        //            WindowManager wm = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);
        //            DisplayMetrics metrics = new DisplayMetrics();  //获取整个窗体的宽高
        //            wm.getDefaultDisplay().getMetrics(metrics);
        //            int windowHeight = metrics.heightPixels;
        //            int windowWidth = metrics.widthPixels;
        //
        //            int maxHeight = (int) (windowHeight * 0.577); //设计需求是770px ,但是770px显示的内容太少了
        //            int finalWidth = (int) (windowWidth * 0.733);
        //
        //            LogUtils.e("宽、高", height + "/" + width);
        //            if (height < maxHeight) {       //控制最大宽高
        //                window.setLayout(finalWidth, height);
        //            } else {
        //                window.setLayout(finalWidth, maxHeight);
        //            }
        //            return true;
        //        }
        //    });
        //}
        //alertDialog.show();     //必须用AlertDialog 对象去show

        //==============================================================================================================
        //这一个也是不好使的，不论是在show 前还是后， lp.width 拿到的值一直是 -2 
        // WindowManager.LayoutParams lp = new WindowManager.LayoutParams();
        // lp.copyFrom(dialog.getWindow().getAttributes());
        // int dialogWidth = lp.width;
        // int dialogHeight = lp.height;
        //
        // if (dialogHeight > 700) {
        //     dialog.getWindow().setLayout(dialogWidth, 700);
        // }

        //==============================================================================================================
        // //这是设置圆角dialog的另外一个示例      
        // AlertDialog.Builder builder = new AlertDialog.Builder(this);
        //  View contentView = getLayoutInflater().inflate(R.layout.custom_alertdialog, null);
        //  builder.setView(contentView);
        //  AlertDialog dialog = builder.create();
        //  //        dialog.getWindow().setBackgroundDrawable(new BitmapDrawable()); // 背景透明.       
        //  dialog.getWindow().setBackgroundDrawable(getResources().getDrawable(R.drawable.shape_bk_cnoneralert));
        //  dialog.setCanceledOnTouchOutside(false); // 点击外部不消失.       
        //  dialog.getWindow().setGravity(Gravity.CENTER); // 位置.      
        //  dialog.show();

        //==============================================================================================================
        //  //这种方法实际测试也是不好使！！getAttributes()获取到的p中，p.height和 p.width 都是-2 ，如果想固定dialog的宽高的话，可以使用这种
        //  WindowManager m = getWindowManager();
        //  Display d = m.getDefaultDisplay(); // 获取屏幕宽、高用       
        //  WindowManager.LayoutParams p = dialog.getWindow().getAttributes(); // 获取对话框当前的参数值      
        //  p.height = (int) (d.getHeight() * 0.4); // 高度设置为屏幕的0.4      
        //  p.width = (int) (d.getWidth() * 0.9); // 宽度设置为屏幕的0.9  
        // dialog.getWindow().setAttributes(p);//  注意: dialog.getWindow().setAttributes(p); 须在 show() 方法之后调用

        //==============================================================================================================
        ////下面这种方式实际测试并不好使！！heightPixels 一直就是屏幕整体的高度，而不是dialog内容区域的高度！！！
        // 如果想直接固定大小的话可以使用该方法，需要在show 之后调用
        // //WindowManager wm = (WindowManager) getSystemService(Context.WINDOW_SERVICE);
        // WindowManager wm = getWindowManager();
        // DisplayMetrics metrics = new DisplayMetrics();
        // wm.getDefaultDisplay().getMetrics(metrics);
        // if (metrics.heightPixels < 200) {
        //     dialog.getWindow().setLayout(WindowManager.LayoutParams.WRAP_CONTENT, WindowManager.LayoutParams
        //             .WRAP_CONTENT);
        // } else {
        //     dialog.getWindow().setLayout(WindowManager.LayoutParams.WRAP_CONTENT, 100);
        // }
    }
}
```
