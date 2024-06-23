# 1. 001-errgroup

[https://pkg.go.dev/golang.org/x/sync@v0.4.0/errgroup#example-Group-Parallel](https://pkg.go.dev/golang.org/x/sync@v0.4.0/errgroup#example-Group-Parallel)

使用 `errgroup` 既可以做到并发又可以方便的解决数据赋值和错误处理。

## 1.1. 需求描述

假设我们一个晨会功能，有如下表格：

表格 | 描述
---|---
meeting_group | 会议团队数据。如团队名称、团队负责人等。开会前先建立团队，基于团队进行开会。
group_member | 团队成员数据。如成员uid、归属团队等。
group_task | 团队任务数据。如任务名称、归属团队等。开会时跟进任务进展。
meeting | 具体的晨会数据，如名称、归属团队等。

如果我们需要查询下面这样一个列表：

![](_v_images/20231023165022742_1298368198.png)

列表中既包含了团队基本信息（团队名称、创建日期、负责人信息），又包含了统计信息（近30天晨会次数、待关闭任务数、成员数）。

## 1.2. 使用 sql 实现

如果我们使用 SQL 语句进行查询，就需要多次 `Join` 和 `Group` , 语句比较复杂：

```sql
SELECT
	mgroup.record_id,
	mgroup.name ,
	mgroup.created_at,
	mgroup.manager_ids,
	COUNT(DISTINCT meeting.record_id ) AS meeting_count,
	COUNT(DISTINCT task.record_id ) AS unclosed_task_count ,
	COUNT(DISTINCT member.record_id ) AS member_count 
FROM
	p_eac_morning_meeting_group AS mgroup
	LEFT JOIN p_eac_morning_meeting AS meeting ON mgroup.record_id = meeting.group_id
	LEFT JOIN p_eac_morning_meeting_group_task AS task ON mgroup.record_id = task.group_id AND task.task_status = 1  AND task.deleted_at IS NULL
	LEFT JOIN p_eac_morning_meeting_group_member AS member ON mgroup.record_id = member.group_id  AND member.deleted_at IS NULL AND member.enabled = 1
WHERE mgroup.enabled = 1	AND mgroup.deleted_at IS NULL
GROUP BY
	mgroup.record_id,
	mgroup.name,
	mgroup.created_at,
	mgroup.manager_ids

```

上述语句虽然实现了我们想要的效果，但语句复杂，不易维护。

## 1.3. 使用代码实现

我们可以先查询出全部团队数据，然后遍历团队，并在遍历的同时查询负责人、近30天任务数量、未关闭任务数、成员数量。

如果不使用并发，则查询比较耗时。

如果使用并发，我们就需要考虑如何对错误进行处理，如何填充数据，什么时机退出程序等。

相关代码省略。


## 1.4. 使用 errgroup 改进

在 go 中提供了 `errgroup` 包，其对错误的处理进行的包装，同时也支持并发处理。

我们可以使用 `errgroup` 对代码实现进行优化，示例如下：

```go
// QueryWithStatistics 携带统计信息的晨会团队(供Web端晨会查询主列表使用)
// 统计数据的填充参考 https://pkg.go.dev/golang.org/x/sync@v0.4.0/errgroup#example-Group-Parallel 使用并发和errgroup，替代了复杂的聚合查询
func (a *MorningMeetingGroupService) QueryWithStatistics(ctx context.Context, params schema.MorningMeetingGroupQueryParam, opt ...schema.QueryOpt) (*schema.WebMorningMeetingGroupWithStatiticsQueryRe, error) {
    // 查询团队数据
    groupQueryRe, err := a.groupM.Query(ctx, params, opt...)
    if err != nil {
        return nil, errors.WrapInternalServer("查询团队数据出错", err)
    }

    // 填充近30天晨会数量、团队成员数、待关闭任务数
    results := make([]*schema.WebMorningMeetingGroupWithStatitics, len(groupQueryRe.Data))
    g, bCtx := errgroup.WithContext(context.Background())
    for i, datum := range groupQueryRe.Data {
        i, datum := i, datum

        g.Go(func() error {
            // 查会议数量
            meetingCount, err := a.getMorningMeetingCount(bCtx, datum.RecordID)
            if err != nil {
                return errors.WrapInternalServer("查询晨会-团队出错(晨会数量)", err)
            }

            // 查询未关闭任务数量
            taskCount, err := a.getUnclosedTaskCount(bCtx, datum.RecordID)
            if err != nil {
                return errors.WrapInternalServer("查询晨会-团队出错(未关闭任务数量)", err)
            }

            // 查询团队成员数量（有效成员数量）
            memberCount, err := a.getGroupMemberCount(bCtx, datum.RecordID)
            if err != nil {
                return errors.WrapInternalServer("查询晨会-团队出错(成员数量)", err)
            }

            // 查询并填充团队责任人信息
            managers, err := a.getGroupManagers(bCtx, datum.ManagerIDs)
            if err != nil {
                return errors.WrapInternalServer("查询晨会-团队出错(负责人)", err)
            }

            results[i] = &schema.WebMorningMeetingGroupWithStatitics{
                RecordID:          datum.RecordID,
                Name:              datum.Name,
                MeetingCount:      meetingCount,
                Managers:          managers,
                CreatedAt:         datum.CreatedAt,
                UnClosedTaskCount: taskCount,
                MemberCont:        memberCount,
            }
            return nil
        })
    }
    if err := g.Wait(); err != nil {
        return nil, err
    }
    return &schema.WebMorningMeetingGroupWithStatiticsQueryRe{
        Data:   results,
        PageRe: groupQueryRe.PageRe,
    }, nil
}

```

上述代码中，将填充统计数据的操作都放到了 `errgroup`  中进行操作， 既实现了并发又简化了错误处理。

所以，推荐使用该方式实现。

## 1.5. 附：官方示例

[https://pkg.go.dev/golang.org/x/sync@v0.4.0/errgroup#example-Group-Parallel](https://pkg.go.dev/golang.org/x/sync@v0.4.0/errgroup#example-Group-Parallel)

### 1.5.1. Example (JustErrors) 

JustErrors illustrates the use of a Group in place of a sync.WaitGroup to simplify goroutine counting and error handling. This example is derived from the sync.WaitGroup example at [https://golang.org/pkg/sync/#example_WaitGroup](https://golang.org/pkg/sync/#example_WaitGroup)

```go
package main

import (
	"fmt"
	"net/http"

	"golang.org/x/sync/errgroup"
)

func main() {
	g := new(errgroup.Group)
	var urls = []string{
		"http://www.golang.org/",
		"http://www.google.com/",
		"http://www.somestupidname.com/",
	}
	for _, url := range urls {
		// Launch a goroutine to fetch the URL.
		url := url // https://golang.org/doc/faq#closures_and_goroutines
		g.Go(func() error {
			// Fetch the URL.
			resp, err := http.Get(url)
			if err == nil {
				resp.Body.Close()
			}
			return err
		})
	}
	// Wait for all HTTP fetches to complete.
	if err := g.Wait(); err == nil {
		fmt.Println("Successfully fetched all URLs.")
	}
}
```

### 1.5.2. Example (Parallel) 

Parallel illustrates the use of a Group for synchronizing a simple parallel task: the "Google Search 2.0" function from [https://talks.golang.org/2012/concurrency.slide#46](https://talks.golang.org/2012/concurrency.slide#46), augmented with a Context and error-handling.

```go
package main

import (
	"context"
	"fmt"
	"os"

	"golang.org/x/sync/errgroup"
)

var (
	Web   = fakeSearch("web")
	Image = fakeSearch("image")
	Video = fakeSearch("video")
)

type Result string
type Search func(ctx context.Context, query string) (Result, error)

func fakeSearch(kind string) Search {
	return func(_ context.Context, query string) (Result, error) {
		return Result(fmt.Sprintf("%s result for %q", kind, query)), nil
	}
}

func main() {
	Google := func(ctx context.Context, query string) ([]Result, error) {
		g, ctx := errgroup.WithContext(ctx)

		searches := []Search{Web, Image, Video}
		results := make([]Result, len(searches))
		for i, search := range searches {
			i, search := i, search // https://golang.org/doc/faq#closures_and_goroutines
			g.Go(func() error {
				result, err := search(ctx, query)
				if err == nil {
					results[i] = result
				}
				return err
			})
		}
		if err := g.Wait(); err != nil {
			return nil, err
		}
		return results, nil
	}

	results, err := Google(context.Background(), "golang")
	if err != nil {
		fmt.Fprintln(os.Stderr, err)
		return
	}
	for _, result := range results {
		fmt.Println(result)
	}

}
```

### 1.5.3. Example (Pipeline) 

Pipeline demonstrates the use of a Group to implement a multi-stage pipeline: a version of the MD5All function with bounded parallelism from [https://blog.golang.org/pipelines](https://blog.golang.org/pipelines).


```go
package main

import (
	"context"
	"crypto/md5"
	"fmt"
	"io/ioutil"
	"log"
	"os"
	"path/filepath"

	"golang.org/x/sync/errgroup"
)

// Pipeline demonstrates the use of a Group to implement a multi-stage
// pipeline: a version of the MD5All function with bounded parallelism from
// https://blog.golang.org/pipelines.
func main() {
	m, err := MD5All(context.Background(), ".")
	if err != nil {
		log.Fatal(err)
	}

	for k, sum := range m {
		fmt.Printf("%s:\t%x\n", k, sum)
	}
}

type result struct {
	path string
	sum  [md5.Size]byte
}

// MD5All reads all the files in the file tree rooted at root and returns a map
// from file path to the MD5 sum of the file's contents. If the directory walk
// fails or any read operation fails, MD5All returns an error.
func MD5All(ctx context.Context, root string) (map[string][md5.Size]byte, error) {
	// ctx is canceled when g.Wait() returns. When this version of MD5All returns
	// - even in case of error! - we know that all of the goroutines have finished
	// and the memory they were using can be garbage-collected.
	g, ctx := errgroup.WithContext(ctx)
	paths := make(chan string)

	g.Go(func() error {
		defer close(paths)
		return filepath.Walk(root, func(path string, info os.FileInfo, err error) error {
			if err != nil {
				return err
			}
			if !info.Mode().IsRegular() {
				return nil
			}
			select {
			case paths <- path:
			case <-ctx.Done():
				return ctx.Err()
			}
			return nil
		})
	})

	// Start a fixed number of goroutines to read and digest files.
	c := make(chan result)
	const numDigesters = 20
	for i := 0; i < numDigesters; i++ {
		g.Go(func() error {
			for path := range paths {
				data, err := ioutil.ReadFile(path)
				if err != nil {
					return err
				}
				select {
				case c <- result{path, md5.Sum(data)}:
				case <-ctx.Done():
					return ctx.Err()
				}
			}
			return nil
		})
	}
	go func() {
		g.Wait()
		close(c)
	}()

	m := make(map[string][md5.Size]byte)
	for r := range c {
		m[r.path] = r.sum
	}
	// Check whether any of the goroutines failed. Since g is accumulating the
	// errors, we don't need to send them (or check for them) in the individual
	// results sent on the channel.
	if err := g.Wait(); err != nil {
		return nil, err
	}
	return m, nil
}

```