# 1. 8-在Wxml中遍历对象或者map的key和value

[原文链接](https://blog.csdn.net/KevinsCSDN/article/details/80351438/)

例如js文件中有如下数据：

```js
data: {
      someone:{ 
              name: "张三", 
              attrs: {'身高': '178cm', '体重':'60kg', '胸围': '36C', '腿长': '1m', '臂长': '60cm'} 
          }
  },
```

用 java 的话，也就是一个对象 someone，包含一个 string 属性 name 和一个 Map 属性 attrs，后面的 map 是动态可变的，不同的对象 map 中的元素个数可能不同.

现在根据需求需要在 wxml 中显示这个 map 中的 key 和 value 。废话不多说，代码如下：

```xml
<view class='attrs' wx:for='{{someone.attrs}}' wx:for-index='key'>
      <text>{{key}}:{{item}}</text>
</view>
```