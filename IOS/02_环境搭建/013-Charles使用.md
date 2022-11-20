## 1.1《Mac安装和破解激活Charles》

### 1.1.1 charles的下载安装
官网下载地址：[https://www.charlesproxy.com/latest-release/download.do](https://www.charlesproxy.com/latest-release/download.do)
安装很简单，一直下一步即可

### 1.1.2 破解

进入网站 [http://www.charles.ren](http://www.charles.ren)，随意输入一个 `Registered Name`，然后点击 **生成**，复制生成的 key， 然后打开 Charles, 并在菜单栏中选择 `help-register`，输入刚刚生成的 key 以及生成 key 时的 `Registered Name` ，确定之后会提示重启，重启后激活成功。

原文链接：[Mac安装和破解激活Charles](https://www.cnblogs.com/x1you/p/12033839.html)

## 1.2 《iOS Charles抓包》

抓包参考文章 ：[轻松搞定Charles的HTTPS抓包（iOS13可用）](https://blog.csdn.net/y277an/article/details/103573163)

注意：按照上面文章配置完成之后，如果还不显示抓包数据，就需要检查 `Proxy ` 中的 `Recording Settings` - `Include` 中的配置。如果需要拦截全部 https 则清空 `Include` 中的内容，否则需要指定 host 和 端口。

其他参考文章：[iOS Charles抓包](https://www.jianshu.com/p/724ef9d3efb6)