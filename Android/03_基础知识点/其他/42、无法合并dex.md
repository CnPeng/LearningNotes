* 问题现象：

Dex Archive Merger Exception: Unable to merge dex

* 问题原因：

libs 目录下有重复的 jar 包

在升级小米推送SDK是，3.2.2 版本的jar没有删除，然后直接引入了 3.6.9 ，然后编译/部署的时候就爆了上述错误。
删除其中一个之后就可以了

其他参考：
https://hk.saowen.com/a/09ca342cc1113b3e1137e8883e2ec1fcee179ff8a8dd41b5dfcf2082530aaba5