# 1. 11-Cron定时任务

> 2022-01-25 12:03

[点击查看原文](https://eddycjy.com/posts/go/gin/2018-04-29-cron/)

## 1.1. 知识点

完成定时任务的功能

## 1.2. 本文目标

在实际的应用项目中，定时任务的使用是很常见的。你是否有过 Golang 如何做定时任务的疑问，莫非是轮询，在本文中我们将结合我们的项目讲述 Cron。

## 1.3. 介绍

我们将使用 [cron](https://github.com/robfig/cron) 这个包，它实现了 cron 规范解析器和任务运行器，简单来讲就是包含了定时任务所需的功能

### 1.3.1. Cron 表达式格式

字段名 | 是否必填 | 允许的值 | 允许的特殊字符
---|---|---|---
秒（Seconds） | Yes | 0-59	| `* / , -`
分（Minutes）	 | Yes | 0-59	| `* / , -`
时（Hours）	 | Yes | 0-23	| `* / , -`
一个月中的某天 （Day of month）	| Yes | 1-31 |	`* / , - ?`
月（Month）	 |Yes | 1-12 or JAN-DEC | `* / , -`
星期几（Day of week）|	Yes | 0-6 or SUN-SAT | `* / , - ?`

Cron 表达式表示一组时间，使用 6 个空格分隔的字段

### 1.3.2. Cron 特殊字符

符号 | 含义
---|---
星号 ( `*` ) | 星号表示将匹配字段的所有值
斜线 ( `/` ) | 斜线用于描述范围的增量，表现为 `“N-MAX/x”`，`first-last/x` 的形式。<br> 例如 `3-59/15` 表示**此时的第三分钟和此后的每 15 分钟，到 59 分钟为止**。<br>即从 `N` 开始，使用增量 `x` 直到该特定范围 `MAX` 结束。它不会重复
逗号 ( `,` ) | 逗号用于分隔列表中的项目。<br>例如，在 Day of week 使用`“MON，WED，FRI”`将意味着星期一，星期三和星期五
连字符 (`-`) | 用于定义范围。例如，`9 - 17` 表示从上午 9 点到下午 5 点的每个小时
问号 ( `?` ) | 不指定值，用于代替 `“ * ”`，类似 `“ _ ”` 的存在，不难理解

### 1.3.3. 预定义的 Cron 时间表

输入	| 简述 | 相当于
---|---|---
`@yearly (or @annually)` | 1 月 1 日午夜运行一次 | `0 0 0 1 1 *`
`@monthly` | 每个月的午夜，每个月的第一个月运行一次 | `0 0 0 1 * *`
`@weekly` | 每周一次，周日午夜运行一次 | `0 0 0 * * 0`
`@daily (or @midnight)` | 每天午夜运行一次	| `0 0 0 * * *`
`@hourly` | 每小时运行一次	| `0 0 * * * *`

## 1.4. 安装

```cmd
$ go get -u github.com/robfig/cron
```

## 1.5. 实践--基于定时任务实现硬删除

在上一篇文章 [《定制 GORM Callbacks》](./10-定制GORMCallbacks.md) 中，我们使用了 GORM 的回调实现了软删除，同时也引入了另外一个问题——就是我怎么硬删除，我什么时候硬删除？

这个往往与业务场景有关系，大致为

* 另外有一套硬删除接口
* 定时任务清理（或转移、backup）无效数据

在这里我们选用第二种解决方案来进行实践

### 1.5.1. 编写硬删除代码

打开 `models` 目录下的 `tag.go`、`article.go` 文件，分别添加以下代码

* `tag.go`

```go
func CleanAllTag() bool {
	db.Unscoped().Where("deleted_on != ? ", 0).Delete(&Tag{})

	return true
}
```

* `article.go`

```go
func CleanAllArticle() bool {
	db.Unscoped().Where("deleted_on != ? ", 0).Delete(&Article{})

	return true
}
```

**注意**：**硬删除要使用 Unscoped()，这是 GORM 的约定**

### 1.5.2. 编写 Cron

在项目根目录下新建 `cron.go` 文件，用于编写定时任务的代码，写入文件内容：

```go
package main

import (
	"time"
	"log"

	"github.com/robfig/cron"

	"github.com/EDDYCJY/go-gin-example/models"
)

func main() {
	log.Println("Starting...")

	c := cron.New(cron.WithSeconds())
	c.AddFunc("* * * * * *", func() {
		log.Println("Run models.CleanAllTag...")
		models.CleanAllTag()
	})
	c.AddFunc("* * * * * *", func() {
		log.Println("Run models.CleanAllArticle...")
		models.CleanAllArticle()
	})

	c.Start()

	t1 := time.NewTimer(time.Second * 10)
	for {
		select {
		case <-t1.C:
			t1.Reset(time.Second * 10)
		}
	}
}
```

在这段程序中，我们做了如下的事情：

#### 1.5.2.1. cron.New()

会根据本地时间创建一个新（空白）的 Cron job runner

```go
func New() *Cron {
	return NewWithLocation(time.Now().Location())
}

// NewWithLocation returns a new Cron job runner.
func NewWithLocation(location *time.Location) *Cron {
	return &Cron{
		entries:  nil,
		add:      make(chan *Entry),
		stop:     make(chan struct{}),
		snapshot: make(chan []*Entry),
		running:  false,
		ErrorLog: nil,
		location: location,
	}
}
```

#### 1.5.2.2. c.AddFunc()

AddFunc 会向 Cron job runner 添加一个 func ，以按给定的时间表运行。

```go
func (c *Cron) AddJob(spec string, cmd Job) error {
	schedule, err := Parse(spec)
	if err != nil {
		return err
	}
	c.Schedule(schedule, cmd)
	return nil
}
```

会首先解析时间表，如果填写有问题会直接 `err` ，无误则将 func 添加到 Schedule 队列中等待执行

```go
func (c *Cron) Schedule(schedule Schedule, cmd Job) {
	entry := &Entry{
		Schedule: schedule,
		Job:      cmd,
	}
	if !c.running {
		c.entries = append(c.entries, entry)
		return
	}

	c.add <- entry
}
```

#### 1.5.2.3. c.Start()

在当前执行的程序中启动 Cron 调度程序。其实这里的主体是 goroutine + for + select + timer 的调度控制

```go
func (c *Cron) Run() {
	if c.running {
		return
	}
	c.running = true
	c.run()
}
```

#### 1.5.2.4. time.NewTimer + for + select + t1.Reset

如果你是初学者，大概会有疑问，这是干嘛用的？

##### 1.5.2.4.1. time.NewTimer(d)

会创建一个新的定时器，持续你设定的时间 `d` 后发送一个 channel 消息

##### 1.5.2.4.2. for + select

阻塞 select 等待 channel

##### 1.5.2.4.3. t1.Reset

会重置定时器，让它重新开始计时

>注：本文适用于 “t.C 已经取走，可直接使用 Reset”。

总的来说，这段程序是为了阻塞主程序而编写的，希望你带着疑问来想，有没有别的办法呢？

有的，你直接 `select{}` 也可以完成这个需求

## 1.6. 验证

```cmd
$ go run cron.go

2018/04/29 17:03:34 [info] replacing callback `gorm:update_time_stamp` from /Users/eddycjy/go/src/github.com/EDDYCJY/go-gin-example/models/models.go:56
2018/04/29 17:03:34 [info] replacing callback `gorm:update_time_stamp` from /Users/eddycjy/go/src/github.com/EDDYCJY/go-gin-example/models/models.go:57
2018/04/29 17:03:34 [info] replacing callback `gorm:delete` from /Users/eddycjy/go/src/github.com/EDDYCJY/go-gin-example/models/models.go:58
2018/04/29 17:03:34 Starting...
2018/04/29 17:03:35 Run models.CleanAllArticle...
2018/04/29 17:03:35 Run models.CleanAllTag...
2018/04/29 17:03:36 Run models.CleanAllArticle...
2018/04/29 17:03:36 Run models.CleanAllTag...
2018/04/29 17:03:37 Run models.CleanAllTag...
2018/04/29 17:03:37 Run models.CleanAllArticle...
```

检查输出日志正常，模拟已软删除的数据，定时任务工作 OK

## 1.7. 小结

定时任务很常见，希望你通过本文能够熟知 Golang 怎么实现一个简单的定时任务调度管理

可以不依赖系统的 Crontab 设置，指不定哪一天就用上了呢

## 1.8. 问题

如果你手动修改计算机的系统时间，是会导致定时任务错乱的，所以一般不要乱来。

## 1.9. 参考

[本系列示例代码 ： go-gin-example](https://github.com/EDDYCJY/go-gin-example)

## 1.10. 补充：

> 以下内容摘自原文评论区

* `c := cron.New()` 应该改为 `c := cron.New(cron.WithSeconds())`，**否则不支持秒级别的定时任务**。