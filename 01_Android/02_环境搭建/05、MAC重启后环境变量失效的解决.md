在 MAC 终端中配置完环境变量后，重启系统或者重启终端之后，刚才配置的环境变量失效，又出现 `command not found `的错误提示。解决方案如下：

* 打开 `.zshrc` 文件

```
# 切换到根目录
cd ~/

# 打开文件
open -e .zshrc

# 如果上述 .zshrc 文件不存在，则通过 touch 创建
touch .zshrc
```

* 添加内容

```
source ~/.bash_profile
```

* 让修改立即生效

```
source .zshrc
```

**环境变量的配置步骤参考 [03、MAC环境变量的配置-adb为例.md](03、MAC环境变量的配置-adb为例.md)**

参考链接：

* [Mac export 添加环境变量或改动 PATH ,重启失效](https://www.jianshu.com/p/2f0c19daec0e)