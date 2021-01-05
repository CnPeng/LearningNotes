wxml 文件如下：

```xml
<view>
  <text class="title">hello {{name}}</text>
  <button bind:tap="buttonHandler">点击</button>
  
  <!-- 也可以这么写 -->
  <!-- <button bindtap="btnClickHandler">点击修改</button> -->
</view>
```

点击按钮时，提示: `does not have a method "btnClickHandler " to handle event "tap".`

问题原因：`btnClickHandler` 方法写在了 `data` 中，js 文件如下：

```js
// 注意，这是错误写法！！！
Page({
  data: {
    name: '张三',
    buttonHandler(event) {
   		 this.setData({
      			name: '李四'
    		});
  	}
  }
});
```

正确写法：**方法应该和 data 平级**, js 文件如下：

```js
// 这是正确的写法
Page({
  data: {
    name: '张三'
  },
  
  
  buttonHandler(event) {
    this.setData({
      name: '李四'
    });
  }
});
```
