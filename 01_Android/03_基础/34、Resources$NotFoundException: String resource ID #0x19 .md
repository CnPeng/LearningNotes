问题原因和解决方案：
为TextView控件setText时，如果传入的是int类型数据，setText 会将其作为 stringID，会去R文件中寻找对应的String，如果R中不包含对应的id，就会报上面的错误，所以，

如果我们需要让TextView展示数字时，需要先将该数字转换成字符串

https://blog.csdn.net/sinat_29962405/article/details/50951008