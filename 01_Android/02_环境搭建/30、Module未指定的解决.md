打开从网络下载的 demo 时，可能会出现 `module not specified` 的错误提示，然后导致 APP 无法运行，解决步骤如下:


* in the root folder of your project, open the `settings.gradle` file for editing.

* Remove `include ':app'` from the file.

* On Android Studio, click on the File Menu, and select `Sync Project 
with Gradle files`.

* After synchronisation, add `include ':app'` back to the settings.gradle file.

* Re-run `Sync Project with Gradle files` again.