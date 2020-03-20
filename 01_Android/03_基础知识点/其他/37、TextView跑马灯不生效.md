TextView的文字内容动态设置，然后跑马灯不生效。

解决办法——代码中控制：
```
   // CnPeng 2018/7/10 下午2:53 解决跑马灯不生效的问题
        itemBinding.tvName.setSingleLine();
        itemBinding.tvName.setSelected(true);
        itemBinding.tvName.setEllipsize(TextUtils.TruncateAt.MARQUEE);
```

设置要点：
1、单行
2、选中(必须设置，否则即便设置了其他几项也不生效)
3、Marquee

注意：   itemBinding.tvName.setFocusable(true); 设置不设置都不影响marquee效果。
