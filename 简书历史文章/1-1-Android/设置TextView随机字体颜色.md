####设置随机字体颜色
* 安卓中的字号一般都是12 或者14以上

* 通过rgb设置随机颜色值

```
//获取随机rgb颜色值
public static intrandomColor(){
    Random random = new Random();
    int red =random.nextInt(150);//0-190        ,如果颜色值过大,就越接近白色,就看不清了,所以需要限定范围
    int green =random.nextInt(150);//0-190
    int blue =random.nextInt(150);//0-190
    return Color.rgb**(red,green, blue);//使用rgb混合生成一种新的颜色,Color.rgb生成的是一个int数
}
//设置随机颜色
textView.setTextColor(ColorUtil.randomColor());
```

#### setTextColor（int color）使用误区
这里注意，setTextColor需要的是一个int类型的颜色值，而平时大家在使用时，往往容易直接将定义的color的resId传进去，
这时候系统会将resId对应的int值作为颜色值，此时正确的使用方法应该是 context.getResource().getColor(resId)。
