##1.TabLayout基本介绍
TabLayout是安卓6.0推出的，可以替代 ViewPagerIndicator 的一个控件，存放在 design 包下，继承自 HorizontalScrollView 。使用的时候需要先导入 android.support.design.widget 包。

## 2. TabLaout的使用介绍
### (1). 单独使用TabLayout
使用TabLayout的时候，需要给它添加 Tab , 添加 Tab 的方式有两种，一种是在代码中通过 tablayout.new Tab( ) 动态添加，一种是在xml中通过 TabItem 添加。具体如下：

####1）代码中动态添加Tab
activity_tablayout_test.xml 布局文件：
``` 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:app="http://schemas.android.com/apk/res-auto"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">

    <!--单独使用方式1 只在xml中声明，从代码中添加tab-->
    <android.support.design.widget.TabLayout
        android:id="@+id/tableLayout_1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:tabMode="scrollable">

    </android.support.design.widget.TabLayout>

</LinearLayout>
```

TabLayoutTestActivity.java 具体使用：

```
/**
 * Created by CnPeng on 2016/12/7.
 * <p>
 * tablayout的使用示例
 */

public class TabLayoutTestActivity extends AppCompatActivity {
    @Override
    protected void onCreate(
            @Nullable
                    Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_tablayout_test);

        initView1();

    }


    /**
     * 代码中添加TabItem 并实现监听
     */
    private void initView1() {
        TabLayout tablayout = (TabLayout) findViewById(R.id.tableLayout_1);

        for (int i = 0; i < 6; i++) {
            // 创建Tab的同时设置tab描述文字以及tab的tag，并添加到tablayout中 . Tab只能用在代码中，不能用在xml          
            tablayout.addTab(tablayout.newTab().setText("代码中定义的" + i + "号标签").setTag(i));
        }

        tablayout.addOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
            @Override
            public void onTabSelected(TabLayout.Tab tab) {
                //                tab.setIcon(R.drawable.ic_launcher);
                switch (tab.getTag().toString()) {  //根据标签做不同的处理
                    case "1":
                        Toast.makeText(TabLayoutTestActivity.this, "1号被选中了，可以用 fragmentA 替换该页面下半部分", Toast
                                .LENGTH_SHORT).show();
                        break;
                    case "2":
                        Toast.makeText(TabLayoutTestActivity.this, "2号被选中了，可以用 fragmentB 替换该页面下半部分", Toast
                                .LENGTH_SHORT).show();
                        break;
                }
            }

            @Override
            public void onTabUnselected(TabLayout.Tab tab) {
                //                tab.setIcon(null);    //取消Icon时使用null

            }

            @Override
            public void onTabReselected(TabLayout.Tab tab) {
                //                tab.setIcon(R.drawable.ic_launcher);
            }
        });
    }
}
```
####2）布局文件中添加Tab
xml布局文件
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:app="http://schemas.android.com/apk/res-auto"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">

    <!--单独使用放肆2 在xml中声明并添加tab
        xml中创建TabItem的时候只有三个属性：layout  icon  text
    -->
    <android.support.design.widget.TabLayout
        android:id="@+id/tableLayout_2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="@dimen/dp20"
        app:tabMode="scrollable">

        <android.support.design.widget.TabItem
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="xml中定义 0号标签"/>
        <android.support.design.widget.TabItem
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="xml中定义 1号标签"/>
        <android.support.design.widget.TabItem
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="xml中定义 2号标签"/>
        <android.support.design.widget.TabItem
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="xml中定义 3号标签"/>

    </android.support.design.widget.TabLayout>

</LinearLayout>
```
代码中使用：
```
/**
 * Created by CnPeng on 2016/12/7.
 * <p>
 * tablayout的使用示例
 */

public class TabLayoutTestActivity extends AppCompatActivity {
    @Override
    protected void onCreate(
            @Nullable
                    Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_tablayout_test);

        initView2();
    }

    /**
     * xml文件中添加TabItem ,并实现监听
     */
    private void initView2() {
        TabLayout tablayout2 = (TabLayout) findViewById(R.id.tableLayout_2);
        tablayout2.addOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
            @Override   //被选中
            public void onTabSelected(TabLayout.Tab tab) {
                //这里拿到的tab 其实就是代码中的tabitem，但tabitem不能设置tag,所以，还是从代码中添加tab比较实用
                switch (tab.getPosition()) {
                    case 0:
                        Toast.makeText(TabLayoutTestActivity.this, "xml中定义的0号被点击了", Toast.LENGTH_SHORT).show();
                        break;
                    case 1:
                        Toast.makeText(TabLayoutTestActivity.this, "xml中定义的1号被点击了", Toast.LENGTH_SHORT).show();
                        break;


                }
            }

            @Override   //未被选中
            public void onTabUnselected(TabLayout.Tab tab) {

            }

            @Override   //重新被选中
            public void onTabReselected(TabLayout.Tab tab) {

            }
        });
    }
}

```
>总结：
A:
在xml布局文件中添加Tab时，只能使用 TabItem , 而且TabItem只有三个属性， layout ， text  , icon ; 而 Tab 则只是用在java代码中，但 Tab  的属性值和方法更加丰富，所以，实际使用的时候，推荐使用Tab 在代码中动态添加 
>
B:
>在 TabLayout节点中，定义了app:tabMode="scrollable"  ，这个 tabMode 表示tab的排列方式，有两个取值：
>* scrollable表示tab过多超出屏幕宽度时，一直横着往外排列，然后滚动可以查看；
>* fixed表示TabLayout的最大宽度满屏，各Tab宽度均分。当 tab 过多超出屏幕宽度时，会挤吧一下 ; 
>* 如果不设置，默认fixed。
>
>具体下过参考下图：
>![TabMode 的两种取值模式的效果差异](http://upload-images.jianshu.io/upload_images/2551993-be5215013b9c5783.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
