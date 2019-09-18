MAC 下利用终端快速查看文件的 MD5、SHA 

## 一、查看MD5

在终端中输入 `md5` ,然后按 TAB 键，然后将文件拖入到终端，然后回车即可。

* 示例如下：

```
// 查看 antLinkerCampus-release_380.apk 的 md5 信息
Last login: Mon Jul  1 20:08:08 on ttys000
CnPeng:3.8.0 cnpeng$ md5 /Users/cnpeng/CnPeng/01_Work/04_OA/AntOA/03_发版文档/3.8.0/antLinkerCampus-release_380.apk 
MD5 (/Users/cnpeng/CnPeng/01_Work/04_OA/AntOA/03_发版文档/3.8.0/antLinkerCampus-release_380.apk) = f237152cf4838d687b8d48cef7677f05
CnPeng:3.8.0 cnpeng$ 
```

## 二、查看SHA

## 1、SHA1

```
shasum -a 1 文件全路径名
```

## 2、SHA256

```
shasum -a 1 文件全路径名
```