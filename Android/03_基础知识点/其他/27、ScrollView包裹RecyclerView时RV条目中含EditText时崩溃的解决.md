## 1、问题现象
报错信息：`parameter must be a descendant of this view`


## 2、复现步骤：

ScrollView 中包裹一个不会滚的RV，Rv的条目中含有ET——ArticleContentEditAdapter的条目中包含EditText，然后先让ET获取焦点，然后条目上方的+，此时隐藏界面底部的4个按钮，这时候ScrollView会重新测量尺寸并重新绘制条目，但是焦点依旧被之前条目中的EditText所持有，然后界面就挂掉了。。。。。

## 3、问题原因：
http://blog.sina.com.cn/s/blog_5704bfaf0102v3bn.html

## 4、解决方案
解决方案是，在点击+后，先清除有焦点的控件，然后再去隐藏或展示底部的四个功能按钮即可

```
View focusedView = mBinding.scrollView.getFocusedChild();
if (null != focusedView) {
   focusedView.clearFocus();
}
```

**180521 补充**
> 最新发现，按照之前的方法，ET获取焦点时，点击非0索引的+没有问题；但是，点击0索引的+依旧会崩溃。解决方案是：在当前页面的跟布局中增加如下代码：

```
android:focusable="true"
android:focusableInTouchMode="true"
```


省略


具体文件：
ArticleContentEditAdapter
item_rv_article_edit.xml