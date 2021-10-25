# 1. 向github推送代码报错Support for password authentication was removed

[原文：《github开发人员在七夕搞事情：remote: Support for password authentication was removed on August 13, 2021》](https://blog.csdn.net/weixin_41010198/article/details/119698015)

## 1.1. 问题描述

2021年8月14日起向 github 推送代码时，可能会碰到如下报错：

```
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information.
fatal: Authentication failed for 'https://github.com/CnPeng/LearningNotes.git/'
```

大概意思就是你原先的密码凭证从 2021 年 8 月 13 日开始就不能用了，必须使用个人访问令牌（personal access token），也就是说需要把你的密码替换成 token！

## 1.2. github 为什么要把密码换成 token

[github官方解释](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)

### 1.2.1. 修改为 token 的动机

我们描述了我们的动机，因为我们宣布了对 API 身份验证的类似更改，如下所示：

近年来，GitHub 客户受益于 GitHub.com 的许多安全增强功能，例如双因素身份验证、登录警报、经过验证的设备、防止使用泄露密码和 WebAuthn 支持。 这些功能使攻击者更难获取在多个网站上重复使用的密码并使用它来尝试访问您的 GitHub 帐户。

尽管有这些改进，但由于历史原因，未启用双因素身份验证的客户仍能够仅使用其 GitHub 用户名和密码继续对 Git 和 API 操作进行身份验证。

从 2021 年 8 月 13 日开始，我们将在对 Git 操作进行身份验证时不再接受帐户密码，并将要求使用基于令牌（token）的身份验证，例如个人访问令牌（针对开发人员）或 OAuth 或 GitHub 应用程序安装令牌（针对集成商） GitHub.com 上所有经过身份验证的 Git 操作。 您也可以继续在您喜欢的地方使用 SSH 密钥（[如果你要使用ssh密钥可以参考](https://cloud.tencent.com/developer/article/1861466)）。

### 1.2.2. 修改为 token 的好处

令牌（token）与基于密码的身份验证相比，令牌提供了许多安全优势：

* 唯一： 令牌特定于 GitHub，可以按使用或按设备生成
* 可撤销：可以随时单独撤销令牌，而无需更新未受影响的凭据
* 有限 ： 令牌可以缩小范围以仅允许用例所需的访问
* 随机 ：令牌不需要记住或定期输入的更简单密码可能会受到的字典类型或蛮力尝试的影响

### 1.2.3. 如何生成自己的 token

在个人设置页面，找到Setting（参考）

![](pics/20210825092838975_246362934.png)

选择开发者设置 Developer setting

![](pics/20210825092903327_273360322.png)

选择个人访问令牌 Personal access tokens，

![](pics/20210825092936231_899410921.png)

然后选中生成令牌 Generate new token

![](pics/20210825092947217_261958014.png)

为您的令牌指定一个描述性名称

![](pics/20210825093011106_386864948.png)

设置token的有效期

![](pics/20210825093043690_1606181567.png)

设置访问权限

![](pics/20210825093126868_1459225393.png)

单击生成令牌

![](pics/20210825093157081_937931411.png)

复制并保存令牌

![](pics/20210825093242091_1283255697.png)

注意：

记得把你的 token 保存下来，因为你再次刷新网页的时候，你已经没有办法看到它了！

## 1.3. 设置 token 登录

### 1.3.1. 方案1

之后用自己生成的 token 登录，把上面生成的 token 粘贴到输入密码的位置，然后成功 push 代码！

![](pics/20210825093550025_838156283.png)

### 1.3.2. 方案2 (推荐)

也可以把 token 直接添加远程仓库链接中，这样就可以避免同一个仓库每次提交代码都要输入 token 了，设置格式为：

```
git remote set-url 远程仓库别名 https://<your_token>@github.com/<USERNAME>/<REPO>.git
```
* 远程仓库别名：将仓库 clone 到本地时自己设置的名称
* `<your_token>`：换成你自己得到的 token
* `<USERNAME>`：是你自己 github 的用户名
* `<REPO>`：是你的仓库名称

例如：

```
git remote set-url origin https://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@github.com/shliang0603/Yolov4_DeepSocial.git/
```

## 1.4. 其他问题

### 1.4.1. push 操作未出现输入密码选项

如果 push 等操作没有出现输入密码选项，请先输入如下命令，之后就可以看到输入密码选项了

```
git config --system --unset credential.helper
```

## 1.5. 其他参考：

* [《Github文档-创建个人访问令牌》](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)
* [GitHub不再支持密码验证解决方案：SSH免密与Token登录配置](https://cloud.tencent.com/developer/article/1861466)