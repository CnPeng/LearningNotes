# 1. 12-可滑动切换的tab选项卡

[原文链接](https://blog.csdn.net/Sophie_U/article/details/71745125)

![](https://img-blog.csdn.net/20170512153451676?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU29waGllX1U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

最终效果如上。问题：

* 1、tab标题总共8个，所以一屏无法全部显示。
* 2、tab内容区左右滑动切换时，tab标题随即做标记（active）。
* 3、当active的标题不在当前屏显示时，要使其能显示到当前屏中。

## 1.1. wxml结构

* tab标题因一排八个，所以使用 scroll-view 组件，使其可横向滚动。
* tab内容可左右滑动切换，使用swiper组件实现

为了偷懒，所以数据都通过 `wx:for` 遍历重复出来。

说明：

* 1、设置 data-current 属性用于：点击当前项时，通过点击事件 swichNav 中处理 e.dataset.current 取到点击的目标值。
* 2、swiper 组件的 current 组件用于控制当前显示哪一页
* 3、swiper 组件绑定 change 事件 switchTab，通过 e.detail.current 拿到当前页

```wxml
<view >
    <scroll-view scroll-x="true" class="tab-h" scroll-left="{{scrollLeft}}">
        <view class="tab-item {{currentTab==0?'active':''}}"  data-current="0" bindtap="swichNav">健康</view>
        <view class="tab-item {{currentTab==1?'active':''}}" data-current="1" bindtap="swichNav">情感</view>
        <view class="tab-item {{currentTab==2?'active':''}}" data-current="2" bindtap="swichNav">职场</view>
        <view class="tab-item {{currentTab==3?'active':''}}" data-current="3" bindtap="swichNav">育儿</view>
        <view class="tab-item {{currentTab==4?'active':''}}" data-current="4" bindtap="swichNav">纠纷</view>
        <view class="tab-item {{currentTab==5?'active':''}}" data-current="5" bindtap="swichNav">青葱</view>
        <view class="tab-item {{currentTab==6?'active':''}}" data-current="6" bindtap="swichNav">全部</view>
        <view class="tab-item {{currentTab==7?'active':''}}" data-current="7" bindtap="swichNav">其他</view>
    </scroll-view>

    <swiper class="tab-content" current="{{currentTab}}" duration="300" bindchange="switchTab"
     style="height:{{winHeight}}rpx">
        <swiper-item wx:for="{{[0,1,2,3,4,5,6,7]}}">

            <scroll-view scroll-y="true" class="scoll-h" >

                <block wx:for="{{[1,2,3,4,5,6,7,8]}}" wx:key="*this">
                    <view class="item-ans">
                        <view class="avatar">
                            <image class="img" src="http://ookzqad11.bkt.clouddn.com/avatar.png"></image>
                        </view>
                        <view class="expertInfo">
                            <view class="name">欢颜</view>
                            <view class="tag">知名情感博主</view>
                            <view class="answerHistory">134个回答，2234人听过 </view>
                        </view>
                        <navigator url="/pages/askExpert/expertDetail" class="askBtn">问TA</navigator> 
                    </view>
                </block>

            </scroll-view>

        </swiper-item>
    </swiper>
</view>
```

## 1.2. js部分

微信小程序在开发起来，个人感觉挺像 vue 的，以数据驱动视图的更新。所以在小程序中，不能直接操作dom，当然也不能使用jquery之类的库。

```js
var app = getApp();

Page({
    data:{
        winHeight:"",//窗口高度
        currentTab:0, //预设当前项的值
        scrollLeft:0, //tab标题的滚动条位置
        expertList:[{ //假数据
            img:"avatar.png",
            name:"欢顔",
            tag:"知名情感博主",
            answer:134,
            listen:2234
        }]
    },

    // 滚动切换标签样式
    switchTab:function(e){
        this.setData({
            currentTab:e.detail.current
        });
        this.checkCor();
    },

    // 点击标题切换当前页时改变样式
    swichNav:function(e){
        var cur=e.target.dataset.current;
        if(this.data.currentTaB==cur){return false;}
        else{
            this.setData({
                currentTab:cur
            })
        }
    },

    //判断当前滚动超过一屏时，设置tab标题滚动条。
    checkCor:function(){
      if (this.data.currentTab>4){
        this.setData({
          scrollLeft:300
        })
      }else{
        this.setData({
          scrollLeft:0
        })
      }
    },

    onLoad: function() {
        var that = this;
        //  高度自适应
        wx.getSystemInfo( {
            success: function( res ) {
                var clientHeight=res.windowHeight,
                    clientWidth=res.windowWidth,
                    rpxR=750/clientWidth;
              var  calc=clientHeight*rpxR-180;
                console.log(calc)
                that.setData( {
                    winHeight: calc
                });
            }
        });
    },

    footerTap:app.footerTap
})
```

## 1.3. wxss样式

```wxss

.tab-h {
  height: 80rpx;
  width: 100%;
  box-sizing: border-box;
  overflow: hidden;
  line-height: 80rpx;
  background: #F7F7F7;
  font-size: 16px;
  white-space: nowrap;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 99;
}

.tab-item {
  margin: 0 36rpx;
  display: inline-block;
}

.tab-item.active {
  color: #4675F9;
  position: relative;
}

.tab-item.active:after {
  content: "";
  display: block;
  height: 8rpx;
  width: 52rpx;
  background: #4675F9;
  position: absolute;
  bottom: 0;
  left: 5rpx;
  border-radius: 16rpx;
}

.item-ans {
  width: 100%;
  display: flex;
  flex-grow: row no-wrap;
  justify-content: space-between;
  padding: 30rpx;
  box-sizing: border-box;
  height: 180rpx;
  align-items: center;
  border-bottom: 1px solid #F2F2F2;
}

.avatar {
  width: 100rpx;
  height: 100rpx;
  position: relative;
  padding-right: 30rpx;
}

.avatar .img {
  width: 100%;
  height: 100%;
}

.avatar .doyen {
  width: 40rpx;
  height: 40rpx;
  position: absolute;
  bottom: -2px;
  right: 20rpx;
}

.expertInfo {
  font-size: 12px;
  flex-grow: 2;
  color: #B0B0B0;
  line-height: 1.5em;
}

.expertInfo .name {
  font-size: 16px;
  color: #000;
  margin-bottom: 6px;
}

.askBtn {
  width: 120rpx;
  height: 60rpx;
  line-height: 60rpx;
  text-align: center;
  font-size: 14px;
  border-radius: 60rpx;
  border: 1px solid #4675F9;
  color: #4675F9;
}

.tab-content {
  margin-top: 80rpx;
}

.scoll-h {
  height: 100%;
}
```