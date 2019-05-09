##1 、事件背景：
当前有这么一个需求，从 A  activity 跳转到 B activity , 然后在 B activity 界面中展示一个 自定义View 的AlertDialog (注意，只是通过setView( ) 方法自定义了view内容，并不是自定义控件)，该view包含两个 Button ，一个取消一个确定，点击取消关闭当前页面并返回A ；点击确定 关闭当前页面 并跳转到 C activity。

##2、故障现象
通过设置onClickListener 监听器，当这两个button被点击的时候，先处理一些数据，然后直接调用activity 的finish() 方法关闭当前activity 。然后在运行的时候没有崩溃，但是打印了一堆 ERROR级别的log，内容如下：
` android.view.WindowLeaked: Activity com.cnpeng.cnpeng_demos2017_01.b_05_alertDialog_dismiss .DissmissAlertDialog has leaked window com.android.internal.policy.impl.PhoneWindow$DecorView{1a1d1b07 V.E..... R......D 0,0-684,238} that
 was originally added here
` 这段错误的意思是说：由于Activity在创建之前其中的 某个控件已经存在了（或者说是，在 activity 销毁之后它内部的控件还有没销毁的），所以产生了 窗体溢出。
##3、故障原因及解决：
那么，故障点在哪呢？经过仔细排查，发现是由于 两个button是自己添加的，而不是调用AlertDialog原有的positiveButton 和 NagetiveButton , 在button的点击事件中调用activity 的 finish( ) 前没有关闭 AlertDialog 。所以，这就导致了activity被销毁之后，AlertDialog 对象依旧存在。

而在AlertDialog 提供的PositiveButton 和  NagetiveButton 中已经封装好了 销毁 AlertDialog 的方法 , 所以不需要我们去做处理。

那么当 AlertDialog 中的按钮被点击之后，如何去销毁AlertDialog呢？其实，也很简单，先获取 AlertDialog 对象，然后调用其dismiss( ) 方法去销毁。but , **此处必须要注意：在获取 AlertDialog 对象的时候，不能使用 create( )方法去获取，使用该方法获取到的对象去销毁AlertDialog 时依旧会报溢出！！！必须使用 show( ) 方法获取AlertDialog对象，这样才不会再次产生溢出**。示例代码如下：
##4、示例代码
此处只给出 通过setView( ) 生成的 AlertDialog 所在的
 Activity , 也就是需求中说的 B ， A 和 C 中没有特殊处理，所以A和C不再给出。
```
/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/4/10:下午8:23
 * <p>
 * 说明：自定义AlertDialog的View,点击确定按钮后跳转到下一个页面，并关闭当前页。
 * <p>
 * 需要注意的是，关闭当前页之前需要关闭Alertdialog，否则会导致actvity溢出。溢出的错误信息如下： 并且在关闭dialog的时候，只有通过show()
 * 方法获取到的dialog对象才可以调用dismiss并实现关闭；用create方法得到的对象则不行
 * <p>
 * 溢出的错误信息如下：（ERROR级别） android.view.WindowLeaked: Activity com.cnpeng.cnpeng_demos2017_01.b_05_alertDialog_dismiss
 * .DissmissAlertDialog
 * has leaked window com.android.internal.policy.impl.PhoneWindow$DecorView{1a1d1b07 V.E..... R......D 0,0-684,238} that
 * was originally added here
 */

public class DissmissAlertDialog extends AppCompatActivity implements View.OnClickListener {

    private AlertDialog dialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_dismiss_alertdialog);

        Button bt_showDialog = (Button) findViewById(R.id.bt_showAlertDialog);
        bt_showDialog.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                showAlertDialog();
            }
        });
    }

    private void showAlertDialog() {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);
        builder.setCancelable(false);
        View customDialogView = getLayoutInflater().inflate(R.layout.custom_alertdialog, null);
        builder.setView(customDialogView);
        //        dialog = builder.create();    //这种还是会报溢出
        //        builder.show();
        dialog = builder.show();    //这样则不会溢出

        Button bt_left = (Button) customDialogView.findViewById(R.id.bt_left);
        Button bt_right = (Button) customDialogView.findViewById(R.id.bt_right);

        bt_left.setOnClickListener(this);
        bt_right.setOnClickListener(this);

    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.bt_left:  //关闭当前页
                if (dialog.isShowing()) {
                    dialog.dismiss();
                }
                finish();
                break;
            case R.id.bt_right: //关闭当前页面，并跳转到其他页面
                CommonUtils.mStartActivity(this, SuspendAndListViewActivity2.class);
                if (dialog.isShowing()) {
                    dialog.dismiss();
                }
                finish();
                break;
        }
    }
}
```
AlertDialog的view。
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:padding="@dimen/dp10"
        android:text="随便写的一个布局界面"/>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">
        <Button
            android:id="@+id/bt_left"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="左侧按钮"/>
        <Button
            android:id="@+id/bt_right"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="右侧按钮"/>
    </LinearLayout>

</LinearLayout>
```
##5、总结
* （1）、AlertDialog 中提供的 positiveButton 和 Nagetivebutton 
内部已经实现了 销毁 AlertDialog 的 dismiss(  ) 方法，所以在调用这两个按钮的时候，不需要手动 dismiss( ) 
* （2）、自己写的 button 中 需要手动创建 AlertDialog 对象去调用 dismiss( ) 方法从而销毁 AlertDialog。但是，创建AlertDialog对象的时候需要使用 show( ) 方法创建，而不能用 create( ) 创建
* （3）、想不明白啊，为啥用 create( ) 方法创建的 对象达不到销毁的目的呢？？其实查看 show( ) 内部也是调用 create( ) 创建对象并返回。。。真是没想明白。

##6、附件
代码地址：https://github.com/CnPeng/CrazyAndroid.git
该文章中的内容  对应其中 的 b_05.

>差不多快一个半月没写东西了，为啥呢？忙吗？其实也不忙；不忙吗？其实每周都有新需求，都有各种需要改的bug。

>归根到底，其实是这一个多月变的懒了，书也读的少了，代码也敲的少了。年初的时候，给自己的目标是把手上关于安卓和java的四本书通读一遍同时把其中的所有代码都敲一遍，然而，现在17年都过去三分之一了，我的书才读了一本中的四分之一，离目标还差的好远好远。。。现在的状态真的不好，还是抓紧改变吧。
