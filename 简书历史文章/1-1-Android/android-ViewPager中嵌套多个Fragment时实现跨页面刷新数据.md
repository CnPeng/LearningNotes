##一、需求
ViewPager中嵌套多个列表页面，点击某个列表页面中的某个条目之后，刷新其他页面对应位置的数据。
**注意：点击之后，需要个性化当前页面中被点击的内容**

>其实，实际使用中可能这种需求会比较少，我也只是举个例子。举这个例子的目的是为了引出文章中主要的知识点——**如何在FragmentPagerAdapter 的子类中获取TAG，并根据TAG获取对应的Fragment对象。**

最终效果示意图：
![ViewPager中跨页面刷新数据](http://upload-images.jianshu.io/upload_images/2551993-ada72b6e58deaa84.gif?imageMogr2/auto-orient/strip)

##二、主要知识点

如何在FragmentPagerAdapter 的子类中获取TAG，并根据TAG获取对应的Fragment对象？

ViewPager中嵌套Fragment的时候，我们通常使用的是FragmentPagerAdapter , 在 FragmentPagerAdapter 源码中会通过 instantiateItem 生成界面，其代码如下：
```
    @Override
    public Object instantiateItem(ViewGroup container, int position) {
        if (mCurTransaction == null) {
            mCurTransaction = mFragmentManager.beginTransaction();
        }

        final long itemId = getItemId(position);

        // Do we already have this fragment?
        String name = makeFragmentName(container.getId(), itemId);
        Fragment fragment = mFragmentManager.findFragmentByTag(name);
        if (fragment != null) {
            if (DEBUG) Log.v(TAG, "Attaching item #" + itemId + ": f=" + fragment);
            mCurTransaction.attach(fragment);
        } else {
            fragment = getItem(position);
            if (DEBUG) Log.v(TAG, "Adding item #" + itemId + ": f=" + fragment);
            mCurTransaction.add(container.getId(), fragment,
                    makeFragmentName(container.getId(), itemId));
        }
        if (fragment != mCurrentPrimaryItem) {
            fragment.setMenuVisibility(false);
            fragment.setUserVisibleHint(false);
        }

        return fragment;
    }
```

上述代码的整体逻辑是：开启事务，然后根据TAG获取Fragment对象，然后判断这个Fragment对象是否已经存在，如果已经存在则重新attcah到Activity中进行展示；如果不存在，则去生成该Fragment对象，并添加到Activity中。

我们重点看下面这几句：
```
 // Do we already have this fragment?
String name = makeFragmentName(container.getId(), itemId);
Fragment fragment = mFragmentManager.findFragmentByTag(name);
```
上面三行代码已经很清楚了，`Do we already have this fragment? ` 我们是否已经有这个fragment了呢？我也不知道，那么就去取TAG，然后根据TAG去获取Fragment对象。makeFragmentName( ) 方法就是生成TAG的方法，具体如下：
```
private static String makeFragmentName(int viewId, long id) {
        return "android:switcher:" + viewId + ":" + id;
    }
```
好了，本文的重点已经介绍完了。。。源码中已经写好代码了，只不过是private的，我们所要做的事情只是复制一份到 FragmentPagerAdapter 的子类中就好了。

不多说了，直接上代码吧

##三、具体实现代码

###1、RefreshFmInVpActivity
```
/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/9/20:上午11:16
 * <p>
 * 说明：ViewPager 中包含多个Fragment 对象 , 当一个Fragment 对象中的数据发生变化时，同时刷新另外几个Fragment中的数据。
 */

public class RefreshFmInVpActivity extends AppCompatActivity {

    private ActivityRefreshFmInVpBinding binding;
    private LocalBroadcastManager        localBroadCastManager;
    private UpdateDataReceiver           receiver;
    private VpAdapter_RefreshFmInVp      vpAdapter;
    private ViewPager viewPager;

    @Override
    protected void onCreate(
            @Nullable
                    Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = DataBindingUtil.setContentView(this, R.layout.activity_refresh_fm_in_vp);

        initViewPager();
        initLocalBroadCastReceiver();

    }

    private void initViewPager() {
        viewPager = binding.viewPagerRefreshFmInVp;
        TabLayout tabLayout = binding.tablayoutTitleRefreshFmInVp;

        tabLayout.setupWithViewPager(viewPager);    //TabLayout与ViewPager 绑定

        String[] titles = {"第一页", "第二页", "第三页", "第四页"};

        FragmentManager fragemntManager = getSupportFragmentManager();
        vpAdapter = new VpAdapter_RefreshFmInVp(fragemntManager, titles);
        viewPager.setAdapter(vpAdapter);
    }

    private void initLocalBroadCastReceiver() {
        localBroadCastManager = LocalBroadcastManager.getInstance(this);
        IntentFilter intentFilter = new IntentFilter(FLAG_BROADCAST_TO_UPDATE);

        receiver = new UpdateDataReceiver();
        localBroadCastManager.registerReceiver(receiver, intentFilter);
    }

    private class UpdateDataReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            int position = intent.getIntExtra(KEY_POSITION, 0);
            int curPageIndex=viewPager.getCurrentItem();
            vpAdapter.refreshMofifiedPageData(position,curPageIndex);
        }
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        localBroadCastManager.unregisterReceiver(receiver);
    }
}
```

###2、R.layout.activity_refresh_fm_in_vp

```
<?xml version="1.0" encoding="utf-8"?>
<layout>
    <data>

    </data>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                  android:layout_width="match_parent"
                  android:layout_height="match_parent"
                  android:orientation="vertical">

        <android.support.design.widget.TabLayout
            android:id="@+id/tablayout_title_refreshFmInVp"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"/>

        <android.support.v4.view.ViewPager
            android:id="@+id/viewPager_refreshFmInVp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    </LinearLayout>
</layout>
```

###3、VpAdapter_RefreshFmInVp
```
/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/9/20:上午11:41
 * <p>
 * 说明：ViewPager的适配器。重点是如何生成并获取TAG，然后根据TAG获取Fragment对象
 */

class VpAdapter_RefreshFmInVp extends FragmentPagerAdapter {
    private final String[]        titles;
    private final FragmentManager fragmentManager;
    List<String> tags = new ArrayList<>();

    public VpAdapter_RefreshFmInVp(FragmentManager fm, String[] titles) {
        super(fm);
        fragmentManager = fm;
        this.titles = titles;
    }

    @Override
    public int getCount() {
        return titles.length;
    }

    @Override
    public Fragment getItem(int position) {
        return Fragment_VpItem.newInstance(position);   //多个页面的布局样式一致，所以需要根据position展示不同的内容
    }

    @Override
    public CharSequence getPageTitle(int position) {
        return titles[position];
    }

    @Override
    public Object instantiateItem(ViewGroup container, int pageIndex) {
        //创建tag并存储到集合中
        String fragmentNameTag = makeFragmentName(container.getId(), pageIndex);
        if (!tags.contains(fragmentNameTag)) {
            tags.add(fragmentNameTag);
        }
        return super.instantiateItem(container, pageIndex);
    }

    /**
     * 创建Fragment的名称也就是tag,该方法源自 FragmentPagerAdapter的源码
     */
    private static String makeFragmentName(int viewId, long id) {
        return "android:switcher:" + viewId + ":" + id;
    }

    /**
     * 刷新数据
     *
     * @param position     列表中被点击条目的位置
     * @param curPageIndex 当前正在展示的页面
     */
    public void refreshMofifiedPageData(int position, int curPageIndex) {
        for (int i = 0; i < tags.size(); i++) {
            Fragment_VpItem fragment = (Fragment_VpItem) fragmentManager.findFragmentByTag(tags.get(i));
            fragment.refreshData(position, curPageIndex);
        }
    }
}
```

###4、Fragment_VpItem
```

/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/9/20:上午11:49
 * <p>
 * 说明：嵌套在ViewPager 中的Fragment。ViewPager中多个页面布局一致时，可以只创建一个Fragment类
 * <p>
 * 如果想要同时刷新各个页面中的数据，并且保持个页面中的数据一致时，直接在Fragment中接收广播即可（本页面中注释掉的代码就是直接接收广播的代码）；
 * 但是，此处我们做了个性化，需要让当前被点击的页面和其他页面的数据不一致，所以就需要在activity中接收广播。
 * <p>
 * 当然了，这些都不是最重要的，最重要的是，我们需要了解怎么在FragmentPagerAdapter中获取tag,并根据TAG获取Fragment对象。
 */

public class Fragment_VpItem extends Fragment {
    public final static String       KEY_POSITION             = "position"; //被点击条目的位置
    public final static String       KEY_PAGE_INDEX           = "pageIndex";//页面索引
    public final static String       FLAG_BROADCAST_TO_UPDATE = "itemClickedToUpdateOtherPages";  //本地广播标志位
    private             List<String> items                    = new ArrayList<>();

    private FragmentRefreshFmInVpBinding binding;
    private int                          pagePosition;    //当前页面的索引
    private View                         fragmentView;
    private LvAdapter                    adapter;
    //    private LocalBroadcastManager        localBroadCastManager;
    //    private UpdateDataReceiver           receiver;


    public static Fragment newInstance(int position) {
        Bundle budle = new Bundle();
        budle.putInt(KEY_PAGE_INDEX, position);

        Fragment_VpItem fragment = new Fragment_VpItem();
        fragment.setArguments(budle);
        return fragment;
    }

    @Override
    public void onCreate(
            @Nullable
                    Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        pagePosition = null != getArguments() ? getArguments().getInt(KEY_PAGE_INDEX) : 0;

        //        initLocalBroadCastReceiver();
    }

    //    private void initLocalBroadCastReceiver() {
    //        localBroadCastManager = LocalBroadcastManager.getInstance(getContext());
    //        IntentFilter intentFilter = new IntentFilter(FLAG_BROADCAST_TO_UPDATE);
    //
    //        receiver = new UpdateDataReceiver();
    //        localBroadCastManager.registerReceiver(receiver, intentFilter);
    //    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater,
                             @Nullable
                                     ViewGroup container,
                             @Nullable
                                     Bundle savedInstanceState) {
        if (null == fragmentView) {        //OnCreateView会被多次调用所以判断是否为空
            binding = DataBindingUtil.inflate(inflater, R.layout.fragment_refresh_fm_in_vp, container, false);
            fragmentView = binding.getRoot();
            initView();
        }

        return fragmentView;
    }

    private void initView() {
        for (int i = 0; i < 20; i++) {
            items.add("第" + pagePosition + "页，第" + i + "条数据");
        }

        adapter = new LvAdapter(getActivity());
        binding.lvRefreshFmInVp.setAdapter(adapter);
        adapter.setData(items);
    }

    public void refreshData(int position, int curPageIndex) {
        if (curPageIndex != pagePosition) {
            items.set(position, "第" + (curPageIndex + 1) + "页的第" + position + "条被点击了");
        } else {
            items.set(position, "被点击了");
        }
        adapter.setData(items);
    }


    //    private class UpdateDataReceiver extends BroadcastReceiver {
    //        @Override
    //        public void onReceive(Context context, Intent intent) {
    //            int position = intent.getIntExtra(KEY_POSITION, 0);
    //            items.set(position, items.get(position) + "其他页面的该条被点击了");
    //
    //            adapter.setData(items);
    //        }
    //    }

    //    @Override
    //    public void onDestroy() {
    //        super.onDestroy();
    //        localBroadCastManager.unregisterReceiver(receiver);
    //    }
}
```
###5、LvAdapter
```

/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/9/20:下午2:24
 * <p>
 * 说明：ListView的适配器
 */

class LvAdapter extends BaseAdapter {

    private List<String> list = new ArrayList<>();
    private final Context context;

    public LvAdapter(FragmentActivity activity) {
        context = activity;
    }

    public void setData(List<String> list) {
        this.list.clear();
        this.list.addAll(list);
        notifyDataSetChanged();
    }

    @Override
    public int getCount() {
        return list.size();
    }

    @Override
    public Object getItem(int position) {
        return list.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(final int position, View convertView, ViewGroup parent) {

        //此处测试使用，不再使用holder了。实际项目中需要使用holder
        if (null == convertView) {
            convertView = LayoutInflater.from(context).inflate(android.R.layout.simple_list_item_1, null);
        }

        final TextView textView = (TextView) convertView.findViewById(android.R.id.text1);
        textView.setText(list.get(position));

        //添加条目点击事件
        convertView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //发送本地广播，通知其他页面刷新数据
                sendLocalBroadCastToUpdate(position);
            }
        });

        return convertView;
    }

    /**
     * 发送本地广播
     */
    private void sendLocalBroadCastToUpdate(int position) {
        Intent intent = new Intent(Fragment_VpItem.FLAG_BROADCAST_TO_UPDATE);
        intent.putExtra(Fragment_VpItem.KEY_POSITION, position);
        LocalBroadcastManager.getInstance(context).sendBroadcast(intent);
    }
}
```

##四、总结
其实代码很简单，FragmentPagerAdapter源码中已经实现好了，重要的只是一种思路：
>列表中条目被点击发送广播——Activity接收广播——将广播事件内容传递到ViewPager 适配器——在ViewPager适配器中获取全部Fragment对象——调用Fragment中的方法刷新列表中的数据

在上面的代码中已经有说明，其实如果不需要个性化当前页面内容的话，直接在Fragment中接收广播并更新数据就好了

##五、附录
[demo地址 , 其中的 b_23 对应文中内容——https://github.com/CnPeng/CrazyAndroid.git](https://github.com/CnPeng/CrazyAndroid.git)
