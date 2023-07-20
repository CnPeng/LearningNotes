xml中设置初始的TabLayout模式为 滚动，并且初始布局宽度为 wrap_content：

```
 <android.support.design.widget.TabLayout
                            android:id="@+id/tabLayout_studentMain"
                            android:layout_width="wrap_content"
                            android:layout_height="35dp"
                            android:background="@color/white"
                            app:tabMode="scrollable"
                            bind:tabIndicatorColor="@color/major_ffc000"
                            bind:tabSelectedTextColor="@color/major_ffc000"
                            bind:tabTextAppearance="@style/TabLayoutTextStyle"
                            bind:tabTextColor="@color/c_3e3e3e" />
```

java代码中根据测量出来的TAB总宽度，判断是否需要改变TabLayout的模式和布局宽度：

```
 mBinding.tabLayoutStudentMain.getViewTreeObserver().addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
            @Override
            public void onGlobalLayout() {
                int width = mBinding.tabLayoutStudentMain.getWidth();
                int measuredWidth = mBinding.tabLayoutStudentMain.getMeasuredWidth();
                int screenWidth = CommonUtils.getScreenWidth(getActivity());
                //如果TAB比较少，不足以填满屏幕宽度，则使用Fixed模式，并改变布局模式为填充宽度
                if (width < screenWidth) {
                    mBinding.tabLayoutStudentMain.setTabMode(MODE_FIXED);
                    LinearLayout.LayoutParams layouParams = (LinearLayout.LayoutParams) mBinding.tabLayoutStudentMain.getLayoutParams();
                    layouParams.width = LinearLayout.LayoutParams.MATCH_PARENT;
                    mBinding.tabLayoutStudentMain.setLayoutParams(layouParams);
                }
                LogUtils.e("宽度/测量宽度/屏幕宽度", width + "/" + measuredWidth + "/" + screenWidth);
                mBinding.tabLayoutStudentMain.getViewTreeObserver().removeOnGlobalLayoutListener(this);
            }
        });

```