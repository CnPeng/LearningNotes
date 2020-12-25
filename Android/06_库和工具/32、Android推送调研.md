# 1. Android推送调研

## 1.1. 名词说明

* **透传消息**：APP 存活情况下，推送服务直接把消息发送给APP应用，由APP自己选择如何处理，注意**透传的前提是APP存活** 。（透传消息可以不用接入第三方SDK，另外，Vivo 不支持透传)

* **通知栏消息**：展示在系统通知栏。APP 进程被杀死时，设备收到推送消息后，由系统弹出标准安卓通知，用户点击通知栏才激活应用


## 1.2. 调研结果总述

* 调研的推送通道：华为、小米、Vivo、OPPO、极光、UPA（统一推送联盟，Vivo 内集成的UPA）
* 预计开发周期：移动端周期大约 1 周（5个工作日）、后台周期（待定）、测试周期（待定）
* 开发完成后的优势：大幅提高消息送达率（Oppo 文档中描述，自己的推送 SDK 能达到 99.9% 的送达率）
* 开发完成后的弊端：App 体积增大（ 测试 DEMO 打包之后的体积大约 4 M ）、后续厂商 SDK 升级之后也需要对应升级

### 1.2.1. 推送测试结果统计

该表格展示的是使用厂商 SDK 和极光 SDK 时在 APP 不同状态下能否送达的情况。

* 离线：表示 APP 被杀死
* 存活：表示 APP 在后台，进程未被杀死
     
品牌|型号|离线-厂商|存活-厂商|离线-极光|存活-极光|备注
---|---|---|---|---|---|---|
OPPO | R15  | ✅  |  ✅   |  ❌  |  ✅  |
vivo | X9s | ✅  | ✅   | ❌  | ❌  |   
Realme | X2 | ✅  |  ✅  | ❌   | ✅    | 和 OPPO 是同一通道
小米 | 8 | ✅  | ✅  |  ❌   | ✅  |   
华为 | p40 | ✅  | ✅   | ❌  | ✅  |
荣耀 | 9x | ✅  | ✅  | ❌  | ✅  |
华为 | Mate20 | ✅  | ✅ | ❌  | ✅  | 
OPPO | R7 | ❌ | ❌  | ❌  | ❌  | 15年10月上市，系统是ColorOS 2.1 手机太老了
一加 | 5T | ❌  | ❌  | ❌  | ✅   | 一加使用的是 OPPO 通道，5T是17年12月上市，应该还没有适配。
三星 | Galaxy S8+ | ❌  | ✅   | ❌   | ✅   | APP  存活状态下，华为通道和小米通道都能送达

基于以上测试结果可以看出，在 APP 被杀死的情况下，极光推送送达率几乎为 0 （但是，当我们打开 APP 时，这些未送达的消息还可以收到）。我们集成厂商 SDK 之后，则可以解决该问题，保证消息能在 APP 被杀死的状态下依旧能送达到用户设备上。


### 1.2.2. 各厂商对推送的限制

