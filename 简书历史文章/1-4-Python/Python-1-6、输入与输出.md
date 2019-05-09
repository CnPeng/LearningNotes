##一、输出

python中变量的输出         
```
 # 打印提示
    print('hello world')

    print('给我的卡---印度语，你好的意思')

    # 输出变量
    url = 'itcast.cn'
    print('我们的网址是:',url)
    print('我们的网址是%s'%url)
```

##二、输入

在Python中，获取键盘输入的数据的方法是采用 **` raw_input`** 函数（至于什么是函数，以后的章节中介绍），那么这个 raw_input 怎么用呢?
看如下示例:
```
  password = raw_input("请输入密码:")
  print （'您刚刚输入的密码是:', password）
```
运行结果:
![运行结果](http://upload-images.jianshu.io/upload_images/2551993-c38941dde1e17286.gif?imageMogr2/auto-orient/strip)

>**注意:**
>* `raw_input(" ")`的小括号中放入的是**提示信息**，用来在获取数据之前给用户的一个简单提示
>* `raw_input("")` 在从键盘获取了数据以后，会**存放到等号右边的变量中**
>* 在python2中print后面可以不加小括号，但是python3后必须加括号
