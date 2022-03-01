# 1. 18-Golang交叉编译

> 2022-01-27 09:49

[点击查看原文](https://eddycjy.com/posts/go/gin/2018-03-26-cgo/)

## 1.1. 知识点

跨平台编译

## 1.2. 本文目标

在本系列 [第 9 篇文章](./9-将Golang应用部署到Docker.md) 讲解构建 Scratch 镜像时，我们编译可执行文件用了另外一个形式的命令，如下：

```/
$ CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o go-gin-example .
```

我想你可能会有疑问，今天本文会针对上述命令进行讲解。

## 1.3. 说明

我们将讲解命令中各个参数的作用，希望你在阅读时，将每一项串联起来，你会发现这就是交叉编译相关的小知识

也就是 `Golang` 令人心动的特性之一——**跨平台编译**

### 1.3.1. CGO_ENABLED

#### 1.3.1.1. 作用：

用于标识（声明） cgo 工具是否可用

#### 1.3.1.2. 意义：

存在交叉编译的情况时， `cgo` 工具是不可用的。

在标准 go 命令的上下文环境中，交叉编译意味着程序**构建环境**的目标计算架构的标识与程序**运行环境**的目标**计算架构的标识不同**，或者程序构建环境的目标操作系统的标识与程序运行环境的目标**操作系统的标识不同**。

#### 1.3.1.3. 小结：

结合案例来说，我们是在宿主机编译的可执行文件，而在 Scratch 镜像运行的可执行文件；

显然两者的计算机架构、运行环境标识你无法确定它是否一致（毕竟构建的 docker 镜像还可以给他人使用），那么我们就要进行交叉编译，而交叉编译不支持 `cgo`，因此这里要禁用掉它。

关闭 `cgo` 后，在构建过程中会忽略 `cgo` 并静态链接所有的依赖库；而开启 `cgo` 后，方式将转为动态链接

#### 1.3.1.4. 补充：

`golang` 是默认开启 `cgo` 工具的，可执行 `go env` 命令查看

```/
$ go env

GOARCH="amd64"
GOBIN=""
GOCACHE="/root/.cache/go-build"
GOEXE=""
GOHOSTARCH="amd64"
GOHOSTOS="linux"
GOOS="linux"
...
GCCGO="gccgo"
CC="gcc"
CXX="g++"
CGO_ENABLED="1"
...
```

### 1.3.2. GOOS

用于标识（声明）程序**构建环境的目标操作系统**

如：linux、windows

### 1.3.3. GOARCH

用于标识（声明）程序**构建环境的目标计算架构**

若不设置，**默认值与程序运行环境的目标计算架构一致**（案例就是采用的默认值）

如：amd64、386

系统	| GOOS | GOARCH
---|---|---
Windows 32 位 | windows | 386
Windows 64 位 | windows | amd64
OS X 32 位 | darwin | 386
OS X 64 位 | darwin | amd64
Linux 32 位 | linux | 386
Linux 64 位 | linux | amd64

### 1.3.4. GOHOSTOS

用于标识（声明）程序**运行环境的目标操作系统**

### 1.3.5. GOHOSTARCH

用于标识（声明）程序**运行环境的目标计算架构**

### 1.3.6. go build

#### 1.3.6.1. `-a`

**强制重新编译**，简单来说，就是不利用缓存或已编译好的部分文件，直接所有包都是最新的代码重新编译和关联

#### 1.3.6.2. `-installsuffix`

##### 1.3.6.2.1. 作用：

在软件包安装的目录中**增加后缀标识**，以保持输出与默认版本分开

##### 1.3.6.2.2. 补充：

如果使用 `-race` 标识，则后缀就会默认设置为 `-race` 标识，用于区别 race 和普通的版本

#### 1.3.6.3. `-o`

指定编译后的**可执行文件名称**

## 1.4. 小结

大部分参数指令，都有一定关联性，且与交叉编译的知识点相关，可以好好品味一下

最后可以看看 `go build help` 加深了解

```/
$ go help build

usage: go build [-o output] [-i] [build flags] [packages]
...
	-a
		force rebuilding of packages that are already up-to-date.
	-n
		print the commands but do not run them.
	-p n
		the number of programs, such as build commands or
		test binaries, that can be run in parallel.
		The default is the number of CPUs available.
	-race
		enable data race detection.
		Supported only on linux/amd64, freebsd/amd64, darwin/amd64 and windows/amd64.
	-msan
		enable interoperation with memory sanitizer.
		Supported only on linux/amd64,
		and only with Clang/LLVM as the host C compiler.
	-v
		print the names of packages as they are compiled.
	-work
		print the name of the temporary work directory and
		do not delete it when exiting.
	-x
		print the commands.

	-asmflags '[pattern=]arg list'
		arguments to pass on each go tool asm invocation.
	-buildmode mode
		build mode to use. See 'go help buildmode' for more.
	-compiler name
		name of compiler to use, as in runtime.Compiler (gccgo or gc).
	-gccgoflags '[pattern=]arg list'
		arguments to pass on each gccgo compiler/linker invocation.
	-gcflags '[pattern=]arg list'
		arguments to pass on each go tool compile invocation.
	-installsuffix suffix
		a suffix to use in the name of the package installation directory,
		in order to keep output separate from default builds.
		If using the -race flag, the install suffix is automatically set to race
		or, if set explicitly, has _race appended to it. Likewise for the -msan
		flag. Using a -buildmode option that requires non-default compile flags
		has a similar effect.
	-ldflags '[pattern=]arg list'
		arguments to pass on each go tool link invocation.
	-linkshared
		link against shared libraries previously created with
		-buildmode=shared.
	-pkgdir dir
		install and load all packages from dir instead of the usual locations.
		For example, when building with a non-standard configuration,
		use -pkgdir to keep generated packages in a separate location.
	-tags 'tag list'
		a space-separated list of build tags to consider satisfied during the
		build. For more information about build tags, see the description of
		build constraints in the documentation for the go/build package.
	-toolexec 'cmd args'
		a program to use to invoke toolchain programs like vet and asm.
		For example, instead of running asm, the go command will run
		'cmd args /path/to/asm <arguments for asm>'.
...

```
## 1.5. 参考

* [本系列示例代码 ：go-gin-example](https://github.com/EDDYCJY/go-gin-example)
* 书籍：《Go 并发编程实战 第二版》