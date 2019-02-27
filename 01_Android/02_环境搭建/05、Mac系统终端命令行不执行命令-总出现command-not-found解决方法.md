## 1、MAC配置完环境变量后，ls不好用的情况

https://blog.csdn.net/u012810020/article/details/51800823


配置过安卓开发环境，改过bash_profile这个文件，最后不知怎么的只有cd命令能执行，我猜测可能修改bash_profile文件后没有保存导致的，保存命令是: source .bash_profile

说下我的解决方法:

1，在命令行中输入：


    export PATH=/usr/bin:/usr/sbin:/bin:/sbin:/usr/X11R6/bin 

     这样可以保证命令行命令暂时可以使用。命令执行完之后先不要关闭终端。
2，输入


    cd ~/  
进入当前用户的home目录。


3，创建bash_profile 执行命令: 


    touch .bash_profile  


4，打开并编辑bash_profile  执行命令:

    open .bash_profile  


5，这样就打开了一个记事本，会显示你之前配置过的path，修改记事本，先全部删除你觉得有用可以在桌面备份下，命令行好用后再加里。

    export JAVA_HOME=`/usr/libexec/java_home`  
 
 
    export PATH=${PATH}:${JAVA_HOME}:${ANDROID_SDK_ROOT}:${ANDROID_SDK_ROOT}/platform-tools:${ANDROID_SDK_ROOT}/tools:${JAVA_HOME}:${JAVA_HOME}/bin  
    export PATH="/usr/local/bin:/usr/local/sbin:~/bin:$PATH"  

上面是系统的，如果想配置什么的话比如安卓开发环境在把sdk的目录加上，不配置的话就不用加了。
    export ANDROID_SDK_ROOT="/Users/wangkai/Documents/adt-bundle-mac-x86_64-20130219/sdk"  
 

  我的命令行里就保存了这4个引用

6，进行保存 全选: command+s 或者不放心的话 选中记事本  文件-保存   保存之后没提示 直接关掉就可以。

7，此时在命令行中输入更新命令(命令行一直不要关): 
    source .bash_profile  

OK，重启终端命令行就可以了，eg: 输入ls



## 2、[mac安装git后，在终端里运行git命令，系统提示需要安装Xcode问题解决](https://blog.csdn.net/u011968063/article/details/70149191)


### 问题：

最近在mac上安装里git，但是在终端里执行git命令的时候，系统就会提示如下信息：

```
xcode-select: note: no developer tools were found at '/Applications/Xcode.app', 
requesting install. Choose an option in the dialog to download the command line 
developer tools.

```
大体意思就是，需要安装Xcode后，才能安装上git来使用。这个问题很是折磨人，尤其是在初次安装完git或者是更新完系统后，会出现这个问题。废话不多说，接着来说解决方法。

### 解决方法：

不使用Xcode安装完git后，git的默认安装目录是：

/usr/local/git

但是使用 which git 命令后，发现git的目录是：

/usr/bin/git

好嘛，发生问题的原因就是这个了，系统默认git的目录和git默认安装目录不一致导致的。找到原因了，就好解决这个问题了，这要想办法让目录一致就好了。

我使用的方法是建立软连接的方法：

1、进入 /usr/bin 目录，使用 find git 命令，看看有没有，如果有，就使用 sudo rm -rf git 命令把它删除掉；

2、还是在 /usr/bin 目录下，使用 ln -s /usr/local/git/bin/git git 命令建立git的软连接；

3、使用 git --version 命令，锵锵，这个时候git命令就好使啦。

注意：/usr/local/git 是我的机器上，git的安装目录，如果你的不一样，需要把 ln -s /usr/local/git/bin/git git 中的红色部分换成你的目录。