厂商|单次请求推送设备数|单日单设备推送数|单日N设备推送数|消息体大小 | 备注
---|---|---|---|---|---|
华为 | 按 Token 发送时 1000（可以自己做循环推送） | 3000 限流，10W 封号 | 无限制 | 4k |  [《单日推送总量的问题》](https://developer.huawei.com/consumer/cn/support/feedback/#/ticketDetail?id=79177&time=2020-10-29%2000:49:43) 、[F & Q](https://developer.huawei.com/consumer/cn/doc/development/HMS-Guides/push-faq-v4#h1-1577152526378)、[华为推送的限制信息](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-Guides/restrictions-0000001050040064)
小米 | 1000 （可以自己做循环推送）| 无限制 | MIUI 日联网设备数小于1W时，每日推送限额数为5W；MIUI日联网设备数大于1W时，每日推送限额为 设备数*5 （跟应用类别有关，我们的 App 选的聊天类型，限制倍数是 5 ）| 4k | [推送限制说明（含 F & Q ）](https://dev.mi.com/console/doc/detail?pId=2086)；另外，通知栏消息又分为普通消息和通知消息。普通消息有前面的限制；通知消息无限制，但需要单独申请通道，用于推送重要内容，会被推送后台会监听，防止滥用
vivo | 单推 1个，群推 [2,1000] | 系统消息不受限制(但限制总量)，运营消息上限为5条。全推 1 条。 | ①消息分为运营消息和系统消息，运营消息的额度是1倍的sdk订阅数，系统消息的额度也是1倍的sdk订阅数 <br>②若您的sdk订阅数小于1万，那么您单日可发送系统消息总量=1万，若您的sdk订阅数大于1万，那么您可发送系统消息总量=sdk订阅数  |  | [《vivo推送消息分类功能说明》](https://dev.vivo.com.cn/documentCenter/doc/359#w1-36619128)  系统消息不足时可以根据该文档中的描述申请增量；虽然提供了全推，但每天只能推1条，不实用。
oppo| 单推1个；多推时支持全推，非全推时如果是RegId 和别名模式，最大 1000，标签(分组)模式未说明限制 | 无说明 |公信可推送数 <50000:100000 ; 公信可推送数 ≥50000:`累计用户数 * 2`。公信可推送数可以理解为 OPPO PUSH 累计用户  | 4k | 在设备联网的情况下，有效送达率可达99.99%以上。支持 ColorOS3.1及以上的系统的 oppo 的机型，一加5/5t及以上机型，realme 所有机型。支持的Android App包名称最大为128个字节。[私信通道](https://open.oppomobile.com/wiki/doc#id=10614)无条数显示，但需要单独申请，申请条件：OPPO PUSH的累计用户数≥300,000


### 1.2.3. 各厂商对定时推送的支持

平台|说明|是否可以取消|备注
---|---|---|---
小米 | 定时消息功能可以指定未来30天内任意时间作为消息下发时间  | 定时消息在消息开始推送之前可以通过推送运营平台-消息记录-详情 中取消  | 字段 `time_to_send`
华为 | 支持  | ——  | [推送功能介绍](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-Guides-V5/service-introduction-0000001050040060-V5) 中说支持定时推送，但是没有找到相关的 API 文档。
vivo | 开发者可以根据业务需求自定义消息推送时间，可以选择立即推送或指定时间推送。定时消息功能可以指定未来30天内任意时间作为消息下发时间。| 定时消息在消息开始推送之前可以通过消息推送平台-推送设置-待发送消息 中取消。| [VPush产品说明](https://dev.vivo.com.cn/documentCenter/doc/180)
oppo | 定时展示功能设置成功后消息即时下发，到达用户手机后并不直接展示出来，到了设置的定时展示时间，消息才展示出来 |不支持取消| [个性功能](https://open.oppomobile.com/wiki/doc#id=10742)


### 1.2.4. 推送方式对比

平台|支持的方式|说明|备注
---|---|---|---
小米  |  标签（Topic）、RegID、别名（Alias）、Useraccount  | Topic 用来标记一组用户; RegID 标记一个设备；alias与RegID(设备)一一对应, 可以使用用户的 APP 账号作为别名；Useraccount 适用于单账号多点登录同时接收消息 | [推送方式](https://dev.mi.com/console/doc/detail?pId=863)
华为 | token/topic/condition |  token 同其他厂商中的 RegID ;   | [下行消息——Message](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-References-V5/https-send-api-0000001050986197-V5)
oppo | 标签(Topic)、RegID、Alias、全推 | Topic 用来标记一组用户；RegID 标记一个设备；Alias 通常是用户的唯一标识，与 RegId 一一对应 |[OPPO PUSH产品说明_推送方式](https://open.oppomobile.com/wiki/doc#id=10742)
vivo | regId，alias，tag(标签)| alias与RegID(设备)一一对应， | [推送方式](https://dev.vivo.com.cn/documentCenter/doc/180#w2-35731162)
极光| 标签(tag)、RegID、别名(alias) 、全推  | 每个用户只能指定一个别名，系统不限定一个别名只能指定一个用户。如果一个别名被指定到了多个用户，当给指定这个别名发消息时，服务器端 API 会同时给这多个用户发送消息 ；标签用于群发，限定一组/群人| [别名与标签 API](https://docs.jiguang.cn/jpush/client/Android/android_api/#api_3)



### 1.2.5. 如何统计到达率和点击率？

待补充，目前各平台对于这两个统计并没有明确的文档，需要在各文档中仔细翻找。


### 1.2.6. 影响送达率的因素？

* 手机系统中的设置（是否开启了通知权限，包括锁屏通知、通知提示音、通知优先级、横幅通知等内容。）
* 厂商兼容（仅集成小米、华为、vivo、oppo 四大厂商通道，其他厂商可能会有兼容问题。 ）
* 网络状态 ，用户设备是否离线（是否联网）。
    * 用户设备离线时，OPPO PUSH 平台只缓存一次用户的消息，如果用户在线后还发不通，PUSH 平台不再缓存。
* 推送数量是否超出了厂商限制（超出厂商对推送数量的限制时，消息无法送达）



## 1.3. 小米推送

[小米推送文档](https://dev.mi.com/console/doc/detail?pId=230)

[Android客户端SDK集成指南](https://dev.mi.com/console/doc/detail?pId=41)

[服务端Java SDK文档](https://dev.mi.com/console/doc/detail?pId=1278)

[推送运营平台使用指南](https://dev.mi.com/console/doc/detail?pId=1551)

### 1.3.1. 后台需要的工作

 [SDK 下载页面 ](https://admin.xmpush.xiaomi.com/zh_CN/mipush/downpage?version=2020092703&appId=2882303761518765392) 
 
 服务端没有 go 版本 SDK，需要我们根据 [《服务器API地址以及参数》](https://dev.mi.com/console/doc/detail?pId=1163#_8) 自己封装一个发送推送的服务端。


#### 1.3.1.1. 服务端对通知的控制

##### 1.3.1.1.1. 控制在手机中的显示效果

* 应用在前台的情况下，**通知消息到达客户端后是否弹出通知可以服务端来设置**。服务端调用 `Message.Builder` 类的 `extra(String key, String value) ` 方法将 `EXTRA_PARAM_NOTIFY_FOREGROUND` 的值设置为 "0" 或者 "1" 。"1"——app 会弹出通知栏消息； “0“——app 不会弹出通知栏消息。
 
 * 通知消息的提醒类型可以由服务端通过  `Message.Builder` 的 `notifyType` 进行设置，可以是多种提醒类型的组合。而且可以通过 `Message.Builder` 类的 `extra(String key, String value)` 方法将 `Constants.EXTRA_PARAM_SOUND_URI` 的值设置为对应通知声音的 URI。如：

```go
.extra(Constants.EXTRA_PARAM_SOUND_URI, "android.resource://" + PACKAGENAME + "/raw/shaking")
```

##### 1.3.1.1.2. 控制点击事件

通知消息的点击行为，分为自定义点击行为和预定义点击行为。

使用自定义点击行为时，用户点击了客户端弹出的通知消息后会将封装消息的 `MiPushMessage` 对象通过 `PushMessageReceiver` 继承类的 `onNotificationMessageClicked` 方法传到客户端。由客户端控制页面跳转逻辑（不忘忘了给 Intent 添加 `NEW_TASK` 的 flag）。

但是，如果客户端不想写页面跳转逻辑，则可以由服务端通过 `Message.Builder` 类的 `extra(String key, String value)`方法定义跳转逻辑。这种方式就叫做预定义跳转。预定跳转的 key 为 `Constants.EXTRA_PARAM_NOTIFY_EFFECT`,  value 取值如下：

取值|说明
---|---
`Constants.NOTIFY_LAUNCHER_ACTIVITY `| 打开当前app对应的Launcher Activity。
`Constants.ACTIVITY` | 打开当前app内的任意一个Activity。
`Constants.NOTIFY_WEB` | 打开网页。

##### 1.3.1.1.3. 控制消息到达时的提醒方式

关键字 `notify_type`


##### 1.3.1.1.4. 控制接收消息的设备类型

关键字|说明
---|---
`extra.model` | 能接收到消息的设备机型 、品牌、价格范围，同类中的多个值使用逗号分隔。
`extra.model_not_in` | 无法收到消息的设备的机型范围，逗号分隔。

##### 1.3.1.1.5. 控制接收消息的 APP 版本

关键字| 说明
---|---
`extra.app_version`  | 可以接收消息的 app 版本号，用逗号分割。安卓 app 版本号来源于 manifest 文件中的 `android:versionName` 的值。
`extra.app_version_not_in` | 无法接收消息的app版本号，用逗号分割。




### 1.3.2. 移动端的工作

支持透传消息和通知栏消息，我们重点关注通知栏消息。

通知栏消息到达时会到达 `PushMessageReceiver` 子类的 `onNotificationMessageArrived` 方法，用户点击之后再传给您的`PushMessageReceiver` 的子类的 `onNotificationMessageClicked` 方法。

对于应用在前台时不弹出通知的通知消息，SDK 会将消息通过广播方式传给 `AndroidManifest` 中注册的 `PushMessageReceiver` 的子类的 `onNotificationMessageArrived` 方法（在 MIUI 上，如果没有收到 `onNotificationMessageArrived` 回调，是因为使用的 MIUI 版本还不支持该特性，需要升级到 MIUI7 之后。非 MIUI 手机都可以收到这个回调）。

#### 1.3.2.1. 前置工作

参考 [《推送服务启用指南》](https://dev.mi.com/console/doc/detail?pId=68) 中的说明创建 app 并启用对应的推送服务。

然后在 [SDK 下载页面 ](https://admin.xmpush.xiaomi.com/zh_CN/mipush/downpage?version=2020092703&appId=2882303761518765392) 中下载对应的 SDK

#### 1.3.2.2. SDK 接入

将下载下来的 `MiPush_SDK_Client_3_8_2.jar` 拷贝到项目中 `libs` 目录下，然后在项目中右击该 jar 文件，在弹出菜单中选择 `Add As Library ...`

然后按照 [文档](https://dev.mi.com/console/doc/detail?pId=41) 操作即可，小米的文档比较清晰。

关于推送对象（即向哪些设备推送）和 推送消息类型等信息可以参考 [《Android版快速接入指南》](https://dev.mi.com/console/doc/detail?pId=100)

**注意事项：**

* 如果将推送的初始化放在自定义的 Application 中，不要忘了将 `AndroidManifest.xml` 中的 Application 替换。
* 在非 MIUI 平台下，如果 `targetSdkVersion>=23`，需要动态申请电话和存储权限，请在申请权限后再调用注册接口，否则会注册失败。
* 如果我们需要定向、分类推送，在小米推送中可以借助 MiPushClient 的 setAlias、setUserAccount、subscribe 的三个方法对目标用户进行分组/分类。



### 1.3.3. F&Q

[小米推送常见问题汇总](https://dev.mi.com/console/doc/detail?pId=1292)

[推送限制说明（含 F & Q ）](https://dev.mi.com/console/doc/detail?pId=2086)

### 1.3.4. 错误码说明

[服务端错误码说明](https://dev.mi.com/console/doc/detail?pId=1557)

## 1.4. 华为推送

[华为推送文档](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-Guides-V5/android-dev-process-0000001050263396-V5)


### 1.4.1. 后台需要的工作

[服务端 API](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-References-V5/https-send-api-0000001050986197-V5)

[服务端示例代码](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-Examples-V5/server-sample-code-0000001050986079-V5) ， 示例代码中有 Go 版本，测试时直接基于该测试代码运行的。

需要注意，在示例代码中，`pushcomon.go` 中的 token 是安装了应用的设备在 Service 的 `onNewIntent()` 中获取到 token 信息。不能随便填，否则推送不成功。实际上线时，该 token 需要由客户端发送给服务端，然后服务端存储，使用的时候取出来，然后依据这些 token 发送通知消息。

### 1.4.2. 移动端的工作

[客户端示例代码](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-Examples-V5/client-sample-code-0000001051066000-V5)

> 在编写 Demo 时没有使用客户端示例代码，是完全从头开始的。

#### 1.4.2.1. 编码前的准备

* 先配置项目签名信息并开通推送服务——[《HUAWEI HMS Core集成准备》](https://developer.huawei.com/consumer/cn/codelab/HMSPreparation/index.html#0)

* 然后添加推送依赖信息（部分内容与上一步重复）——[《集成HMS Core SDK》](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-Guides/android-integrating-sdk-0000001050040084)

* 配置清单文件，注册自定义 Service——[《配置Manifest文件》](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-Guides/android-cfg-manifest-0000001050042037)

下面的内容都是从  [《客户端开发指导》](https://developer.huawei.com/consumer/cn/doc/HMSCore-Guides-V5/android-client-dev-0000001050042041-V5#ZH-CN_TOPIC_0000001050042041__section876955375919) 中摘录的主要内容。

#### 1.4.2.2. 获取 Token

每个设备上的每个应用的 Token 都是唯一存在的，客户端向服务端请求应用的唯一标识：Push Token，开发者的服务器根据这个 Token 推送消息。

获取该 Token 是有手动方式，也有自动方式，官方推荐使用自动方式。自动方式申请到的 Token 会在 `HmsMessageService` 的 `onNewIntent` 回调中返回。

有两种方式可以开启自动获取 token  的能力：

方式一:


在 `<application>` 标签中添加 `<meta-data>` 元数据，参考如下，`<meta-data>元` 数据 “name”需要固定为 “push_kit_auto_init_enabled”， “value”为 “true”即为启用了自动初始化。 “value”为 “false”即代表未启用自动初始化。

```xml
    <meta-data
        android:name="push_kit_auto_init_enabled"
        android:value="true"/>
```

方式二:

应用在 **主Activity** 类中显式调用 `setAutoInitEnabled(boolean enable)` 方法，设置为true即为启用了自动初始化。设置后值会保存在应用本地的“shared_prefs”目录下的文件中。


#### 1.4.2.3. 设置是否显示通知栏消息

通知栏消息是由系统直接在通知中心下拉列表呈现的即时消息。系统默认允许显示通知栏消息。——所以，我们不需要做处理。

#### 1.4.2.4. 消息点击——打开首页


##### 1.4.2.4.1. 消息体的数据结构

服务端发送消息内容给客户端，消息内容的完整数据结构参考 [《服务端API——下行消息》](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-References-V5/https-send-api-0000001050986197-V5)

数据结构的示例如下：

```json
{
    "message": {
        "data": "{ 'score': '7', 'time': '16:42' }",
        "notification": {
            "title": "message title",
            "body": "message body"
        },
        "android": {
            "data": "{ 'androidData': '7', 'time': '16:42' }",
            "notification": {
                "click_action": {
                    "type": 3
                }
            }
        },
        "token": [
            "pushtoken1"
        ]
    }
}
```

**注意：**

* 返回数据中的 android 字段表示安卓消息推送控制参数，对于安卓通知栏消息，本字段必填。
* 另外，android 字段中的部分字段会覆盖 `message.data` 和 `message.notification` 中内容，具体查看服务端的 API 说明

##### 1.4.2.4.2. 清单文件中注册 LaunchActivity

```xml
<activity android:name=".MainActivity">
    <intent-filter>
        //'name'值不可改变
        <action android:name="android.intent.action.MAIN"/>
        <category android:name="android.intent.category.LAUNCHER"/>
    </intent-filter>
</activity>
```

##### 1.4.2.4.3. App 的 LaunchActivity 中接收数据

华为推送的消息最终都是通过 intent 推送给 Activity 的。

理论上来讲，如果我们想向 APP 内的子页面跳转的话，可以通过 intent 中的数据实现。

另外，华为推送中还提供了向指定页面直接跳转的逻辑。可以参考官方文档中的示例。


### 1.4.3. 华为推送 F&Q

[开发过程中的相关问题说明（含服务端和客户端）](https://developer.huawei.com/consumer/cn/doc/development/HMS-Guides/push-faq-v4#h1-1577152526378)

摘录主要问题如下：

#### 1.4.3.1. 设备接收华为推送消息的条件？

* 设备：华为手机支持，非华为手机需安装 HMS Core（APK）（APK可前往 [华为应用市场](https://appdl-drcn.dbankcdn.com/dl/appdl/application/apk/a2/a27070ad7c944ee7bcf1574526b7898e/com.huawei.appmarket.2010231801.apk?sign=mw@mw1603868945895&maple=0&distOpEntity=HWSW) 搜索下载）；（注意，非华为手机安卓 HMS 后大概率会被系统杀死）
* 网络：手机处于联网且网络状态稳定；
* EMUI版本：部分EMUI4.0、EMUI4.1，以及 EMUI5.0 及之后的华为手机，建议EMUI5.0以上；

受 Android API 的限制，海外低版本的华为手机和非华为手机推送只能在Android API level 21（Android 5.0）以上使用；

#### 1.4.3.2. Push开发者一次性最多可以 push 多少个用户？

服务端限制1000个，如果多于这个数字，应用的服务器需要自己做循环。

#### 1.4.3.3. Push 服务在推送次数和频率方面有什么限制？如何防止开发者对用户造成骚扰，管理手段是什么？

每天向某个设备的某个App发送不超过 3000 条，超过3000条进行限流，超过10W条直接封号。

#### 1.4.3.4. 服务端发送推送消息后，为什么客户端无法接收到？

可能原因：

* 发送消息类型。若消息类型为透传消息，在应用进程被杀死时消息可能无法送达；

* 终端与网络状况。确保终端处于稳定网络下，且设置了允许通知；

* EMUI版本。检查手机EMUI版本，EMUI 版本低于 4.1 或者 HMS Core（APK）版本较低均不支持展示推送消息；

* 检查手机是否为 EMUI8.0 系统，如果是早期的 EMUI8.0，编译构建 App 时 targetSdkVersion 需保证低于26，或者进行系统升级（设置—系统—软件更新，不同手机型号导航可能略有差异）；

* 排除限流情况。每天可向单个设备最多发送3000条，超过后会被限流24小时，超过10万条会直接封禁推送权益；

* 缓存问题。HMS Core（APK）的缓存问题，清理应用和HMS Core（APK）数据及缓存，重启手机测试。

* 检查终端 token 是否正确。尝试在 AppGallery Connect 进行推送，如果 AppGallery Connect 后台也推送失败则是 token 已失效、或者手机配置有问题，需进行 token 检查和手机权限设置。如果能推送成功，则为调用接口时参数配置错误导致。

* 检查服务端封装的消息体格式。建议参考服务端API进行消息体字段排查。

### 1.4.4. 错误码说明

[HMS Core Push SDK 错误码说明](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-References-V5/error-code-0000001050255690-V5)

## 1.5. vivo 推送

* [Vivo推送文档](https://dev.vivo.com.cn/documentCenter/doc/180)
* [推送运营平台](https://vpush.vivo.com.cn/#/homePage/appDetail)
* [vivo推送常见问题汇总](https://dev.vivo.com.cn/documentCenter/doc/156)
* [名词解释](https://dev.vivo.com.cn/documentCenter/doc/174)
* [vivo推送消息分类功能说明](https://dev.vivo.com.cn/documentCenter/doc/359#w1-36619128)

对接 vivo 推送时，我们需要关注两个重点事项：

* 系统消息是否被误判为运营消息，误判的话，会影响商家响应订单的时效
* 做好系统消息推送量的统计系统消息总量不足时，需要及时向 vivo申请扩容。

上述两个问题的解决方案没想好。。。

### 1.5.1. 名词解释

* [《名词解释》](https://dev.vivo.com.cn/documentCenter/doc/174)

部分摘录如下：

名称|说明
---|---
registerId |registerId简称regid，regId是指集成了Vpush SDK的该应用apk安装到用户手机上并调用Vpush SDK提供的API成功注册Vpush的后，由Vpush后台返回给客户端的当前设备当前应用的唯一标识。
 别名（alias） | 开发者为设备设置的名称，一般为应用内的单点登录账号或其他用来标识用户的信息。如果多个设备被设置同一个别名，别名将只在最后一个设置的设备上生效。
单推 | 一条消息只对一个regid推送。
群推 | 对应用下已订阅Vpush的指定用户群进行推送。“群推消息体”指调用API接口保存的群推消息体，数量上限默认1000，随SDK订阅数增长。
全推 | 对应用下已订阅Vpush的全部用户进行推送。
标签推 | 对应用下已订阅Vpush的设置了标签的用户进行推送。
无效ID | 指注册失败未订阅、alias/regID/tag/clientid/openid无效。如果应用调用了turnoffPush()，或者在funtouch os上卸载了，或者超过3个月都没有和vivo服务器建立过长连接，则会判定ID失效。
用户解订阅 | 指的是用户卸载以及app主动turnOffPush()。
**Push SDK订阅数** | 指该应用apk集成push-sdk成功的数量。用户卸载应用或者手机90天内不联网，则订阅失效。SDK目前的策略是不限制单推和群推的比例，可发送的单推和群推消息指定的用户量不得超过每日限制的推送总量，保底10000。SDK订阅数和可发送的用户总量可以在[《开发者后台--用户数据》](https://vpush.vivo.com.cn/#/secondPage/userStats) 查看， 全推消息计算在内。
当日运营消息总量 | 运营消息一般由非用户主动订阅或行为产生，主要帮助APP进行营销促活，包括但不限于广告、营销、推荐、推广、活动等对用户有主动运营作用的消息。
当日系统消息总量 | 系统消息一般由用户主动订阅或行为产生，可以帮助用户完成app功能的使用，包括但不限于日程、订单、物流、待办等。


### 1.5.2. vivo 推送消息分类

Vivo 推送中，消息需要做分类，通过分类可以扩大每日推送的消息数量。可以分为运营消息和系统消息，具体参考 [vivo推送消息分类功能说明](https://dev.vivo.com.cn/documentCenter/doc/359#w1-36619128)

简单理解：系统消息为用户强相关的消息，运营消息为公共类消息


#### 1.5.2.1. 系统消息说明

默认1倍SDK订阅数的系统消息量级（即系统消息总量 = SDK 订阅数）。可按照  [vivo推送消息分类功能说明](https://dev.vivo.com.cn/documentCenter/doc/359#w1-36619128) 文档中 3-3 的模板申请扩容。

系统消息只能通过 API 接口创建，web 端推送运营平台暂不支持此功能。

Vivo 推送内容会对系统消息进行智能分类，智能分类过程中可能会将系统消息自动修正为运营消息，并扣除运营消息额度；若识别为系统消息，则从系统消息总量扣除额度。

若智能分类过程中发生误判，需要自己通过邮件按照指定模板进行申诉。邮件地址：push@vivo.com，模板内容参考 参考 [vivo推送消息分类功能说明](https://dev.vivo.com.cn/documentCenter/doc/359#w1-36619128) 中的 2-2 节。

> 问题：我们怎么才能准确及时的获知系统消息被误判为运营消息呢？

系统消息包含以下 8 类：

类别 | 说明
---|---
邮件 | 邮件的发送、接收状态信息
用户设置的提醒 | 用户在app内主动设置的提醒信息并确认会收到推送，如：设置的签到打卡提醒；行程通知、出行服务生成的检票、到达、延误、晚点等行程通知；主动设置的开播提醒、书籍更新；设置的商品或机票降价、商品开团提醒
物流 | 物流节点信息，包括收发货，派送，签收，取件通知等
订单 | 生成订单相关信息  如：下单成功、卖家收到新订单、订单详情、订单状态、物流状态、订单售后、订单投诉处理进度、开票信息等；
待办待阅读 | 待处理业务流程，如：审核进度提醒、认证状态、等流程提醒；库存不足、售罄提醒、商品下架通知；工单处理、限制提现、客诉警告、店铺限制、商品黑名单、交易违规、涉假/涉欺诈发货通知；常旅信息到期、证件过期提醒等
财务 | 包括收付款、银行到账&扣款、催缴&退款信息、充值，涉及金额、账单、交易提醒、贷款受理进度、还款/逾期提醒、资金冻结提醒、资金限制提醒、缴纳保证金提醒、红包到账（非促销类红包）等
功能提醒 | 用户操作了系统功能后的结果通知，如：“共享单车已关锁还车，点击查看详情”，“小钱袋定期转入失败：系统异常”
即时消息 | 聊天消息（私信、群聊），不包括官方号或者商家批量推送给用户的私信、广告

不允许通过系统消息发送的内容（即以下内容归位运营消息）：

类别|说明
---|---
内容推荐 | 非用户主动订阅，APP向用户推送的资讯、内容。如：微博、资讯、新闻、点评、小说、广告、视频、音频、直播等等
陌生人推荐 | 大V、主播、异性、可能认识的人等
金融理财 | 金融理财产品推荐。   如：投资、贷款、股票、基金、贵金属等
促销活动及推荐 | 商品推广、宣传、折扣、红包、领劵、返现等优惠信息；推荐产品功能、服务更新，商品、商家、店铺推荐的内容，例如“您的授信额度增加”
业务到期提醒 | 提醒用户保险、医疗、水电到期续保续费，或者话费、会员等月结日充值提醒等服务续期类推送
运营活动 | 各类APP内非用户主动设置的需用户参与的活动提醒消息、小游戏提醒、服务或商品评价提醒等。 如：抽奖、积分、签到、任务、分享、偷菜、领金币等
天气 | 天气预警提醒
社交动态 | 用户之间的社交互动提醒，如：添加好友、被赞、被@、评论、留言、关注、转发
订阅信息 | 关注的人发布作品，如：关注的人直播提醒、动态更新


#### 1.5.2.2. 开启消息分类

系统消息只能通过 API 接口创建，web 端推送运营平台暂不支持此功能。通过 API 推送消息时，将字段 `classification` 的值设置为 `1` 即表示为系统消息，`0` 则表示运营消息，不设置时默认为 `0`。


### 1.5.3. 后台需要的工作

[PUSH-UPS-API接口文档](https://dev.vivo.com.cn/documentCenter/doc/362)

**注意：**

* 未上线的应用申请推送服务时，只有App上线后，才会自动变为审核通过，推送权限会变为  "正式"，从而能在生产环境推送正式消息；否则，一直显示审核中，且推送权限为“受限”
* 推送权限为“受限”的应用( 即非生产版本的 App 测试推送时 ) **只能通过 API 向在 [推送运营平台](https://vpush.vivo.com.cn/#/homePage/appDetail) 页面中添加的测试设备发送测试消息**。测试设备数量上限为20个。否则会一直报错：`10302 regId 不合法, regId为无效的regId, regId可能已经失效`

Vivo 的推送按接收群体可以分为三类：单推、群推、全推。各自有独立的接口。

#### 1.5.3.1. 鉴权（向 vivo 请求 token）

鉴权就是向 Vivo 发送一个请求获取 token，除鉴权接口以外，其他接口都需要在 Header 中携带该 Token.

一个 appId 可对应多个 token ，**24小时过期，业务方做中心缓存，定时更新一次**。（文档中推荐 1-2 或 3-4 小时更新一次）

限制：**一天限制调用不超过10000次。**


#### 1.5.3.2. 单推（推给单个用户）

携带消息内容以及 `用户regId（或alias）` 进行通知消息推送。针对每个用户发送不同的通知。

使用场景：如物流、订单状态、游戏预约状态、行程状态、聊天（如微信、评论）等。

限制：根据客户端 SDK 订阅数自动配置，可发送的用户总量可以在开发者后台查看。


#### 1.5.3.3. 群推（一次推送给多个用户）

使用场景：活动、系统升级提醒等。

限制：默认根据客户端SDK订阅数自动配置。

需要先通过  `/message/saveListPayload` 获取 taskId，然后触发 `/message/pushToList` 群推接口。

群推时，可以通过 RegId 或 Alias 进行群体区分，RegId 的优先级高。调用群推接口时，单次目标数据量 [2,1000]


#### 1.5.3.4. 全推（推送给全部的用户）

向所有设备推送某条消息。

使用场景：活动、系统升级提醒等。

限制：默认是每个 app 每日可发送一条。

注意：全推消息只有已经上线的 App 才可以使用。未上线的 App 请求全推接口时，会报错： {"result":10045,"desc":"应用审核中不可发送正式消息"}


### 1.5.4. 移动端的工作


注意：vivo 推送服务 SDK 支持的最低 android 版本为 Android 6.0。

[Android PUSH-SDK API接口文档](https://dev.vivo.com.cn/documentCenter/doc/364)

Android 端集成时，直接按照 [《Android PUSH-SDK集成指南》](https://dev.vivo.com.cn/documentCenter/doc/365) 中的说明操作即可。

可通过调用客户端 `isSupport()` 返回的公共状态码判断，状态码为101则系统不支持。     


### 1.5.5. F&Q

[vivo推送常见问题汇总](https://dev.vivo.com.cn/documentCenter/doc/156)

[Vivo推送相关的名词解释](https://dev.vivo.com.cn/documentCenter/doc/174)

摘录部分内容:

#### 1.5.5.1. vivo推送支持哪些消息类型？

仅支持通知，透传消息暂不支持。


#### 1.5.5.2. vivo推送支持哪些消息发送方式？

支持3种消息发送方式：RegID、Alias、标签;

支持消息推送类型：全推、群推、标签推、单推

以上能力均支持平台和api，单推目前只支持api推送


#### 1.5.5.3. 推送是否支持自定义icon图标？是否支持带图消息？ 

暂不支持。


#### 1.5.5.4. 手机晚上无法收到推送？在限制时间之外发送推送，是会延迟推送还是被直接抛弃？

1）为避免造成用户打扰，目前vivo手机接收的消息为7:00-23:00，服务器允许推送时间为7:00-23:00，系统消息不受此时间限制。

2）在允许时间之外发送的运营消息，将在有效量到发送量之间被管控，计入管控量。


#### 1.5.5.5. 如何判断系统是否支持vpush？    

可通过调用客户端 `isSupport()` 返回的公共状态码判断，状态码为101则系统不支持。     
> 该方法对移动端有用。


#### 1.5.5.6. vpush目前支持deeplink吗？    

支持


#### 1.5.5.7. 一个alias允许绑定多少设备,alias长度限制是多少？一个regId只能绑定一个alias吗？    

一个alias允许绑定一个设备，长度限制40字符。    

一个regId只能绑定一个alias，如果绑定了别的alias，原来的绑定关系会解除。


#### 1.5.5.8. API接口返回成功但是消息没有收到可能是什么原因？

1）确认客户端是否已经成功集成并 turnOnPush 获取到 regId，推送指定的 regId 是否是最新的 regId;

2） 确认手机是否已经打开通知栏开关权限；

3）检查消息是否已经送达但是折叠到了消息盒子里面？目前的策略是应用存活时展示，不存活时折叠；

4）检查单个客户端每日可接收的消息数量是否超出了限制？目前正式应用是每天最多可接收5条运营消息；

5）检查设备联网是否有问题，如果连接的wifi设置了代理，一般需要重启手机，可以切换手机网络重试；

6）如果上诉条件检查了但还是没有接收到消息，可以提供消息Id（taskId）和客户端订阅推送时返回的regId给小V客服。


#### 1.5.5.9. taskid与requestid的区别？    

taskid是消息ID，用于定位可能存在问题的消息。requestid是作为请求去重使用。



### 1.5.6. 错误码说明

[错误码参考](https://dev.vivo.com.cn/documentCenter/doc/368)


## 1.6. oppo 推送

开发者技术支持：QQ群1(1125363958)、QQ群2(1125372593)

* [Oppo推送App管理入口](https://open.oppomobile.com/newservice/capability?pagename=push)
* [OPPO PUSH产品说明](https://open.oppomobile.com/wiki/doc#id=10742)
* [OPPO PUSH受限说明](https://open.oppomobile.com/wiki/doc#id=10743)
* [OPPO PUSH服务开启指南](https://open.oppomobile.com/wiki/doc#id=10195)
* [OPPO PUSH 客户端SDK接口文档（2.1.0版本）](https://open.oppomobile.com/wiki/doc#id=10704)
* [OPPO PUSH运营平台使用指南](https://open.oppomobile.com/wiki/doc#id=10198)
* [OPPO  PUSH运营规范](https://open.oppomobile.com/wiki/doc#id=10611)
* [OPPO PUSH 服务端API](https://open.oppomobile.com/wiki/doc#id=10688)
* [OPPO PUSH FAQ](https://open.oppomobile.com/wiki/doc#id=10200)

OPPO 推送在 Android 端的适配情况如下：

* 只支持Android 4.4或以上版本的手机系统。
* 适配了 Android Q。
* 支持 ColorOS3.1及以上的系统的oppo的机型，一加5/5t及以上机型，realme所有机型。
* 仅支持通知栏消息，目前不支持透传


### 1.6.1. 名词解释

#### 1.6.1.1. 推送方式说明

 OPPO PUSH 支持标签、RegID、Alias等推送方式。 

* **标签（Topic）**：是具有某些相同属性的用户集合, 在应用初始化时或运行过程中，开发者可结合自己的业务特征，给用户打上不同的标签(Topic)。

* **RegID** ：是应用在对应设备的上唯一标识，当开发者需要给一个或多个设备推送消息时，可以使用基于 RegID的推送，将个性化的信息推送给指定设备。这种方式适用于需要为           每个用户订制个性化推送的场景。 注意：RegID在用户设备刷机、卸载应用、或设备长时间无连接时会失效

*  **alias**：是 OPPO 推送提供的一种个性化设定, 开发者可以将用户在应用内的账号或其它用户唯一标识设定为用户设备 RegID 的别名，在推送中可以直接基于别名进行推送。

别名不仅方便开发者将推送与自有的账号系统进行关联，同时也避免了因需要保存设备 RegID 与自有帐号的对应关系而额外带来的开发和存储成本。 


**注意:** alias与RegID(设备)一一对应，同一个alias不能对应多个RegID，alias对应的RegID以最后一次setalias对应的RegID为准；

alias失效的可能原因如下：

*  ①  主动调用unsetalias；
* ② alias对应的RegID已经失效；
* ③  设置alias不成功（调用setalias失败）；

         • alias只跟设备相关，所以请使用用户账号作为alias时，合理判断alias可能失效的情况。 

#### 1.6.1.2. 推送平台中 key 和 Secret 的说明 

* AppId、AppKey：客户端的身份标识，客户端SDK初始化时使用；
* AppSecret：开发者服务器端的身份标识，在使用Server SDK向客户端发送消息时用于校验身份；
* MasterSecret：开发者在使用服务端API接口时，用于校验身份的标识。

### 1.6.2. 后台需要的工作

* [OPPO PUSH 服务端API](https://open.oppomobile.com/wiki/doc#id=10688)

#### 1.6.2.1. 鉴权

响应回来的 auth_token，有效期默认为24小时，过期后无法使用，需要自行维护。后续推送操作都需要在请求头中添加该字段，在请求头中对应的关键字也是 `auth_token`


#### 1.6.2.2. 单推

OPPO 的单推分为两种，一次给指定用户推一个消息，一次给指定用户推送多个消息（即批量单推）。我们实际应该只用到前一种。

[单推接口 API ](https://open.oppomobile.com/wiki/doc#id=10692)  中的 `notification` 字段的内部结构在  [通知栏消息字段API](https://open.oppomobile.com/wiki/doc#id=10688) 中


#### 1.6.2.3. 广播推送（多推）

广播推送主要用于向大批量用户推送同一条消息的场景中，例如全量用户推送、标签推送、大量 registration_id 推送等推送场景。

多推时需要先执行保存通知栏消息内容体的操作，对应接口 `/server/v1/message/notification/save_message_content`, 然后将携带接口中返回的 `message_id` 去触发推送接口 `/server/v1/message/notification/broadcast`.

多推时支持一次推给全部用户，也支持推送给指定的部分用户。

##### 1.6.2.3.1. 字段说明

 `/server/v1/message/notification/broadcast` 接口的字段信息如下：

名称 | 类型 | 必须 | 默认 | 描述
---|---|---|---|---
`message_id` | String |是 |无 | 消息Id, 即 `/server/v1/message/notification/save_message_content` 返回的消息 id
`target_type` | Short | 是 | 无 | 目标类型，1:ALL; 2:`registration_id`; 5:别名 6:标签
`target_value` | String | 否 | 无 | 推送目标用户：ALL：不需要填写此字段 。非 ALL 时参考如下说明。

`target_type` 取值不为 ALL 时，`target_value` 取值说明如下：

* `registration_id` 与别名方式：【多个以英文分号(;)分隔，最大1000个】
* 标签：标签表达式：`{ "or" : [], "and" : [], "not" : [] }`。这几种类型可以并存，多项的隐含关系是 AND，即取几种类型结果的交集。
    * 例如：{ "or" : [ "tagID1", " tagID2" ], "and" : [ "tagID3", "tagID4"], "not" : [ "tagID5", "tagID6"] }
    * 先计算 "or" 字段的结果  tagID1或tagID2=A;
    * 再计算 "and" 字段的结果 tagID3且tagID4=B;
    * 再计算 "not" 字段的结果 非(tagID5或tagID6)=C
    * `target_value` 的最终结果为 A且B且C 。

### 1.6.3. 移动端的工作

* [OPPO PUSH 客户端SDK接口文档（2.1.0版本）](https://open.oppomobile.com/wiki/doc#id=10704)
* [OPPO推送官方DEMO](https://openfs.oppomobile.com/open/res/202009/21/cc0458dc32bac0e6b3c68c616a02824a.rar)


#### 1.6.3.1. 移动端集成步骤

参考  [OPPO PUSH 客户端SDK接口文档（2.1.0版本）](https://open.oppomobile.com/wiki/doc#id=10704) 中的 《SDK集成步骤》。核心内容如下：

* 下载 arr 包文件，并导入到 libs 目录下
* 在 modle 的 gradle 文件的 dependencies 中启用 arr 支持

```xml
dependencies {
    implementation fileTree(dir: "libs", include: ["*.jar","*.aar"])
}
```

* 执行初始化 `HeytapPushManager.init(pCtx, true);` , 第二个参数表示是否启用 log
* 代码判断是否支持 OPPO 推送：`HeytapPushManager.isSupportPush();` 不支持的话，不执行后续操作。
* 注册推送服务，` HeytapPushManager.register(pCtx, oppoAppKey, oppoAppSecret, new ICallBackResultService() {...}` 
* 在上一步的 `ICallBackResultService` 的 `onRegister(int reCode, String regId) ` 中判断是否注册成功，reCode 为 0 表示成功。
    * 注册成功，将获取到的 regId 传递给服务端
    * 注册失败，继续调用 `HeytapPushManager.getRegister();` 重新注册。

#### 1.6.3.2. 引导用户开启通知栏权限


OPPO 应用通知栏权限默认是关闭的，建议在 APP 内做弹框提示，调用 `HeytapPushManager.requestNotificationPermission` 显示通知权限弹窗，引导用户一键开启通知栏权限。

### 1.6.4. F&Q

[OPPO PUSH FAQ](https://open.oppomobile.com/wiki/doc#id=10200)

#### 1.6.4.1. OPPO 推送支持的设备

支持 ColorOS3.1及以上的系统的 oppo 的机型，一加5/5t及以上机型，realme 所有机型。（来源于：[支持的设备终端](https://open.oppomobile.com/wiki/doc#id=10743)）

#### 1.6.4.2. 推送限制和私信通道申请


累计用户数 | 可推送总数量
---|---
公信可推送数 <50000 | 100000
公信可推送数 ≥50000 | 累计用户数*2
私信可推送数 | 不限量

* 目前私信处于公测阶段，需要申请才能开启私信通道，[私信通道申请](https://open.oppomobile.com/wiki/doc#id=10614)的前提是：OPPO PUSH的累计用户数≥300,000
* 累计用户数是指从接入 OPPO PUSH 的 app 客户端，用户安装并激活的累计用户量，去除已卸载用户数。
* 累计用户数在 [OPPO推送运营平台](https://push.oppo.com/top/application-list) 可查询，每天会刷新。

#### 1.6.4.3. 28、如何创建通道？

请查看[《通知通道（Channel）适配》](https://open.oppomobile.com/wiki/doc#id=10289) 文档。

#### 1.6.4.4. 推送消息是否可以提供声音、震动等提醒选项设置？

消息推送时目前没有提供提醒方式的选择，所以通知都是使用系统默认的提醒方式。

#### 1.6.4.5. 打开范围中，启动应用内页的地址如何填写？怎么传递参数呢？

① click_action_type为1时填写activity对应的intent action；click_action_type为4时填写activity的完整类名

② 打开应用内页的action需要加标签<category android:name="android.intent.category.DEFAULT" />

③ 传递参数放在键值对里，客户端通过intent.getExtras()获取。

④ push的intent-filter应与其他功能的intent-filter区分开，勿添加其他action与data标签。

⑤ click_action_type为5时scheme路径需要写在click_action_url参数上 。

示例：


客户端AndroidManifest声明打开的activity如下：

```xml
<activity android:name="com.coloros.push.demo.component.InternalActivity">
<intent-filter>
<action android:name="com.coloros.push.demo.internal" /> 
<category android:name="android.intent.category.DEFAULT" /> 
</intent-filter>
<intent-filter>
<action android:name="android.intent.action.VIEW" />

<data android:scheme="https" />
<data android:scheme="http" />
<data android:scheme="command" />
<category android:name="android.intent.category.BROWSABLE" />
<category android:name="android.intent.category.DEFAULT" />
</intent-filter>
</activity>
```

API推送：


click_action_type为1时填写：com.coloros.push.demo.internal

click_action_type为4时填写：com.coloros.push.demo.component.InternalActivity

click_action_type为5时填写：command://test?key1=val1&key2=val2


#### 1.6.4.6. 如何获取oppo的rom值，判断OPPO机型或是否支持OPPO PUSH？

获取 `Build` 中的字段，可以根据里面的字段判断是否是 oppo 手机，`Build.BRAND`。 

调用 `PushManager.isSupportPush` 来判断是否支持OPPO push。


### 1.6.5. 错误码说明

[请求地址及公共参数（含错误码） ](https://open.oppomobile.com/wiki/doc#id=10689)


------

## 1.7. 极光推送


[服务端 REST API 概述](https://docs.jiguang.cn//jpush/server/push/server_overview/)




### 1.7.1. 后台工作

项目目前使用的就是极光推送。项目地址：[泉通推送服务](https://gogs.xiaoyuanjijiehao.com/jingutong/jgt-push)——项目中同时集成了 极光推送和小米推送，但是小米推送没有使用。

其他实现参考：

* [ylywyn/jpush-api-go-client](https://github.com/ylywyn/jpush-api-go-client)
* [xialeistudio/go-jpush](https://github.com/xialeistudio/go-jpush)


### 1.7.2. 移动端工作

#### 1.7.2.1. 集成 SDK 并初始化

使用 [《Android SDK 集成指南》](https://docs.jiguang.cn/jpush/client/Android/android_guide/)  中的自动集成功能即可。然后在 Application 中执行初始化操作，如下：

```java
 public void init(PushTestApplication appCtx) {
        JPushInterface.setDebugMode(true);
        JPushInterface.init(appCtx);
    }
```

> 假设集成过程中在清单文件设置的 Service 和 Reciver 分别是：JgtJPushReceiver，JgtJPushService

#### 1.7.2.2. 设置别名

[新别名 alias 与标签 tag 接口](https://docs.jiguang.cn/jpush/client/Android/android_api/#alias-tag)

新别名 alias 与标签 tag 接口回调触发 `cn.jpush.android.service.JPushMessageReceiver`，详细的回调方法请参考 [新的消息回调方式说明](https://docs.jiguang.cn/jpush/client/Android/android_api/#new-callback)。

```java
public void setAlias(String alias) {
        JPushInterface.setAlias(mAppCtx, mSequence, alias);
        mSequence++;
    }
```
#### 1.7.2.3. 在 Receiver 中处理回调


详细的回调方法请参考 [新的消息回调方式说明](https://docs.jiguang.cn/jpush/client/Android/android_api/#new-callback)。



### 1.7.3. F&Q

[Android SDK FAQ](https://docs.jiguang.cn/jpush/client/Android/android_faq/)

#### 1.7.3.1. 什么是通道共享功能？需要怎么使用？

极光的通道共享功能是利用极光 SDK 的高覆盖率，将各个应用中的长连接通道共享化，可以解决以下问题：

* APP（均需要集成极光 SDK ）进程间交互有效的情况下，针对某应用被杀死之后，极光也能通过共享通道将消息有效的下发到设备端并将该应用拉起。
* 某应用处于后台时，如果被系统关闭网络后，无法及时收到消息；此时可以从共享通道正常将下发下发到SDK，保证消息及时性。

使用方式：

* 集成好极光的 SDK （SDK 版本 3.1.6 及以后版本）以后，即可使用通道共享功能。
共享通道在启动时会扫描一遍手机上的其他应用，并拉起相关应用，即被杀死的 App 会被其他启动应用拉起，你的 App 启动也将拉起其他被杀死的应用。如果需要关闭该功能，将 Androidmanifest 中 downloadprovider 的 android:exported 设为 false 即可


#### 1.7.3.2. 如果APP不想被其它应用拉起也不拉起其它应用怎么办？

由于 Android 应用进程常被杀死导致长连接断开收不到消息，极光提供了拉起杀死应用的功能，从而保证 Android 消息的到达率。

* 集成 JPush SDK 1.8.0 及以上 版本，默认有配置不同应用间的 JPush 服务相互拉起的功能。
* 集成 JPush SDK 3.1.6 及以上版本，默认有配置通道共享功能，该功能也会相互拉起应用。
* 如需关闭拉起服务，可在 Androidmanifest 中做如下操作：
    * 将 DaemonService 组件的 enabled 设置成 false：此时您的 App 不会被其他 App 拉起，但会拉起其他的 App； 如果您希望达到您的 App 不会被其他 App 拉起，同时也不*           拉起其它 App 的效果，请请 [联系商务开通 VIP 服务](https://www.jiguang.cn/accounts/business_contact?fromPage=push_doc)，极光 VIP 用户可以提供此功能支持。
    * 将 DownloadProvider 的 android:exported 设为 false ：即可关闭通道共享功能

#### 1.7.3.3. 第三方系统收不到推送的消息

由于第三方 ROM 的管理软件需要用户手动操作

小米【 MIUI 】

   * 自启动管理：需要把应用加到【自启动管理】列表，否则杀进程或重新开机后进程无法开启
   * 通知栏设置：应用默认都是显示通知栏通知，如果关闭，则收到通知也不会提示
   * 网络助手：可以手动禁止已安装的第三方程序访问 2G/3G 和 WIFI 的网络和设置以后新安装程序是否允许访问 2G/3G 和 WIFI 的网络
   * MIUI 7 神隐模式： 允许应用进行自定义配置模式，应用在后台保持联网可用，否则应用进入后台时，应用无法正常接收消息。【设置】下电量和性能中【神隐模式】

华为【 Emotion 】

   * 自启动管理：需要把应用加到【自启动管理】列表，否则杀进程或重新开机后进程不会开启，只能手动开启应用
   * 后台应用保护：需要手动把应用加到此列表，否则设备进入睡眠后会自动杀掉应用进程，只有手动开启应用才能恢复运行
   * 通知管理：应用状态有三种：提示、允许、禁止。禁止应用则通知栏不会有任何提醒

魅族【 Flyme 】

  * 自启动管理：需要把应用加到【自启动管理】列表，否则杀进程或重新开机后进程无法开启
  * 通知栏推送：关闭应用通知则收到消息不会有任何展示
  * 省电管理： 安全中心里设置省电模式，在【待机耗电管理】中允许应用待机时，保持允许，否则手机休眠或者应用闲置一段时间，无法正常接收消息。

VIVO【 Funtouch OS 】

  * 内存一键清理：需要将应用加入【白名单】列表，否则系统自带的“一键加速”，会杀掉进程
  * 自启动管理：需要将应用加入 “i 管家”中的【自启动管理】列表，否则重启手机后进程不会自启。但强制手动杀进程，即使加了这个列表中，后续进程也无法自启动。

OPPO【 ColorOS 】

   * 冻结应用管理：需要将应用加入纯净后台，否则锁屏状态下无法及时收到消息
   * 自启动管理：将应用加入【自启动管理】列表的同时，还需要到设置-应用程序-正在运行里锁定应用进程，否则杀进程或者开机后进程不会开启，只能手动开启应用

三星

* 内存一键优化：需要将应用加入【白名单】列表，否则系统内存优化后，会杀掉应用进程

#### 1.7.3.4. Tag、Alias、Registration ID 需要每次初始化时都重新设置吗，会变化吗？

tag、alias 可以参考别名与标签 API 进行设置，3.0.7 版本以前的接口每次设置是覆盖设置，而不是增量设置；从 3.0.7 版本开始，对别名标签提供增删改查接口。Tag 和 alias 一经设置成功，除非取消或覆盖，是不会变化的。**设置好的 tag、alias 与客户端的对应关系保存在 JPush 服务器，目前没有从 JPush 服务器查询这个对应关系的接口，所以需要客户将对应关系保存在 APP 应用服务器。**

Registration ID 是客户端 SDK 第一次成功连接到 JPush 服务器时，JPush 服务器给分配的。可以通过获取 RegistrationID API 来获取 Registration ID 进行推送。Registration ID 对应一个应用的一个客户端，Android 的 registration ID 一般不会改变。

### 1.7.4. 错误码说明



--------

## 1.8. 统一推送联盟 UPA

[统一推送联盟](http://www.chinaupa.com/) ，英文全称为 Unified Push Alliance, 简称 UPA

统一推送联盟在各品牌手机上的支持情况（基于 UPA 网站中资讯内容整理）：



厂商|支持情况|备注信息
---|---|---
小米 | MIUI10.0 及以上版本 | 含红米系列手机，新上市手机都会支持，历史机型会以系统升级的方式兼容。
vivo | 支持 2017 年以后发布的所有手机 | 包括 iQOO 品牌
华为 | EMUI 10.0 及以上版本 | 含荣耀，历史机型会以系统升级的方式兼容。
OPPO | ColorOS 3.0及以上版本 | 预计在2019年12月31日可覆盖全机型的93%。（资讯原文是 2019年9月发布的）
一加 | 一加5及以上机型中支持统一推送服务 | 一加5 是 2017年6月发布的
Realme | 覆盖全部机型 | 
中兴 | Mifavor 9.1及以上版本 | 
三星 | Galaxy A50s机型（SM-A5070）及以后的新机型 | Galaxy A50s 是 2019年11月上市



目前，UPA 没有直接暴露给开发者相关的 SDK，但从 华为 和 Vivo 的推送 SDK 中可以间接的查看到相应的类。

Vivo 的文档中有对统一推送联盟接口的调用方式进行说明。[具体可以查看 Vivo API 文档中的 11-12 节](https://dev.vivo.com.cn/documentCenter/doc/362#w2-98682354),但是，：

* 对于推送限制没有一个详细的说明，仅描述由 Vivo 运营人员配置。
* 而且开发环境下无法实现测试，只有已经上线的 App 才可以发送测试消息——未上线的 App 推送时会提示：` {"result":10045,"desc":"应用审核中不可发送正式消息"}`。
* 另外，Vivo 文档中描述的 `click_action` 应该是一个必填字段，该字段指定了点击 UPA 消息之后的跳转目标。该字段的子字段 `intent` 需要以 schema 的方式指定跳转目标，如：` "intent": "Test#TestIntent"`
* Vivo 文档中的 `registration_tokens` 应该仅是只 vivo 的注册设备

> 根据 Vivo 提供的 UPA 接口协议文档大致推测，Vivo 做了二次封装, 其暴露的接口仅适用于 Vivo 设备。



------



## 1.9. 其他


### 1.9.1. 移动端判断厂商的代码

OPPO 文档中推荐使用 `Build.BRAND` 字段判断是否是 oppo 手机。 

[Android 判断手机品牌](https://www.cnblogs.com/yongdaimi/p/11913081.html)

[Android判断手机ROM](https://www.jianshu.com/p/ba9347a5a05a) 

[Android 获取手机的厂商、型号、Android系统版本号、IMEI、当前系统语言等工具类](https://blog.csdn.net/zhuwentao2150/article/details/51946387?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~all~first_rank_v2~rank_v25-1-51946387.nonecase&utm_term=android%20%E5%88%A4%E6%96%AD%E6%89%8B%E6%9C%BA%E5%8E%82%E5%95%86&spm=1000.2123.3001.4430)

### 1.9.2. Android 端注意事项

Android 端在集成厂商推送时，需要特别注意两个事情：

* 个别品牌手机会收到多个厂商通道推送消息的情况
* Android 9.0 以后新增的通知通道

## 1.10. 附录

测试 apk 的证书指纹信息：

```
MD5:  80:C6:62:E9:8C:63:C1:A8:13:D9:AC:6C:C6:89:80:3B
SHA1: 41:13:61:90:38:E3:3A:D3:09:0A:8F:3E:3E:D3:FE:32:7D:03:C8:DD
SHA256: C9:14:0B:6E:4B:70:C8:ED:0D:59:FF:C8:18:AA:8F:B9:6A:27:93:1D:98:69:A6:10:5F:25:28:FF:9D:63:FD:D7
```