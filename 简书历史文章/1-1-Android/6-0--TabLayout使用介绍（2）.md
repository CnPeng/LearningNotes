> 180928更新
>关于指定条的宽度，在support28 / androdix 的版本添加了属性设置`app:tabIndicatorFullWidth `为false时自适应文字的宽度， 还有反射里面的mTabStrip也改成了slidingTabIndicator，如果是更高的版本再使用mTabStrip会报空指针。

相关文章：
[6.0--TabLayout使用介绍（1）](http://www.jianshu.com/writer#/notebooks/8238235/notes/7501749)

在[6.0--TabLayout使用介绍（1）](http://www.jianshu.com/writer#/notebooks/8238235/notes/7501749)中，介绍了单独使用TabLayout的情况，接下来要说的是，TabLayout 与 ViewPager配合使用实现ViewPagerIndicator的功能。

### (1). activity_vp_and_tl.xml 布局文件
布局方式有两种，如下：
######方式1 ：ViewPager 与TabLayout是平级关系
activity_vp_and_tl.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:app="http://schemas.android.com/apk/res-auto"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">
    <android.support.design.widget.TabLayout
        android:id="@+id/tableLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:tabMode="scrollable">
    </android.support.design.widget.TabLayout>

<!--backgroud的设置是为了给vp添加一个红色的顶部边线，这个边线并不是Tablayout 的指示线-->
    <android.support.v4.view.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@drawable/layer_list_singleline">
    </android.support.v4.view.ViewPager>

</LinearLayout>
```
######方式2 ：ViewPager 中嵌套TabLayout
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:app="http://schemas.android.com/apk/res-auto"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">


    <!--这种方式也可以直接实现vp 和tl 的绑定，但这样的话，顶部的边线就需要由具体的页面去实现-->
    <android.support.v4.view.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <android.support.design.widget.TabLayout
            android:id="@+id/tableLayout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
        </android.support.design.widget.TabLayout>
    </android.support.v4.view.ViewPager>
</LinearLayout>
```
### (2).代码中的使用：
VpAndTlActvitiy.java
```
/**
 * Created by CnPeng on 2016/12/6.
 * <p>
 * 展示viewPager 和 TabLayout的配合使用
 */

public class VpAndTLActivity extends AppCompatActivity {

    public static void startVpAndTLActivity(Context context) {
        Intent inent = new Intent(context, VpAndTLActivity.class);
        context.startActivity(inent);
    }

    @Override
    protected void onCreate(
            @Nullable
                    Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_vp_and_tl);

        initView();
    }

    /**
     * 初始化view
     */
    private void initView() {
        ViewPager viewPager = (ViewPager) findViewById(R.id.viewPager);
        TabLayout tablayout = (TabLayout) findViewById(R.id.tableLayout);

        tablayout.setSelectedTabIndicatorColor(getResources().getColor(R.color.colorAccent));   //指示器的颜色(底部横线的颜色)
        tablayout.setSelectedTabIndicatorHeight(15);                             //指示器的高度，单位 px（底部横线的高度）
        tablayout.setTabTextColors(R.color.colorPrimary, R.color.colorAccent);   //指示器中文字的颜色

        FragmentManager manager = getSupportFragmentManager();

        List<String> titles = new ArrayList<>();
        titles.add("第 1 个页面");
        titles.add("第 2 个页面");
        titles.add("第 3 个页面");
        titles.add("第 4 个页面");
        titles.add("第 5 个页面");

        viewPager.setAdapter(new MyVpAndTlAdapter(titles));

        //关联vp 和 tl (如果vp 和 TL 是平级关系时，使用这种；如果VP中包含TL时，不用写这句代码)
        tablayout.setupWithViewPager(viewPager);
    }
}
```

###(3). ViewPager适配器代码
MyVpAndTlAdapter.java
```
/**
 * Created by CnPeng on 2016/12/6.
 * <p>
 * ViewPager + TabLayout 时使用的适配器
 */
public class MyVpAndTlAdapter extends PagerAdapter {
    private final List<String> titles;

    public MyVpAndTlAdapter(List<String> titles) {
        this.titles = titles;
    }

    @Override   //获取总数量
    public int getCount() {
        return titles.size();
    }

    @Override   //实例化布局
    public Object instantiateItem(ViewGroup container, int position) {
        View view = new ImageView(container.getContext());
        view.setBackgroundResource(R.drawable.ic_launcher);
        container.addView(view);
        return view;
    }

    @Override  //获取标题
    public CharSequence getPageTitle(int position) {
        //        return super.getPageTitle(position);
        return titles.get(position);
    }

    @Override   //销毁视图
    public void destroyItem(ViewGroup container, int position, Object object) {
        //        super.destroyItem(container, position, object);
        container.removeView((View) object);
    }

    @Override
    public boolean isViewFromObject(View view, Object object) {
        return view == object;  //固定写法
    }
}

```
附：layer_list_singleline.xml,这个是第一种方式中vp的那个backgroud
```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">

    <!--底层使用蓝色填充色-->
    <item>
        <shape>
            <solid android:color="@color/colorAccent"/>
        </shape>
    </item>

    <!--上面一层距离底层的顶部1dp,类似marginTop,填充色为白色，这样就形成了一个带有蓝色顶部边线的白色背景的图-->
    <item android:top="1dp">
        <shape>
            <solid android:color="#fff"/>
        </shape>
    </item>
</layer-list>
```
>总结：
**A**:
如果vp 和 TL 是平级关系,需要使用 `tablayout.setupWithViewPager(viewPager);`  将二者关联。
如果VP中包含TL时，会自动关联，不用写上面这句代码
**B**:
vp配合TL使用时，TabLayout 中的标签名称是通过ViewPager适配器中的getPageTitle()方法来设置的。所以，需要给适配器传入一个标签名称的集合。

[参考连接https://developer.android.com/reference/android/support/design/widget/TabLayout.html](https://developer.android.com/reference/android/support/design/widget/TabLayout.html)

> **一个TL+一个VP +一个FM** 实现多页面切换时，VP使用FragmentPagerAdapter，其写法参考：https://developer.android.com/reference/android/support/v4/app/FragmentPagerAdapter.html
