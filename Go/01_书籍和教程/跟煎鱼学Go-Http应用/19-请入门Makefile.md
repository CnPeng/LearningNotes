# 1. 19-请入门Makefile

> 2022-01-27 10:21

[点击查看原文](https://eddycjy.com/posts/go/gin/2018-08-26-makefile/)

## 1.1. 知识点

写一个 Makefile

## 1.2. 本文目标

含一定复杂度的软件工程，基本上都是先编译 A，再依赖 B，再编译 C…，最后才执行构建。如果每次都人为编排，又或是每新来一个同事就问你项目 D 怎么构建、重新构建需要注意什么…等等情况，岂不是要崩溃？

我们常常会在开源项目中发现 Makefile，你是否有过疑问？

本章节会简单介绍 Makefile 的使用方式，最后建议深入学习。

## 1.3. 怎么解决

对于构建编排，Docker 有 `Dockerfile` ，在 Unix 中有神器 [Make](https://en.wikipedia.org/wiki/Make_%28software%29) ….

## 1.4. Make

### 1.4.1. 是什么

Make 是一个自动化构建工具，会在当前目录下寻找 `Makefile` 或 `makefile` 文件。如果存在，会依据 `Makefile` 的构建规则去完成构建

当然了，实际上 `Makefile` 内都是你根据 `make` 语法规则，自己编写的特定 `Shell` 命令等

它是一个工具，规则也很简单。在支持的范围内，编译 A， 依赖 B，再编译 C，完全没问题

### 1.4.2. 规则

`Makefile` 由多条规则组成，每条规则都以一个 target（目标）开头，后跟一个 `:` 冒号，冒号后是这一个目标的 prerequisites（前置条件）

紧接着新的一行，必须以一个 tab 作为开头（即点击一次键盘上的 TAB 键），后面跟随 command（命令），也就是你希望这一个 target 所执行的构建命令

```/
[target] ... : [prerequisites] ...
<tab>[command]
    ...
    ...
```

* `target` ：一个目标代表一条规则，可以是一个或多个文件名。也可以是某个操作的名字（标签），称为伪目标（phony）.
* `prerequisites` ：前置条件，这一项是**可选参数**。通常是多个文件名、伪目标。它的作用是**判断 target 是否需要重新构建**，如果前置条件不存在或有过更新（文件的最后一次修改时间）则认为 target 需要重新构建
* `command`：构建这一个 `target` 的具体命令集

### 1.4.3. 简单的例子

本文将以 [go-gin-example](https://github.com/EDDYCJY/go-gin-example) 去编写 Makefile 文件，请跨入 make 的大门

#### 1.4.3.1. 分析

在编写 Makefile 前，需要先分析构建先后顺序、依赖项，需要解决的问题等

#### 1.4.3.2. 编写

```makefile
.PHONY: build clean tool lint help

all: build

build:
	go build -v .

tool:
	go tool vet . |& grep -v vendor; true
	gofmt -w .

lint:
	golint ./...

clean:
	rm -rf go-gin-example
	go clean -i .

help:
	@echo "make: compile packages and dependencies"
	@echo "make tool: run specified go tool"
	@echo "make lint: golint ./..."
	@echo "make clean: remove object files and cached files"
```

##### 1.4.3.2.1. `.PHONY`

在上述文件中，使用了 `.PHONY`，其作用是声明 build / clean / tool / lint / help 为伪目标，**声明为伪目标**会怎么样呢？

声明为伪目标后：在执行对应的命令时，make 就**不会去检查**是否存在 build / clean / tool / lint / help 其对应的文件，而是**每次都会运行标签对应的命令**

若不声明：恰好存在对应的文件，则 make 将会认为 xx 文件已存在，没有重新构建的必要了

##### 1.4.3.2.2. 命令

在命令行中执行如下命令即可看见效果，

命令 | 作用
---|---
make | 即 make all，执行上述 Makefile 文件中的 `all`
make build |  执行上述 Makefile 文件中的 `build`命令，即编译当前项目的包和依赖项
make tool | 执行上述 Makefile 文件中的 `tool`命令，即运行指定的 Go 工具集
make lint | 执行上述 Makefile 文件中的 `lint`命令，即 golint 一下
make clean | 执行上述 Makefile 文件中的 `clean`命令，删除对象文件和缓存文件
make help | 执行上述 Makefile 文件中的 `help`命令

#### 1.4.3.3. 为什么会打印执行的命令

如果你实际操作过，可能会有疑问。明明只是执行命令，为什么会打印到标准输出上了？

##### 1.4.3.3.1. 原因

make 默认会打印每条命令，再执行。这个行为被定义为**回声**

##### 1.4.3.3.2. 解决

在对应命令前加上 `@`，即可指定该命令不被打印到标准输出上

```makefile
build:
	@go build -v .
```

那么还有其他的特殊符号吗？有的，请课后去了解下 `+`、`-` 的用途 🤩

## 1.5. 小结

这是一篇比较简洁的文章，希望可以让您对 Makefile 有一个基本了解。


## 1.6. 参考

* [本系列示例代码：go-gin-example](https://github.com/EDDYCJY/go-gin-example)

## 1.7. 补充

* go >= 1.12 不在支持 `go tool vet` 以后都用 `go vet` 吧——摘自评论区
* MacOS Big Sur 11.6.1 系统中，如果在 GoLand 中执行 make 命令提示 `Command not found`，则可以尝试使用系统自带的 CMD 工具。如果系统自带的 CMD 工具可以执行，则可以在 GoLand 的 `Preferences`-`工具`-`终端`-`Shell 路径` 中将默认的 `/bin/zsh` 修改为 `/bin/bash`，该修改表示切换 cmd 工具为系统自带的 CMD 工具。——CnPeng