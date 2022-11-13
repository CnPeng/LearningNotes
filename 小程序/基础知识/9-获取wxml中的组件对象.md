# 1. 9-获取wxml中的组件对象

[原文链接](https://blog.csdn.net/philler/article/details/104811504)

在 wxml 中指定组件的 id 或者 class ：

```wxml
<my-get-obj class="my_get_obj"/>
<button bindtap="getObj">获取组件对象</button>
```

在页面中调用 `this.selectComponent(组件的id或者类)` 获取组件对象：

```js
getObj(){
    const cpn = this.selectComponent(".my_get_obj");
    console.log(cpn);
  }
```

然后修改组件对象中的数据有两种方式

* 通过获取的组件对象的setData方式修改组件对象中的数据（不推荐）
* 通过调用组件的中的方法修改组件对象中的数据（推荐）
