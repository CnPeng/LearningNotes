##一、Ubuntu中安装AndroidStudio
* 到 Android  官网下载安装包，
* 下载完成之后解压，将解压后的包改名为 AndroidStudio （`不改也行，改名主要是为了好区别。另外，必要的话，也可以将解压后的包挪到自己指定的一个目录`)
* 然后 通过 `cd` 命令进入到 AndroidStudio 包中的 `bin` 目录，bin 目录中会有一个 `studio.sh`  的文件。
* 在命令窗口中执行命令 `./studio.sh`
* 之后按照提示一步步的安装即可

##二、在Lanucher 中创建快捷方式
按照上面第一部分的描述安装完成之后，会有一个比较尴尬的问题，就是——找不到启动的快捷图标：**在 Lanucher 导航条中没有，在Dash 中也找不到**。这样导致的情况就是，安装成功之后我关闭了Studio，想再次启动的时候根本不知道该从哪里启动啊啊啊啊啊。。。。

 最终的解决方案是：通过命令行手动的添加AndroidStudio 的快捷方式。具体如下：
* 先执行如下命令
`sudo gedit /usr/share/applications/Studio.desktop` 
执行该命令之后会创建并打开一个，名为 `Studio.desktop` 的文件，文件内容为空。

* 然后复制如下内容到该文件中
```
[Desktop Entry]
Version=1.0
Type=Application
Name=Android Studio
Exec="/home/username/Programs/AndroidStudio/bin/studio.sh" %f
Icon=/home/username/Programs/AndroidStudio/bin/idea.png
Categories=Development;IDE;
Terminal=false
StartupNotify=true
StartupWMClass=jetbrains-android-studio
Name[en_GB]=android-studio.desktop
```
>**注意，以上内容必须顶格写，任意一行前面不要有空格**

然后保存 并退出即可，此时，再看 Lanucher 导航条 和 Dash 中就都有了 AndroidStudio 的快捷方式图标了。

在 Dash  中的快捷方式显示如下：

![](http://upload-images.jianshu.io/upload_images/2551993-4992330c13d9b660.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 Launcher 的快捷方式展示如下：

![](http://upload-images.jianshu.io/upload_images/2551993-3721f37896c8ce4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##三、补充：其他创建AndroidStudio 快捷方式的方法（不一定能生效）
>这两种方法可能会不生效，上面描述的 `手动创建快捷图标的方法才是最根本最有效的方法`

**方法A :** 
在安装时，如果安装成功了，会打开AndroidStudio ，此时，在 Launcher 导航条中是能看到AndroidStudio 的图标的，右击该图标，然后 选择 “锁定到启动器”即可

**方法B**
参照下图，在安装成功，进入该界面之后，点击 底部的 `Configuration ` ，在下拉菜单中选择 `Create DeskTop Entry` 即可
![](http://upload-images.jianshu.io/upload_images/2551993-e346d2ed008afd6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


