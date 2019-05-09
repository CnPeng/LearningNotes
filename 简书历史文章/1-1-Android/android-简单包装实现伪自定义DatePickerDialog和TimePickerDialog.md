之所以写这个东西，是因为在我们的设计中需要给 日期选择器加一个标题，一开始直接使用  原生的 DatePickerAlertDialog ，因为他继承自 AlertDialog ,所以就直接 调用了 setTitle 方法，然而，丑的那叫一塌糊涂啊。所以，最后只好自己简单包装一下。

内容简单，不做过多解释，直接上代码。

## 1 伪自定义的DatePickerDialog

效果图：
![S70904-15160552.jpg](http://upload-images.jianshu.io/upload_images/2551993-5678a36841a25fe7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

DatePickerDialog代码：
```
/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/7/25:上午11:58
 * <p>
 * 说明：对 DatePicker 和 AlertDialog 包装生成的自定义 日期 选择器dialog 
 */

public class CustomDatePickerAlertDialog {

    private final Context                          context;
    private final AlertDialog                      dialog;             //dialog对象
    private       View                             dialogView;         //dialogView 
    private       CustomDatepickerDialogAntBinding dialogBinding;
    private       int                              year;
    private       int                              month;
    private       int                              day;

    /**
     * @param context     上下文
     * @param year        年份，具体年份   （此处三个日期 同 Calendar 中取出的值）
     * @param monthOfYear 月份，取值 0-11
     * @param dayOfMonth  天，取值1-31
     */
    public CustomDatePickerAlertDialog(Context context, int year, int monthOfYear, int dayOfMonth) {
        this.context = context;
        AlertDialog.Builder builder = new AlertDialog.Builder(context);
        dialog = builder.create();

        initDate(year, monthOfYear, dayOfMonth);
        initDialogView();
    }

    /**
     * 初始化日期，如果外部在初始化传递的都是0 ，则使用该日期去初始化DatePicker
     *
     * @param year        年份，具体年份   （此处三个日期 同 Calendar 中取出的值）
     * @param monthOfYear 月份，取值 0-11
     * @param dayOfMonth  天，取值1-31
     */
    private void initDate(int year, int monthOfYear, int dayOfMonth) {
        if (year == 0 || dayOfMonth == 0) {
            Calendar calendar = Calendar.getInstance();
            year = calendar.get(Calendar.YEAR);
            month = calendar.get(Calendar.MONTH);   //取值 0-11
            day = calendar.get(Calendar.DAY_OF_MONTH);
        } else {
            this.year = year;
            month = monthOfYear;
            day = dayOfMonth;
        }
        LogUtils.e("初始化时的年月日是：", year + "/" + (month + 1) + "/" + day);
    }

    private void initDialogView() {
        LayoutInflater inflater = LayoutInflater.from(context);
        dialogBinding = DataBindingUtil.inflate(inflater, R.layout.custom_datepicker_dialog_ant, null, false);
        dialogView = dialogBinding.getRoot();
        dialog.setView(dialogView);     //设置view
        setLayoutByPx(0, 0);          //设置宽高
        if (Build.VERSION.SDK_INT >= 21) {  //21之前设置背景的时候依旧会有白色边框
            setBackGroundDrawableResource(0);
        }
        setDimAmount(0.15f);

        initDatePicker();
    }

    private void initDatePicker() {

        DatePicker datePicker = dialogBinding.datePickerCustomDatePickerDialog;

        datePicker.init(year, month, day, new DatePicker.OnDateChangedListener() {
            @Override
            public void onDateChanged(DatePicker view, int selectedyear, int monthOfYear, int dayOfMonth) {
                year = selectedyear;
                month = monthOfYear;
                day = dayOfMonth;
            }
        });
    }

    /**
     * 展示dialog
     */
    public void show() {
        if (dialog != null && !dialog.isShowing()) {
            dialog.show();
        }
    }

    /**
     * 关闭dialog
     */
    public void dismissDialog() {
        if (dialog != null && dialog.isShowing()) {
            dialog.dismiss();
        }
    }

    /**
     * 返回dialog的view对象
     */
    public View getDialogView() {
        return dialogView;
    }

    /**
     * 返回dialog对象
     */
    public Dialog getDialogObj() {
        return dialog;
    }

    /**
     * 设置dialog的宽高信息,单位px
     * 注意：不推荐用该方法，由于标注是按照IOS标准标的像素，如果直接传递像素，在安卓设备上会产生较严重的偏差
     */
    public void setLayoutByPx(final int width, final int height) {
        final Window window = dialog.getWindow();
        if (null != window) {
            Display display = window.getWindowManager().getDefaultDisplay();
            DisplayMetrics metrics = new DisplayMetrics();
            display.getMetrics(metrics);
            final int windowWidth = metrics.widthPixels;

            if (height != 0) {      //如果不为0，则指定LL的高度填充父窗体，也就是填满指定的高度值,避免出现内容小于指定高度时，内容底部显示白色块
                LinearLayout ll_root_dialog = dialogBinding.llRootAntDialog;
                ViewGroup.LayoutParams layoutParams = (ViewGroup.LayoutParams) ll_root_dialog.getLayoutParams();
                layoutParams.height = ViewGroup.LayoutParams.MATCH_PARENT;
                ll_root_dialog.setLayoutParams(layoutParams);
                ll_root_dialog.setGravity(Gravity.CENTER);
            }

            dialogView.addOnLayoutChangeListener(new View.OnLayoutChangeListener() {
                @Override
                public void onLayoutChange(View v, int left, int top, int right, int bottom, int oldLeft, int oldTop,
                                           int oldRight, int oldBottom) {
                    int finalWidth = width <= 0 ? (int) (windowWidth * 0.76) : width;
                    int finalHeight = height <= 0 ? FrameLayout.LayoutParams.WRAP_CONTENT : height;
                    //                    LogUtils.e("宽高", finalWidth + "/" + finalHeight);
                    window.setLayout(finalWidth, finalHeight);
                    window.setGravity(Gravity.CENTER);

                }
            });
        }
    }

    /**
     * 设置dialog的宽高信息，单位dp
     * 推荐使用这种，先将标注图上的px 按照2：1 转成dp,然后调用该方法
     */
    public void setLayoutByDp(final int width, final int height) {
        final Window window = dialog.getWindow();
        if (null != window) {
            Display display = window.getWindowManager().getDefaultDisplay();
            DisplayMetrics metrics = new DisplayMetrics();
            display.getMetrics(metrics);
            final int windowWidth = metrics.widthPixels;

            if (height != 0) {      //如果不为0，则指定LL的高度填充父窗体，也就是填满指定的高度值,避免出现内容小于指定高度时，内容底部显示白色块
                LinearLayout ll_root_dialog = dialogBinding.llRootAntDialog;
                ViewGroup.LayoutParams layoutParams = (ViewGroup.LayoutParams) ll_root_dialog.getLayoutParams();
                layoutParams.height = ViewGroup.LayoutParams.MATCH_PARENT;
                ll_root_dialog.setLayoutParams(layoutParams);
                ll_root_dialog.setGravity(Gravity.CENTER);
            }

            dialogView.addOnLayoutChangeListener(new View.OnLayoutChangeListener() {
                @Override
                public void onLayoutChange(View v, int left, int top, int right, int bottom, int oldLeft, int oldTop,
                                           int oldRight, int oldBottom) {
                    DisplayMetrics metrics = context.getResources().getDisplayMetrics();
                    int widthPx = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, width, metrics);
                    int heightPx = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, height, metrics);
                    int finalWidth = widthPx <= 0 ? (int) (windowWidth * 0.76) : widthPx;
                    int finalHeight = heightPx <= 0 ? FrameLayout.LayoutParams.WRAP_CONTENT : heightPx;
                    //                    LogUtils.e("宽高", widthPx + "/" + heightPx);
                    window.setLayout(finalWidth, finalHeight);
                }
            });
        }
    }

    /**
     * 设置dialog的宽高信息，无单位
     * 也推荐使用这种，按照比率设置宽高
     *
     * @param widthRate  内容区域占屏幕宽度的多少，取值（0，1]
     * @param heightRate 内容区域占屏幕高度的多少,取值 （0，1]
     */
    public void setLayoutByRate(final float widthRate, final float heightRate) {
        final Window window = dialog.getWindow();
        if (null != window) {
            Display display = window.getWindowManager().getDefaultDisplay();
            DisplayMetrics metrics = new DisplayMetrics();
            display.getMetrics(metrics);
            final int windowWidth = metrics.widthPixels;
            final int windowHeight = metrics.heightPixels;

            if (heightRate != 0) {      //如果不为0，则指定LL的高度填充父窗体，也就是填满指定的高度值,避免出现内容小于指定高度时，内容底部显示白色块
                LinearLayout ll_root_dialog = dialogBinding.llRootAntDialog;
                ViewGroup.LayoutParams layoutParams = (ViewGroup.LayoutParams) ll_root_dialog.getLayoutParams();
                layoutParams.height = ViewGroup.LayoutParams.MATCH_PARENT;
                ll_root_dialog.setLayoutParams(layoutParams);
                ll_root_dialog.setGravity(Gravity.CENTER);
            }

            dialogView.addOnLayoutChangeListener(new View.OnLayoutChangeListener() {
                @Override
                public void onLayoutChange(View v, int left, int top, int right, int bottom, int oldLeft, int oldTop,
                                           int oldRight, int oldBottom) {
                    int finalWidth = widthRate <= 0 ? (int) (windowWidth * 0.76) : (int) (windowWidth * widthRate);
                    int finalHeight = heightRate <= 0 ? FrameLayout.LayoutParams.WRAP_CONTENT : (int) (windowHeight *
                            heightRate);
                    //                    LogUtils.e("宽高", finalWidth + "/" + finalHeight);
                    window.setLayout(finalWidth, finalHeight);
                }
            });
        }
    }

    /**
     * 设置dialog的背景--传入资源id
     */
    public void setBackGroundDrawableResource(int drawableResId) {
        Window window = dialog.getWindow();
        if (null != window) {
            if (0 == drawableResId) {
                drawableResId = R.drawable.shape_bk_rect_cornor_white;
            }
            window.setBackgroundDrawableResource(drawableResId);
        }
    }

    /**
     * 设置背景图--传入drawable对象
     */
    public void setBackGroundDrawable(Drawable drawable) {
        Window window = dialog.getWindow();
        if (null != window) {
            if (null == drawable) {
                drawable = context.getResources().getDrawable(R.drawable.shape_bk_rect_cornor_white);
            }
            window.setBackgroundDrawable(drawable);
        }
    }

    /**
     * 设置背景图--根据传入的color值生成对应填充色的圆角背景图
     *
     * @param colorInt      色值
     * @param conorRadiusPx 圆角半径,单位PX
     */
    public void setBackGroundDrawable(
            @ColorInt
                    int colorInt, int conorRadiusPx) {
        GradientDrawable drawable = new GradientDrawable();
        drawable.setColor(colorInt);
        drawable.setCornerRadius(conorRadiusPx);
        drawable.setShape(GradientDrawable.RECTANGLE);

        Window window = dialog.getWindow();
        if (null != window) {
            window.setBackgroundDrawable(drawable);
        }

    }

    /**
     * 设置确定按钮的问题及其点击事件,
     * 传入的事件监听为null时，会关闭dialog
     */
    public void setPositiveButton(String des, final AntDatePickerDialogClickListener clickListener) {
        dialogBinding.setIsConfirmBtShow(true);
        dialogBinding.tvConfirmBT.setText(des);
        dialogBinding.tvConfirmBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, year, month + 1, day);
                }
                dismissDialog();
            }
        });
    }

    public void setPositiveButton(int strResId, final AntDatePickerDialogClickListener clickListener) {
        dialogBinding.setIsConfirmBtShow(true);
        dialogBinding.tvConfirmBT.setText(context.getResources().getString(strResId));
        dialogBinding.tvConfirmBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, year, month + 1, day);
                }
                dismissDialog();
            }
        });
    }

    /**
     * 取消按钮的点击事件
     */
    public void setNegativeButton(String des, final AntDatePickerDialogClickListener clickListener) {
        dialogBinding.setIsCancleBtShow(true);
        dialogBinding.tvCancleBT.setText(des);
        dialogBinding.tvCancleBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, year, month + 1, day);
                }
                dismissDialog();
            }
        });
    }

    public void setNegativeButton(int strResId, final AntDatePickerDialogClickListener clickListener) {
        dialogBinding.setIsCancleBtShow(true);
        dialogBinding.tvCancleBT.setText(context.getResources().getString(strResId));
        dialogBinding.tvCancleBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, year, month + 1, day);
                }
                dismissDialog();
            }
        });
    }

    /**
     * 跳过按钮的点击事件
     */
    public void setSkipButton(String des, final AntDatePickerDialogClickListener clickListener) {
        dialogBinding.setIsSkipBtShow(true);
        dialogBinding.tvSkipBT.setText(des);
        dialogBinding.tvSkipBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, year, month + 1, day);
                }
                dismissDialog();
            }
        });
    }

    public void setSkipButton(int strResId, final AntDatePickerDialogClickListener clickListener) {
        dialogBinding.setIsSkipBtShow(true);
        dialogBinding.tvSkipBT.setText(context.getResources().getString(strResId));
        dialogBinding.tvSkipBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, year, month + 1, day);
                }
                dismissDialog();
            }
        });
    }

    /**
     * 设置确认按钮的文字颜色
     */
    public void setPositiveButtonTextColor(
            @ColorInt
                    int color) {
        dialogBinding.tvConfirmBT.setTextColor(color);
    }

    public void setPositiveButtonTextColor(ColorStateList colorStateList) {
        dialogBinding.tvConfirmBT.setTextColor(colorStateList);
    }

    /**
     * 设置取消按钮的字体颜色
     */
    public void setNegativeButtonTextColor(
            @ColorInt
                    int color) {
        dialogBinding.tvCancleBT.setTextColor(color);
    }

    public void setNegativeButtonTextColor(ColorStateList colorStateList) {
        dialogBinding.tvCancleBT.setTextColor(colorStateList);
    }

    /**
     * 设置跳过按钮的字体颜色
     */
    public void setSkipButtonTextColor(
            @ColorInt
                    int color) {
        dialogBinding.tvSkipBT.setTextColor(color);
    }

    public void setSkipButtonTextColor(ColorStateList colorStateList) {
        dialogBinding.tvSkipBT.setTextColor(colorStateList);
    }

    /**
     * 设置标题
     */
    public void setTitle(String title) {
        dialogBinding.setIsTitleShow(true);
        dialogBinding.tvTitle.setText(title);
    }

    public void setTitle(int strResId) {
        dialogBinding.setIsTitleShow(true);
        dialogBinding.tvTitle.setText(context.getResources().getString(strResId));
    }

    /**
     * 设置副标题
     */
    public void setSubTitle(String title) {
        dialogBinding.setIsSubTitleShow(true);
        dialogBinding.tvSubTitle.setText(title);
    }

    public void setSubTitle(int strResId) {
        dialogBinding.setIsSubTitleShow(true);
        dialogBinding.tvSubTitle.setText(context.getResources().getString(strResId));
    }

    /**
     * 设置副标题的点击事件
     */
    public void setSubTitleClickListener(final AntDatePickerDialogClickListener clickListener) {
        dialogBinding.tvSubTitle.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    dismissDialog();
                    clickListener.onClick(dialogBinding.tvSubTitle, year, month, day);
                }
            }
        });
    }


    /**
     * 更改主标题文字的大小
     */
    public void setTitleTextSize(int sizeSP) {
        if (sizeSP <= 0) {
            sizeSP = 14;
        }
        dialogBinding.tvTitle.setTextSize(sizeSP);
    }

    /**
     * 修改Dialog阴影区域的灰度百分比
     * <p>
     * 取值 0-1.
     */
    public void setDimAmount(float rate) {
        Window window = dialog.getWindow();
        if (null != window) {
            if (rate < 0) {
                rate = 0;
            } else if (rate > 1) {
                rate = 1;
            }
            window.setDimAmount(rate);
        }
    }

    /**
     * 点击非内容区域是否可以关闭
     */
    public void setCancelable(boolean bool) {
        dialog.setCancelable(bool);
    }

    /**
     * 对外暴露点击事件的自定义接口
     */
    public interface AntDatePickerDialogClickListener {
        void onClick(View view, int selectedYear, int selectedMonth, int selectedDay);
    }
}

```
布局文件：
```
<?xml version="1.0" encoding="utf-8"?>
<layout>
    <data>
        <import type="android.view.View"/>
        <variable name="isTitleShow" type="Boolean"/>
        <variable name="isSubTitleShow" type="Boolean"/>
        <variable name="isCancleBtShow" type="Boolean"/>
        <variable name="isConfirmBtShow" type="Boolean"/>
        <variable name="isSkipBtShow" type="Boolean"/>

    </data>

    <!--外面这层RL的实际作用是，让内部的LL布局参数生效，因为在填充布局时parent传递的null，所以被填充布局的第一层布局参数不生效，从第二层起才生效-->
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

        <LinearLayout
            android:id="@+id/ll_root_antDialog"
            xmlns:android="http://schemas.android.com/apk/res/android"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:divider="@drawable/shape_divider_05dp"
            android:gravity="center"
            android:orientation="vertical"
            android:showDividers="middle">

            <!--标题区域-->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="#ffc000"
                android:orientation="vertical">

                <!--主标题-->
                <TextView
                    android:id="@+id/tv_title"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:ellipsize="end"
                    android:gravity="center"
                    android:maxLines="1"
                    android:paddingBottom="8dp"
                    android:paddingLeft="@dimen/dp20"
                    android:paddingRight="@dimen/dp20"
                    android:paddingTop="8dp"
                    android:text="标题"
                    android:textColor="#fff"
                    android:textSize="16sp"
                    android:visibility="@{isTitleShow?View.VISIBLE:View.GONE}"/>

                <!--副标题-->
                <TextView
                    android:id="@+id/tv_subTitle"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_marginBottom="4dp"
                    android:layout_marginLeft="@dimen/dp20"
                    android:layout_marginRight="@dimen/dp20"
                    android:layout_marginTop="-5dp"
                    android:background="@drawable/layerlist_bottomline_1dp_white"
                    android:ellipsize="end"
                    android:gravity="center"
                    android:maxLines="1"
                    android:paddingBottom="@dimen/dp2"
                    android:paddingLeft="@dimen/dp3"
                    android:paddingRight="@dimen/dp3"
                    android:text="副标题副题副副标题副标题副标题副标题副标题副标题副"
                    android:textColor="#fff"
                    android:textSize="10sp"
                    android:visibility="@{isSubTitleShow?View.VISIBLE:View.GONE}"/>
            </LinearLayout>

            <DatePicker
                android:id="@+id/datePicker_customDatePickerDialog"
                style="@android:style/Widget.DatePicker"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:calendarViewShown="false"
                android:datePickerMode="spinner"/>

            <!--底部按钮-->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:divider="@drawable/shape_divider_05dp"
                android:orientation="horizontal"
                android:showDividers="middle"
                android:visibility="@{isConfirmBtShow||isCancleBtShow?View.VISIBLE:View.GONE}">

                <TextView
                    android:id="@+id/tv_cancleBT"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:ellipsize="end"
                    android:gravity="center"
                    android:maxLines="1"
                    android:paddingBottom="@dimen/dp10"
                    android:paddingTop="@dimen/dp10"
                    android:text="取消"
                    android:textColor="@color/c_666666"
                    android:textSize="16sp"
                    android:visibility="@{isCancleBtShow?View.VISIBLE:View.GONE}"/>

                <TextView
                    android:id="@+id/tv_skipBT"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:ellipsize="end"
                    android:gravity="center"
                    android:maxLines="1"
                    android:paddingBottom="@dimen/dp10"
                    android:paddingTop="@dimen/dp10"
                    android:text="跳过"
                    android:textColor="@color/c_666666"
                    android:textSize="16sp"
                    android:visibility="@{isSkipBtShow?View.VISIBLE:View.GONE}"/>

                <TextView
                    android:id="@+id/tv_confirmBT"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:ellipsize="end"
                    android:gravity="center"
                    android:maxLines="1"
                    android:paddingBottom="@dimen/dp10"
                    android:paddingTop="@dimen/dp10"
                    android:text="确定"
                    android:textColor="#ffc000"
                    android:textSize="16sp"
                    android:visibility="@{isConfirmBtShow?View.VISIBLE:View.GONE}"/>

            </LinearLayout>

        </LinearLayout>
    </RelativeLayout>

</layout>

```


代码中调用：

```
    /**
     * 展示时间选择器=
     * <p>
     */
    private void showDateAndTimePickerDialog() {
        Calendar calendar = Calendar.getInstance();
        int year = calendar.get(Calendar.YEAR);
        int month = calendar.get(Calendar.MONTH);
        int day = calendar.get(Calendar.DAY_OF_MONTH);
        CustomDatePickerAlertDialog dialog = new CustomDatePickerAlertDialog(context, year, month, day);
        dialog.setPositiveButton("确定", new CustomDatePickerAlertDialog.AntDatePickerDialogClickListener() {
            @Override
            public void onClick(View view, int year, int month, int day) {
                LogUtils.e("你设置的日期是：", year + "/" + month + "/" + day);
                showTimePickerDialog(year, month, day);
            }
        });
        dialog.setNegativeButton("取消", null);
        dialog.setSkipButton("跳过", new CustomDatePickerAlertDialog.AntDatePickerDialogClickListener() {
            @Override
            public void onClick(View view, int selectedYear, int selectedMonth, int selectedDay) {
                sendLateTimeToServer(0, 0, 0, 0, 0);
                goToSignSituationAct();
            }
        });
        dialog.setTitle("请设置迟到判定时间——年月日");
        dialog.setCancelable(false);
        dialog.show();
    }
```
## 2 伪自定义的TimePickerDialog
效果图：
![S70904-15164218.jpg](http://upload-images.jianshu.io/upload_images/2551993-9e686cb13e2614f5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

TimePickerDialog 代码：

```
/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/7/25:上午11:58
 * <p>
 * 说明：对 TimePicker 和 AlertDialog 包装生成的自定义 时间选择器dialog 
 */

public class CustomTimePickerAlertDialog {

    private final Context                          context;
    private final AlertDialog                      dialog;             //dialog对象
    private       View                             dialogView;         //dialogView 
    private       CustomTimepickerDialogAntBinding dialogBinding;
    private       int                              mHour;
    private       int                              mMinute;

    /**
     * 
     * @param context 上下文
     * @param hour     小时，取值 0-23
     * @param minute   分钟，取值 0-59
     */
    public CustomTimePickerAlertDialog(Context context, int hour, int minute) {
        this.context = context;
        AlertDialog.Builder builder = new AlertDialog.Builder(context);
        dialog = builder.create();

        initDate(hour, minute);
        initDialogView();
    }

    /**
     * 初始化日期，如果外部在初始化传递的都是0 ，则使用该日期去初始化DatePicker
     *
     * @param hour   小时
     * @param minute 分
     */
    private void initDate(int hour, int minute) {
        if (hour == -1 || minute == -1) {
            Calendar calendar = Calendar.getInstance();
            mHour = calendar.get(Calendar.HOUR_OF_DAY);
            mMinute = calendar.get(Calendar.MINUTE);
        } else {
            mHour = hour;
            mMinute = minute;
        }
        LogUtils.e("初始化时的时间是：", mHour + "/" + mMinute);
    }

    private void initDialogView() {
        LayoutInflater inflater = LayoutInflater.from(context);
        dialogBinding = DataBindingUtil.inflate(inflater, R.layout.custom_timepicker_dialog_ant, null, false);
        dialogView = dialogBinding.getRoot();
        dialog.setView(dialogView);     //设置view
        setLayoutByPx(0, 0);          //设置宽高
        if (Build.VERSION.SDK_INT >= 21) {
            setBackGroundDrawableResource(0);
        }
        setDimAmount(0.15f);

        initTimePicker();
    }

    private void initTimePicker() {

        TimePicker mTimePicker = dialogBinding.timePickerCustomTimePickerDialog;
        mTimePicker.setIs24HourView(true);
        mTimePicker.setCurrentHour(mHour);     //0-23
        mTimePicker.setCurrentMinute(mMinute); //0-59
        mTimePicker.setOnTimeChangedListener(new TimePicker.OnTimeChangedListener() {
            @Override
            public void onTimeChanged(TimePicker view, int hourOfDay, int minute) {
                mHour = hourOfDay;
                mMinute = minute;
                LogUtils.e("设置的时间是：", hourOfDay + "/" + minute);
            }
        });
    }

    /**
     * 展示dialog
     */
    public void show() {
        if (dialog != null && !dialog.isShowing()) {
            dialog.show();
        }
    }

    /**
     * 关闭dialog
     */
    public void dismissDialog() {
        if (dialog != null && dialog.isShowing()) {
            dialog.dismiss();
        }
    }

    /**
     * 返回dialog的view对象
     */
    public View getDialogView() {
        return dialogView;
    }

    /**
     * 返回dialog对象
     */
    public Dialog getDialogObj() {
        return dialog;
    }

    /**
     * 设置dialog的宽高信息,单位px
     * 注意：不推荐用该方法，由于标注是按照IOS标准标的像素，如果直接传递像素，在安卓设备上会产生较严重的偏差
     */
    public void setLayoutByPx(final int width, final int height) {
        final Window window = dialog.getWindow();
        if (null != window) {
            Display display = window.getWindowManager().getDefaultDisplay();
            DisplayMetrics metrics = new DisplayMetrics();
            display.getMetrics(metrics);
            final int windowWidth = metrics.widthPixels;

            if (height != 0) {      //如果不为0，则指定LL的高度填充父窗体，也就是填满指定的高度值,避免出现内容小于指定高度时，内容底部显示白色块
                LinearLayout ll_root_dialog = dialogBinding.llRootAntDialog;
                ViewGroup.LayoutParams layoutParams = (ViewGroup.LayoutParams) ll_root_dialog.getLayoutParams();
                layoutParams.height = ViewGroup.LayoutParams.MATCH_PARENT;
                ll_root_dialog.setLayoutParams(layoutParams);
                ll_root_dialog.setGravity(Gravity.CENTER);
            }

            dialogView.addOnLayoutChangeListener(new View.OnLayoutChangeListener() {
                @Override
                public void onLayoutChange(View v, int left, int top, int right, int bottom, int oldLeft, int oldTop,
                                           int oldRight, int oldBottom) {
                    int finalWidth = width <= 0 ? (int) (windowWidth * 0.76) : width;
                    int finalHeight = height <= 0 ? FrameLayout.LayoutParams.WRAP_CONTENT : height;
                    //                    LogUtils.e("宽高", finalWidth + "/" + finalHeight);
                    window.setLayout(finalWidth, finalHeight);
                    window.setGravity(Gravity.CENTER);

                }
            });
        }
    }

    /**
     * 设置dialog的宽高信息，单位dp
     * 推荐使用这种，先将标注图上的px 按照2：1 转成dp,然后调用该方法
     */
    public void setLayoutByDp(final int width, final int height) {
        final Window window = dialog.getWindow();
        if (null != window) {
            Display display = window.getWindowManager().getDefaultDisplay();
            DisplayMetrics metrics = new DisplayMetrics();
            display.getMetrics(metrics);
            final int windowWidth = metrics.widthPixels;

            if (height != 0) {      //如果不为0，则指定LL的高度填充父窗体，也就是填满指定的高度值,避免出现内容小于指定高度时，内容底部显示白色块
                LinearLayout ll_root_dialog = dialogBinding.llRootAntDialog;
                ViewGroup.LayoutParams layoutParams = (ViewGroup.LayoutParams) ll_root_dialog.getLayoutParams();
                layoutParams.height = ViewGroup.LayoutParams.MATCH_PARENT;
                ll_root_dialog.setLayoutParams(layoutParams);
                ll_root_dialog.setGravity(Gravity.CENTER);
            }

            dialogView.addOnLayoutChangeListener(new View.OnLayoutChangeListener() {
                @Override
                public void onLayoutChange(View v, int left, int top, int right, int bottom, int oldLeft, int oldTop,
                                           int oldRight, int oldBottom) {
                    DisplayMetrics metrics = context.getResources().getDisplayMetrics();
                    int widthPx = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, width, metrics);
                    int heightPx = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, height, metrics);
                    int finalWidth = widthPx <= 0 ? (int) (windowWidth * 0.76) : widthPx;
                    int finalHeight = heightPx <= 0 ? FrameLayout.LayoutParams.WRAP_CONTENT : heightPx;
                    //                    LogUtils.e("宽高", widthPx + "/" + heightPx);
                    window.setLayout(finalWidth, finalHeight);
                }
            });
        }
    }

    /**
     * 设置dialog的宽高信息，无单位
     * 也推荐使用这种，按照比率设置宽高
     *
     * @param widthRate  内容区域占屏幕宽度的多少，取值（0，1]
     * @param heightRate 内容区域占屏幕高度的多少,取值 （0，1]
     */
    public void setLayoutByRate(final float widthRate, final float heightRate) {
        final Window window = dialog.getWindow();
        if (null != window) {
            Display display = window.getWindowManager().getDefaultDisplay();
            DisplayMetrics metrics = new DisplayMetrics();
            display.getMetrics(metrics);
            final int windowWidth = metrics.widthPixels;
            final int windowHeight = metrics.heightPixels;

            if (heightRate != 0) {      //如果不为0，则指定LL的高度填充父窗体，也就是填满指定的高度值,避免出现内容小于指定高度时，内容底部显示白色块
                LinearLayout ll_root_dialog = dialogBinding.llRootAntDialog;
                ViewGroup.LayoutParams layoutParams = (ViewGroup.LayoutParams) ll_root_dialog.getLayoutParams();
                layoutParams.height = ViewGroup.LayoutParams.MATCH_PARENT;
                ll_root_dialog.setLayoutParams(layoutParams);
                ll_root_dialog.setGravity(Gravity.CENTER);
            }

            dialogView.addOnLayoutChangeListener(new View.OnLayoutChangeListener() {
                @Override
                public void onLayoutChange(View v, int left, int top, int right, int bottom, int oldLeft, int oldTop,
                                           int oldRight, int oldBottom) {
                    int finalWidth = widthRate <= 0 ? (int) (windowWidth * 0.76) : (int) (windowWidth * widthRate);
                    int finalHeight = heightRate <= 0 ? FrameLayout.LayoutParams.WRAP_CONTENT : (int) (windowHeight *
                            heightRate);
                    //                    LogUtils.e("宽高", finalWidth + "/" + finalHeight);
                    window.setLayout(finalWidth, finalHeight);
                }
            });
        }
    }

    /**
     * 设置dialog的背景--传入资源id
     */
    public void setBackGroundDrawableResource(int drawableResId) {
        Window window = dialog.getWindow();
        if (null != window) {
            if (0 == drawableResId) {
                drawableResId = R.drawable.shape_bk_rect_cornor_white;
            }
            window.setBackgroundDrawableResource(drawableResId);
        }
    }

    /**
     * 设置背景图--传入drawable对象
     */
    public void setBackGroundDrawable(Drawable drawable) {
        Window window = dialog.getWindow();
        if (null != window) {
            if (null == drawable) {
                drawable = context.getResources().getDrawable(R.drawable.shape_bk_rect_cornor_white);
            }
            window.setBackgroundDrawable(drawable);
        }
    }

    /**
     * 设置背景图--根据传入的color值生成对应填充色的圆角背景图
     *
     * @param colorInt      色值
     * @param conorRadiusPx 圆角半径,单位PX
     */
    public void setBackGroundDrawable(
            @ColorInt
                    int colorInt, int conorRadiusPx) {
        GradientDrawable drawable = new GradientDrawable();
        drawable.setColor(colorInt);
        drawable.setCornerRadius(conorRadiusPx);
        drawable.setShape(GradientDrawable.RECTANGLE);

        Window window = dialog.getWindow();
        if (null != window) {
            window.setBackgroundDrawable(drawable);
        }

    }

    /**
     * 设置确定按钮的问题及其点击事件,
     * 传入的事件监听为null时，会关闭dialog
     */
    public void setPositiveButton(String des, final AntTimePickerDialogClickListener clickListener) {
        dialogBinding.setIsConfirmBtShow(true);
        dialogBinding.tvConfirmBT.setText(des);
        dialogBinding.tvConfirmBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, mHour, mMinute);
                }
                dismissDialog();
            }
        });
    }

    public void setPositiveButton(int strResId, final AntTimePickerDialogClickListener clickListener) {
        dialogBinding.setIsConfirmBtShow(true);
        dialogBinding.tvConfirmBT.setText(context.getResources().getString(strResId));
        dialogBinding.tvConfirmBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, mHour, mMinute);
                }
                dismissDialog();
            }
        });
    }

    /**
     * 取消按钮的点击事件
     */
    public void setNegativeButton(String des, final AntTimePickerDialogClickListener clickListener) {
        dialogBinding.setIsCancleBtShow(true);
        dialogBinding.tvCancleBT.setText(des);
        dialogBinding.tvCancleBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, mHour,mMinute);
                }
                dismissDialog();
            }
        });
    }

    public void setNegativeButton(int strResId, final AntTimePickerDialogClickListener clickListener) {
        dialogBinding.setIsCancleBtShow(true);
        dialogBinding.tvCancleBT.setText(context.getResources().getString(strResId));
        dialogBinding.tvCancleBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, mHour, mMinute);
                }
                dismissDialog();
            }
        });
    }

    /**
     * 跳过按钮的点击事件
     */
    public void setSkipButton(String des, final AntTimePickerDialogClickListener clickListener) {
        dialogBinding.setIsSkipBtShow(true);
        dialogBinding.tvSkipBT.setText(des);
        dialogBinding.tvSkipBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v, mHour, mMinute);
                }
                dismissDialog();
            }
        });
    }

    public void setSkipButton(int strResId, final AntTimePickerDialogClickListener clickListener) {
        dialogBinding.setIsSkipBtShow(true);
        dialogBinding.tvSkipBT.setText(context.getResources().getString(strResId));
        dialogBinding.tvSkipBT.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    clickListener.onClick(v,mHour, mMinute);
                }
                dismissDialog();
            }
        });
    }

    /**
     * 设置确认按钮的文字颜色
     */
    public void setPositiveButtonTextColor(
            @ColorInt
                    int color) {
        dialogBinding.tvConfirmBT.setTextColor(color);
    }

    public void setPositiveButtonTextColor(ColorStateList colorStateList) {
        dialogBinding.tvConfirmBT.setTextColor(colorStateList);
    }

    /**
     * 设置取消按钮的字体颜色
     */
    public void setNegativeButtonTextColor(
            @ColorInt
                    int color) {
        dialogBinding.tvCancleBT.setTextColor(color);
    }

    public void setNegativeButtonTextColor(ColorStateList colorStateList) {
        dialogBinding.tvCancleBT.setTextColor(colorStateList);
    }

    /**
     * 设置跳过按钮的字体颜色
     */
    public void setSkipButtonTextColor(
            @ColorInt
                    int color) {
        dialogBinding.tvSkipBT.setTextColor(color);
    }

    public void setSkipButtonTextColor(ColorStateList colorStateList) {
        dialogBinding.tvSkipBT.setTextColor(colorStateList);
    }

    /**
     * 设置标题
     */
    public void setTitle(String title) {
        dialogBinding.setIsTitleShow(true);
        dialogBinding.tvTitle.setText(title);
    }

    public void setTitle(int strResId) {
        dialogBinding.setIsTitleShow(true);
        dialogBinding.tvTitle.setText(context.getResources().getString(strResId));
    }

    /**
     * 设置副标题
     */
    public void setSubTitle(String title) {
        dialogBinding.setIsSubTitleShow(true);
        dialogBinding.tvSubTitle.setText(title);
    }

    public void setSubTitle(int strResId) {
        dialogBinding.setIsSubTitleShow(true);
        dialogBinding.tvSubTitle.setText(context.getResources().getString(strResId));
    }

    /**
     * 设置副标题的点击事件
     */
    public void setSubTitleClickListener(final AntTimePickerDialogClickListener clickListener) {
        dialogBinding.tvSubTitle.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (null != clickListener) {
                    dismissDialog();
                    clickListener.onClick(dialogBinding.tvSubTitle,mHour, mMinute);
                }
            }
        });
    }


    /**
     * 更改主标题文字的大小
     */
    public void setTitleTextSize(int sizeSP) {
        if (sizeSP <= 0) {
            sizeSP = 14;
        }
        dialogBinding.tvTitle.setTextSize(sizeSP);
    }

    /**
     * 修改Dialog阴影区域的灰度百分比
     * <p>
     * 取值 0-1.
     */
    public void setDimAmount(float rate) {
        Window window = dialog.getWindow();
        if (null != window) {
            if (rate < 0) {
                rate = 0;
            } else if (rate > 1) {
                rate = 1;
            }
            window.setDimAmount(rate);
        }
    }

    /**
     * 点击非内容区域是否可以关闭
     */
    public void setCancelable(boolean bool) {
        dialog.setCancelable(bool);
    }

    /**
     * 对外暴露点击事件的自定义接口
     */
    public interface AntTimePickerDialogClickListener {
        void onClick(View view, int selectedHour, int selectedMinute);
    }
}
```
布局文件：
```
<?xml version="1.0" encoding="utf-8"?>
<layout>
    <data>
        <import type="android.view.View"/>
        <variable name="isTitleShow" type="Boolean"/>
        <variable name="isSubTitleShow" type="Boolean"/>
        <variable name="isCancleBtShow" type="Boolean"/>
        <variable name="isConfirmBtShow" type="Boolean"/>
        <variable name="isSkipBtShow" type="Boolean"/>

    </data>

    <!--外面这层RL的实际作用是，让内部的LL布局参数生效，因为在填充布局时parent传递的null，所以被填充布局的第一层布局参数不生效，从第二层起才生效-->
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

        <LinearLayout
            android:id="@+id/ll_root_antDialog"
            xmlns:android="http://schemas.android.com/apk/res/android"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:divider="@drawable/shape_divider_05dp"
            android:gravity="center"
            android:orientation="vertical"
            android:showDividers="middle">

            <!--标题区域-->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="#ffc000"
                android:orientation="vertical">

                <!--主标题-->
                <TextView
                    android:id="@+id/tv_title"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:ellipsize="end"
                    android:gravity="center"
                    android:maxLines="1"
                    android:paddingBottom="8dp"
                    android:paddingLeft="@dimen/dp20"
                    android:paddingRight="@dimen/dp20"
                    android:paddingTop="8dp"
                    android:text="标题"
                    android:textColor="#fff"
                    android:textSize="16sp"
                    android:visibility="@{isTitleShow?View.VISIBLE:View.GONE}"/>

                <!--副标题-->
                <TextView
                    android:id="@+id/tv_subTitle"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:layout_marginBottom="4dp"
                    android:layout_marginLeft="@dimen/dp20"
                    android:layout_marginRight="@dimen/dp20"
                    android:layout_marginTop="-5dp"
                    android:background="@drawable/layerlist_bottomline_1dp_white"
                    android:ellipsize="end"
                    android:gravity="center"
                    android:maxLines="1"
                    android:paddingBottom="@dimen/dp2"
                    android:paddingLeft="@dimen/dp3"
                    android:paddingRight="@dimen/dp3"
                    android:text="副标题副题副副标题副标题副标题副标题副标题副标题副"
                    android:textColor="#fff"
                    android:textSize="10sp"
                    android:visibility="@{isSubTitleShow?View.VISIBLE:View.GONE}"/>
            </LinearLayout>

            <TimePicker
                android:id="@+id/timePicker_customTimePickerDialog"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:calendarViewShown="false"
                android:timePickerMode="spinner"/>

            <!--底部按钮-->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:divider="@drawable/shape_divider_05dp"
                android:orientation="horizontal"
                android:showDividers="middle"
                android:visibility="@{isConfirmBtShow||isCancleBtShow?View.VISIBLE:View.GONE}">

                <TextView
                    android:id="@+id/tv_cancleBT"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:ellipsize="end"
                    android:gravity="center"
                    android:maxLines="1"
                    android:paddingBottom="@dimen/dp10"
                    android:paddingTop="@dimen/dp10"
                    android:text="取消"
                    android:textColor="@color/c_666666"
                    android:textSize="16sp"
                    android:visibility="@{isCancleBtShow?View.VISIBLE:View.GONE}"/>

                <TextView
                    android:id="@+id/tv_skipBT"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:ellipsize="end"
                    android:gravity="center"
                    android:maxLines="1"
                    android:paddingBottom="@dimen/dp10"
                    android:paddingTop="@dimen/dp10"
                    android:text="跳过"
                    android:textColor="@color/c_666666"
                    android:textSize="16sp"
                    android:visibility="@{isSkipBtShow?View.VISIBLE:View.GONE}"/>

                <TextView
                    android:id="@+id/tv_confirmBT"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:ellipsize="end"
                    android:gravity="center"
                    android:maxLines="1"
                    android:paddingBottom="@dimen/dp10"
                    android:paddingTop="@dimen/dp10"
                    android:text="确定"
                    android:textColor="#ffc000"
                    android:textSize="16sp"
                    android:visibility="@{isConfirmBtShow?View.VISIBLE:View.GONE}"/>

            </LinearLayout>

        </LinearLayout>
    </RelativeLayout>

</layout>

```


代码中调用：

```
 /**
     * 展示时间选择器
     */
    private void showTimePickerDialog(final int year, final int month, final int dayOfMonth) {
        Calendar calendar2 = Calendar.getInstance();
        int hour = calendar2.get(Calendar.HOUR_OF_DAY);
        int minute = calendar2.get(Calendar.MINUTE);
        CustomTimePickerAlertDialog timePickerAlertDialog = new CustomTimePickerAlertDialog(context, hour, minute);
        timePickerAlertDialog.setCancelable(false);
        timePickerAlertDialog.setTitle("请设置迟到判定时间——时：分");
        timePickerAlertDialog.setPositiveButton("确定", new CustomTimePickerAlertDialog
                .AntTimePickerDialogClickListener() {
            @Override
            public void onClick(View view, int selectedHour, int selectedMinute) {
                LogUtils.e("最终设置的时间是：", selectedHour + "/" + selectedMinute);
                sendLateTimeToServer(year, month, dayOfMonth, selectedHour, selectedMinute);
            }
        });
        timePickerAlertDialog.setSkipButton("跳过", new CustomTimePickerAlertDialog.AntTimePickerDialogClickListener() {
            @Override
            public void onClick(View view, int selectedHour, int selectedMinute) {
                sendLateTimeToServer(0, 0, 0, 0, 0);
                goToSignSituationAct();
            }
        });
        timePickerAlertDialog.setNegativeButton("取消", null);
        timePickerAlertDialog.show();
    }
```

## 3 总结：
以上内容就是完整代码，整体比较简单。

> * DatePicker 设置 为 Spinner 模式时这里使用了`style="@android:style/Widget.DatePicker"`
>* TimePicker 设置为 Spinner 模式时使用了 `timePickerMode=spinner ` , 之所以不用 style ，是因为spinner 模式的 `style="@android:style/Widget.TimePicker" `被系统 私有了，无法调用，系统对外提供的都是 MaterialDesign 模式的Style


>最初是想将 DatePicker 和 TimePicker 包装到同一个 AlertDialog  中，但是非常遗憾，TimePicker 和 DatePicker 底层用的是 NumberPicker ，宽度写死了，导致 将 TimePicker 和 DatePicker 横向放在同一个 AlertDialog 中时会导致超出宽度，部分内容无法展示。这种情况暂时没想好怎么解决，如果哪位大侠知道，烦请告知，谢谢!
