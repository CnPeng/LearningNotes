![2018-01-17 20-13-03屏幕截图.png](http://upload-images.jianshu.io/upload_images/2551993-c2294cc387ff96de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


>Error:Execution failed for task ':app:transformClassesWithDexBuilderForDebug'.
 com.android.build.api.transform.TransformException: java.io.UncheckedIOException: java.nio.file.AccessDeniedException: /home/cnpeng/.android/build-cache.lock


最终解决方案：删除 .android 目录。
命令如下：
>cnpeng@cnpeng:~$ sudo rm -r .android
[sudo] cnpeng 的密码： 
cnpeng@cnpeng:~$ 




参考内容：
https://stackoverflow.com/questions/47164357/android-update-causing-error-errorjava-nio-file-accessdeniedexception-home-p

https://stackoverflow.com/questions/30749042/error-in-building-project-in-android-studio/37943687
