[原文:[IOS] Showing Recent Messages :-1: Unable to load contents of file list](https://www.jianshu.com/p/6461abf21fd9)

## 1.错误详情

```swift

Showing Recent Messages
:-1: Unable to load contents of file list: '/Users/kerwin/Program/ios/lover/Pods/Target Support Files/Pods-Lover/Pods-Lover-frameworks-Debug-input-files.xcfilelist' (in target 'Lover')


Showing Recent Messages
:-1: Unable to load contents of file list: '/Users/kerwin/Program/ios/lover/Pods/Target Support Files/Pods-Lover/Pods-Lover-frameworks-Debug-output-files.xcfilelist' (in target 'Lover')


Showing Recent Messages
:-1: Unable to load contents of file list: '/Users/kerwin/Program/ios/lover/Pods/Target Support Files/Pods-Lover/Pods-Lover-resources-Debug-input-files.xcfilelist' (in target 'Lover')


Showing Recent Messages
:-1: Unable to load contents of file list: '/Users/kerwin/Program/ios/lover/Pods/Target Support Files/Pods-Lover/Pods-Lover-resources-Debug-output-files.xcfilelist' (in target 'Lover')
```

## 2. 解决方案

在 `xx.xcodeproj/project.pbxproj` （ xx 为项目名）中找到对应的文件名移除即可编译成功。

右击 `project.pbxproj` ，然后选择打开方式，使用 VsCode 或其他工具打开，然后检索 `-input-files.xcfilelist`，`-output-files.xcfilelist` 找到它们并删除，共有四个代码片段。

 
