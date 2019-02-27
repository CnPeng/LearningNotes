[点击查看原文](
https://medium.com/rosberryapps/lets-optimize-the-work-with-terminal-for-xcode-developers-194623da55d4)

### 步骤1：编辑一个 `xcode-terminal.sh` 脚本文件

内容如下：

```
#!/bin/sh

if [ -n "$XcodeProjectPath" ]; then	
  open -a Terminal "$XcodeProjectPath"/..
else		
  open -a Terminal "$XcodeWorkspacePath"/..
fi
```

上述脚本在 `.xcodeproj` or `.xcworkspace` 目录下都可以打开终端。

另外，.sh 前面的名称可以自定义，但是下步骤二中修改权限时，名称必须一致。

### 步骤2：修改权限

切换到上述脚本文件目录，然后打开终端执行下列命令

```
chmod +x <xcode-terminal.sh>
```

### 步骤3：添加到 Xcode 中

依次打开 ： **Xcode menu > Behaviors > Edit Behaviors…**, 然后点击下图左下角的 + 

![](https://images.gitee.com/uploads/images/2019/0121/191044_f4161a99_930142.png "屏幕截图.png")

然后输入自定义的 Behavior 名称，并指定一个快捷键。

然后勾选上图右侧的 `Run`, 并双击 `Run` 右侧的下拉框，指定该 Behavior 对应的脚本文件——也就是刚才创建的 xcode-terminal.sh。

至此，配置完成。在 Xcode 编辑器中，按下自定义的快捷键就可以调出终端了。