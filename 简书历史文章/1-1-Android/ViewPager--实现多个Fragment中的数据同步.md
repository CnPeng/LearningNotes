当前做的项目中，需要实现点击 “通知编辑界面” 中的 📎 跳转到 “附件选择界面”。附件选择界面实现的思路是 Activity+TabLayout+ViewPager，ViewPager 中嵌套Fragment，fragment 中只用一个lv展示数据。



![通知编辑界面.png](http://upload-images.jianshu.io/upload_images/2551993-92b4a4a4eb467f75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![选择附件.png](http://upload-images.jianshu.io/upload_images/2551993-23bc7e46d9b90c21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上图中，“全部” 标签中展示本地所有的文件，“文档”中只展示本地的文档，“图片” 标签中展示本地所有的图片文档。 如果我在 “全部” 中选中一个文档，那么我需要记录它的选中状态，当我切换到 “文档” 标签时就直接选中它，这就是这里说的数据同步。

**实现这个数据同步的话，整体思路是在 “选择附件界面” 的Activity 中定义各个标签对应的集合，然后 在Frgament 中直接获取并使用。**

开始的时候，我是在 “附件选择界面” 的Activity中开启子线程直接获取数据填充到各个集合，这样可以直接实现数据同步，但这样有一个问题，就是从 “通知编辑界面“ 跳转到 ” 附件选择界面“ 的时候需要等待老大一会儿，用户体验不佳。

虽然可以在 ”通知编辑界面” 加dialog进度提示，但是总感觉这不符合正常逻辑；正常应该是进入 “上传文件界面” 后，去加载本地数据，如果数据多加载慢就给出加载提示。

**所以，为了将加载进度挪到 “上传文件界面” 中，我将获取本地文件的操作挪到了 Fragment 中。在 Fragment 中，先获取定义在Activtiy 的各个Tab 对应的集合，然后在 onCreateView 方法中开启子线程获取本地文件数据并添加到Tab对应的集合中。为了防止数据重复，在添加到Tab 对应的集合之前，先根据集合的size是否为0 判断集合中是否有数据，如果有数据，就不再添加，否则就添加。然后用Handler 发送消息 去更新LV 的适配器。**

如果ViewPager没有预加载的话，上面在Fragment 中获取数据并添加的方式是没错的。but ，ViewPager 最少会预加载一页，这就导致了一个问题，当我在 “全部” 的Fragment中开启线程加载本地文件数据的时候，由于VP 预加载，“文档” 的Frgament 中也在同一时刻开启了子线程去获取本地文件数据。这样就导致了在判断 集合的size 时 拿到的都是0，然后两边就分别获取到了数据并添加到集合中，导致 文档 集合中数据的重复。

为了解决数据重复的问题，可以想法子关闭ViewPager 的预加载，一个关闭VP预加载的简单粗暴的方式就是直接复制ViewPager 类中的代码，然后更改类名作为自定义VP，并将 private static final int DEFAULT_OFFSCREEN_PAGES = 1  的值改成0，这样就有了一个不会预加载的VP，但这样就不能与TabLayout 关联并实现同步滑动了。所以，**在不更改VP 的情况下，我们可以考虑加 同步锁，将 Tab 对应的集合锁定，当一个线程正在操作/使用该集合的时候，不让其他线程操作和使用。 **相关代码如下：

* **GetLocalFilesActivity.java  选择附件界面**

```

/**
 * Created by CnPeng on 2016/12/14.
 * <p>
 * 获取本地文件，并用VP 分别显示
 */

public class GetLocalFilesActivity extends FragmentActivity implements View.OnClickListener {
    private List<String>        titles;    //标题集合
    public  List<LocalFileBean> selectedList; //被选中的集合,定义在这儿直接让VP内的FM调用并赋值
    public List<LocalFileBean> words           = new ArrayList<>();//本地文档集合,定义在这儿直接让VP内的FM调用并赋值
    public List<LocalFileBean> pics            = new ArrayList<>();//本地图片集合,定义在这儿直接让VP内的FM调用并赋值
    public List<LocalFileBean> videos          = new ArrayList<>();//本地视频集合,定义在这儿直接让VP内的FM调用并赋值
    public List<LocalFileBean> audios          = new ArrayList<>();//本地音频集合,定义在这儿直接让VP内的FM调用并赋值
    public List<LocalFileBean> newSelectedList = new ArrayList<>();//新被选中的集合,定义在这儿直接让VP内的FM调用并赋值

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_get_local_file_vp);

        //拿到上个activity的集合
        selectedList = (List<LocalFileBean>) getIntent().getSerializableExtra("uploadFilesList");

        initData();

        initView();
    }

    private void initData() {
        titles = new ArrayList<>();
        titles.add("全部");
        titles.add("文档");
        titles.add("图片");

    }

    private void initView() {
        //初始化标题
        ThreeWightTitleLayout twtl_selectFiles = (ThreeWightTitleLayout) findViewById(R.id.twtl_selectFiles);
        ThreeWightTitleLayout.initTitle(getString(R.string.uploadFiles));
        ThreeWightTitleLayout.initFuncText(getString(R.string.upload));
        ThreeWightTitleLayout.initFuncImage(0);     //取消右上角图片

        //右上角功能按钮
        LinearLayout llbtn_Upload = (LinearLayout) findViewById(R.id.func_list_img);
        llbtn_Upload.setOnClickListener(this);

        ViewPager vp_localFiles = (ViewPager) findViewById(R.id.vp_LocalFiles);
        TabLayout tabLayout = (TabLayout) findViewById(R.id.tb_LocalFiels);

        //已经从代码中设置了tabIndicatorColor属性，所以这里就不需要了
        //        tabLayout.setSelectedTabIndicatorColor(getResources().getColor(R.color.eba338));

        FragmentManager manager = getSupportFragmentManager();
        LocalFilesVPAdapter adapter = new LocalFilesVPAdapter(titles, manager);
        vp_localFiles.setAdapter(adapter);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.func_list_img:        //"上传"的点击事件
                //封装Fragment中被选中的数据，传递给上一个界面, 并关闭当前activity。先判断是否已选择数据
                //                if (selectedList.size() == 0) {     //没选中数据，给出提示
                if (newSelectedList.size() == 0) {     //没选中数据，给出提示
                    ToastUtil.toastShort(getString(R.string.upload_noAttachment));
                } else {
                    LogUtils.e("上传--传递数据给上一级Activity", "此时被选中的集合数据大小" + selectedList.size());
                    LogUtils.e("上传--传递数据给上一级Activity", "此时被选中的new集合数据大小" + newSelectedList.size());
                    Intent intent = new Intent();

                    intent.putExtra("SelectedFilesList", (Serializable) newSelectedList);  //强转，传递数据
                    setResult(RESULT_OK, intent);
                    finishActivity();   //关闭当前页面
                }
                break;
        }
    }

    public void finishActivity() {
        finish();
    }

}
```
>上面代码中ThreeWightTitleLayout 是自定义的顶部标题栏控件， LogUtils 是自定义的吐司工具类


* **LocalFilesFragment.java  展示数据的Fragment**

```
/**
 * Created by CnPeng on 2016/12/14.
 * <p>
 * 展示文件列表的Fragment
 */
public class LocalFilesFragment extends Fragment {
    private EmptyListViewPromptView elvpv_getLocalFiles;  //加载数据时的提示布局
    private View                    view;                 //Fragment的界面布局
    private UploadFilesLvAdapter    adapter;
    private int                     positon;    //当前是VP 的第几个位置
    private ListView                listView;   //展示内容的lv
    private List<LocalFileBean>     selectedList;   //被选中的全部
    private List<LocalFileBean>     newSelectedList;   //被选中的全部
    private             List<LocalFileBean> filesList = new ArrayList<>();  //文件集合
    public static final int                 LOCALFILE = 3; //创建适配器时使用，用来区分是否显示上传进度
    public static final int                 MSG_ALL   = 0;    //发送消息，区分是那种类型的文件,0 全部
    public static final int                 MSG_DOC   = 1;    //发送消息，区分是那种类型的文件,1 文档
    public static final int                 MSG_IMAGE = 2;    //2 图片
    ContentResolver contentResolver;

    /**
     * 创建Fragment对象，并传递数据
     *
     * @param index 当前展示的是VP的第几个页面，用来创建不同的Fragment
     * @return fragment
     */
    public static LocalFilesFragment newInstance(int index) {
        LocalFilesFragment fragment = new LocalFilesFragment();
        Bundle args = new Bundle();
        args.putInt("position", index);
        fragment.setArguments(args);
        return fragment;
    }

    @Override   //获取传递的数据
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //获取传递的数据，根据position判断要显示的是图片，还是全部，还是文档      
        positon = getArguments() != null ? getArguments().getInt("position") : 0;

        contentResolver = getActivity().getContentResolver();
    }

    @Override   //填充布局
    public View onCreateView(LayoutInflater inflater, final ViewGroup container, Bundle savedInstanceState) {
        if (null == view) {     //加这个判断，是为了避免 onCreateView 重复执行 

            //获取activity中定义的被选中的集合，实现Activity与Framgment之间的数据传递
            selectedList = ((GetLocalFilesActivity) getActivity()).selectedList;
            newSelectedList = ((GetLocalFilesActivity) getActivity()).newSelectedList;

            view = inflater.inflate(R.layout.fragment_get_local_file, container, false);
            listView = (ListView) view.findViewById(R.id.lv_documents);

            elvpv_getLocalFiles = (EmptyListViewPromptView) view.findViewById(R.id.elvpv_getLocalFiles);
            elvpv_getLocalFiles.setPromptText(getString(R.string.CommonHit_Loading));
            elvpv_getLocalFiles.setVisibility(View.VISIBLE);

            //条目点击事件        
            listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
                @Override
                public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
                    LocalFileBean bean = filesList.get(i);
                    boolean flag = bean.isChecked();    //获取之前的状态

                    int size = bean.getFileSize();   //拿到的单位是B 

                    if (size > 10 * 1024 * 1024) {   //不大于10
                        ToastUtil.toastShort(R.string.hint_uploadFileIsLarge);
                        return; //弹完吐司之后，不往集合添加
                    }

                    //控制数量不能超过5个
                    if (!flag && selectedList.size() >= 5) {
                        ToastUtil.toastShort("最多只能选择五个文件");
                    } else {
                        flag = !flag;   //取反

                        bean.setChecked(flag);
                        adapter.notifyDataSetChanged();

                        //将被选中的数据存进单独的集合
                        if (flag) {
                            selectedList.add(bean);    //选中的存
                            newSelectedList.add(bean);
                        } else {
                            selectedList.remove(bean);  //未选中的移除
                            newSelectedList.remove(bean);  //未选中的移除
                        }
                        //                        LogUtils.e("被选中的数据有多少？", selectedList.size() + "");
                        //                        LogUtils.e("New被选中的数据有多少？", newSelectedList.size() + "");
                    }
                }
            });
            fillData();
        }
        return view;
    }

    private void fillData() {
        // 搜索本地文件，是耗时操作，开启子线程，去后台加载数据               
        new Thread(new Runnable() {
            @Override
            public void run() {

                List<LocalFileBean> words = ((GetLocalFilesActivity) getActivity()).words;
                List<LocalFileBean> pics = ((GetLocalFilesActivity) getActivity()).pics;
                List<LocalFileBean> videos = ((GetLocalFilesActivity) getActivity()).videos;
                List<LocalFileBean> audios = ((GetLocalFilesActivity) getActivity()).audios;
                
                synchronized (words) {  //同步代码块，以集合为锁对象，保证同一时间只有一个线程可以操作该集合
                    if (!(words.size() > 0)) {
                        //这里要用addAll(),保证words 的对象地址不变；如果用= 地址就变了，就会导致数据不能同步，每次都是新获取
                        words.addAll(GetLocalFileUtils2.getAllWords(contentResolver));
                    }
                }
                synchronized (pics) {
                    if (!(pics.size() > 0)) {
                        pics.addAll(GetLocalFileUtils2.getAllPictures(contentResolver));
                    }
                }

                synchronized (videos) {
                    if (!(videos.size() > 0)) {
                        videos.addAll(GetLocalFileUtils2.getAllVideos(contentResolver));
                    }
                }
                synchronized (audios) {
                    if (!(audios.size() > 0)) {
                        audios.addAll(GetLocalFileUtils2.getAllMusic(contentResolver));
                    }
                }
                switch (positon) {
                    case 0:
                        filesList.addAll(words);
                        filesList.addAll(pics);
                        filesList.addAll(audios);
                        filesList.addAll(videos);
                        handler.sendEmptyMessage(MSG_ALL);  //全部中集合文件和文档
                        LogUtils.e("123", "全部--查找完毕" + filesList.size());
                        break;
                    case 1: //文档页面
                        //获取数据，并添加到集合,拿到数据后发送空消息通知
                        filesList.addAll(words);
                        handler.sendEmptyMessage(MSG_DOC);
                        LogUtils.e("123", "文档--查找完毕" + filesList.size());
                        break;
                    case 2:
                        filesList.addAll(pics);
                        filesList.addAll(audios);
                        filesList.addAll(videos);
                        handler.sendEmptyMessage(MSG_IMAGE);
                        LogUtils.e("123", "图片/视频/音频--查找完毕" + filesList.size());
                        break;
                }
            }
        }).start();
    }

    //处理本地搜索结果，设置LV适配器，展现数据 ，并关闭对话框
    Handler handler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            if (filesList != null && filesList.size() > 0) {
                //有相关数据时，直接隐藏提示布局
                elvpv_getLocalFiles.setVisibility(View.INVISIBLE);
                adapter = new UploadFilesLvAdapter(filesList, LOCALFILE);
                listView.setAdapter(adapter);
            } else {
                // 没有数据时，给出空布局提示          
                elvpv_getLocalFiles.setPromptText(getString(R.string.CommonHit_NoData));
                elvpv_getLocalFiles.setVisibility(View.VISIBLE);
            }
        }
    };
}
```
>**A**
上面代码中，核心部分是fillData() 方法。
**B**
EmptyListViewPromptView 是自定义的数据为空时的提示控件
**C**
为了方便外部在创建Fragment的时候传递数据给Fragment , 封装了一个 newInstance() 方法，该方法中将要传递的数据封装到bundle,然后setArguments()给Frgament, 最终返回一个fragment对象。
**D**
同步锁 锁定的是 对象，所以，想锁定哪个对象，就将对象作为同步锁对象
**E**
从Fragment中获取它所依附的Activity 中的数据时，这里直接使用的是getActivity(), 然后强转为所在的Activity类，然后再获取数据。



* **LocalFilesVPAdapter.java  ViewPager 的适配器**
```
/**
 * Created by CnPeng on 2016/12/14.
 * <p>
 * LocalFiles_VP 的适配器
 */
public class LocalFilesVPAdapter extends FragmentPagerAdapter {
    private final List<String> titles;

    public LocalFilesVPAdapter(List<String> titles, FragmentManager manager) {
        super(manager);
        this.titles = titles;
    }

    @Override   //获取具体的view，这里使用的是Fragxment
    public Fragment getItem(int position) {
        return LocalFilesFragment.newInstance(position);
    }

    @Override
    public int getCount() {
        return titles.size();
    }

    @Override   //返回VP的标题
    public CharSequence getPageTitle(int position) {
        return titles.get(position);
    }
}
```

**补充：
在向Frgament中传递数据的时候，官方推荐使用setArguments() 。但是，为什么推荐使用setArguments()  而不是直接通过 有参构造传递数据呢 ？因为当Fragment所依赖的Actvitiy重新创建的时候（比如横竖屏切换的时候），会先销毁已有的Fragment对象，然后调用Fragment的空参去重新创建Frament 对象这就导致了数据的丢失。而使用setArguments（）传递的数据则不受影响。**[详细原因可以参考：https://yq.aliyun.com/articles/12368 ](https://yq.aliyun.com/articles/12368)

关于TabLayout的基本使用，可以参考我的另一篇文章：[http://www.jianshu.com/p/c1aa1056176b](http://www.jianshu.com/p/c1aa1056176b)
