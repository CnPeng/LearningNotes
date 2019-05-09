## 1 ListView为什么需要局部刷新？
使用ListView时，如果数据发生变化，通常会使用 notifyDataSetChanged( ) 方法去通知适配器刷新界面。但是 **notifyDataSetChanged( ) 是刷新全部数据和数据对应的view，如果数据量较大的话，这是比较消耗资源的**。所以我们就需要考虑如何实现ListView的局部更新。

>其实，在RecyclerView中，当列表数据发生变化时，提供了两种更新方式，具体如下：
>- **全局更新**
 `通过 notifyDataSetChanged( ) 实现`
>- **局部更新**
`通过 notifyItemChanged( ) 、notifyItemRemoved( ) 、notifyItemInstered( ) 等方法实现`
>
>由于此篇文章主要介绍ListView局部刷新的实现，所以关于RecyclerView的内容不做过多说明

## 2 最终效果示意
先上动图：
![singleRefresh.gif](http://upload-images.jianshu.io/upload_images/2551993-65519b32c1872d7c.gif?imageMogr2/auto-orient/strip)
在上面的动图中，我们通过底部红色的log信息可以看到，进入页面初始化数据时会不断的调用 getView( ) 方法去绘制view，而我们单击某个条目时 只会走一次 getView( )，这就表明，达到了局部刷新的效果。

## 3 详细示例代码：
代码中重点部分都有相应注释，其他不再赘述。

>代码中使用了数据绑定--DataBinding, 关于数据绑定的知识从很长时间前就开始整理，现在已经整理了一个将近70页的word文档，内容包括网络上看到了，也有工作中总结过，但是还没有整理完毕。后期整理完毕后会单独作为一篇或多篇写出来，如果你想提前看下未完成版，可以留言(✿◡‿◡✿)

* **ListViewLocalRefreshActivity.java**
```

/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/8/24:下午8:13
 * <p>
 * 说明：ListView 单条局部刷新的实现--点击之后更新局部条目
 */

public class ListViewLocalRefreshActivity extends AppCompatActivity {

    private LocalRefreshLVBinding    binding;
    private LvAdapter_LocalRefreshLv adapter;

    @Override
    protected void onCreate(
            @Nullable
                    Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = DataBindingUtil.setContentView(this, R.layout.activity_lv_local_refresh);

        initListView();
        initLvITemClickEvent();
        setDataToLv();
    }

    private void initListView() {
        adapter = new LvAdapter_LocalRefreshLv(ListViewLocalRefreshActivity.this);
        binding.lvLocalRefresh.setAdapter(adapter);
    }

    private void setDataToLv() {
        List<User> userList = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            userList.add(new User("张三", i));
        }

        if (null != adapter) {
            adapter.setList(userList);
        }
    }

    private void initLvITemClickEvent() {
        binding.lvLocalRefresh.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                User clickedUser = (User) parent.getAdapter().getItem(position); //获取被点击的bean

                clickedUser.setName(clickedUser.getName() + position);  //点击后更改数据

                updateSingleItemData(clickedUser);      //更新条目view
            }
        });
    }

    /**
     * 更新单条数据，由于LV的复用机制，所以需要获取第一条可见和最后一条可见，然后获取对应的bean，然后判断该bean是否与被点击的bean一致
     */
    private void updateSingleItemData(User clickedUser) {
        int firstVisiableIndex = binding.lvLocalRefresh.getFirstVisiblePosition();
        int lastVisiableIndex = binding.lvLocalRefresh.getLastVisiblePosition();

        for (int i = firstVisiableIndex, j = lastVisiableIndex; i <= j; i++) {
            User user = (User) binding.lvLocalRefresh.getItemAtPosition(i);  //获取 i 索引出的bean
            if (user.getId() == clickedUser.getId()) {  //如果数据一致
                //获取对应的条目view（由于复用机制，所以此处需要 i- firstVisiableIndex）
                View view = binding.lvLocalRefresh.getChildAt(i - firstVisiableIndex);

                adapter.updateChangedItemBean(i, clickedUser);//此处千万不要忘了更新适配器中的集合数据，否则adapter中的数据无变化view自然也不会变

                adapter.getView(i, view, binding.lvLocalRefresh);
            }
        }
    }
}
```
****
* **LvAdapter_LocalRefreshLv**
```
/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/8/24:下午8:23
 * <p>
 * 说明：局部刷新的ListView的适配器
 */

class LvAdapter_LocalRefreshLv extends BaseAdapter {

    private final Context                   context;
    private       LocalRefreshLvItemBinding binding;

    private List<User> userList = new ArrayList<>();

    public LvAdapter_LocalRefreshLv(Context context) {
        this.context = context;
    }

    /**
     * 更新全部集合数据并更新界面
     */
    public void setList(List<User> userList) {
        if (null != userList) {
            this.userList.clear();
            this.userList.addAll(userList);
        }
        notifyDataSetChanged();
    }

    /**
     * 只更新单条数据且不更新界面
     */
    public void updateChangedItemBean(int position, User newUserBean) {
        userList.set(position, newUserBean);
    }

    @Override
    public int getCount() {
        return userList.size();
    }

    @Override
    public Object getItem(int position) {
        return userList.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {

        //通过该log的打印，我们能确认单条更新的方法是生效的。notifyDataSetChanged会打印多次。
        LogUtils.e("被更新的条目索引是：", position + "");

        if (null == convertView) {
            binding = DataBindingUtil.inflate(LayoutInflater.from(context), R.layout.item_lv_local_refresh, parent, 
                    false);
            convertView = binding.getRoot();
            convertView.setTag(binding);
        } else {
            binding = (LocalRefreshLvItemBinding) convertView.getTag();
        }

        binding.setUserName(userList.get(position).getName());
        return convertView;
    }
}
```
*****
* **activity_lv_local_refresh.xml**
```
<?xml version="1.0" encoding="utf-8"?>
<layout>
    <data class="LocalRefreshLVBinding">

    </data>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                  android:layout_width="match_parent"
                  android:layout_height="match_parent"
                  android:orientation="vertical">
        <ListView
            android:id="@+id/lv_localRefresh"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>


    </LinearLayout>
</layout>
```
## 4 参考内容以及附录
* 参考内容：
[http://blog.csdn.net/yuyuanhuang/article/details/43198107](http://blog.csdn.net/yuyuanhuang/article/details/43198107)

* 文中代码对应的GitHub地址：
[https://github.com/CnPeng/CrazyAndroid.git](https://github.com/CnPeng/CrazyAndroid.git)
其中的 b_21 对应文中内容
