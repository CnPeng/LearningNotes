# 1. 14-实现excel的导入和导出

> 2022-01-26

[点击查看原文](https://eddycjy.com/posts/go/gin/2018-06-14-excel/)

## 1.1. 知识点

导出功能的实现

## 1.2. 本文目标

在本节，我们将实现对标签信息的导出、导入功能，这是很标配功能了，希望你掌握基础的使用方式。

另外在本文我们使用了 2 个 Excel 的包，excelize 最初的 XML 格式文件的一些结构，是通过 `tealeg/xlsx` 格式文件结构演化而来的，因此特意在此都展示了，你可以根据自己的场景和喜爱去使用。

## 1.3. 配置

首先要指定导出的 Excel 文件的存储路径，在 `app.ini` 中增加配置：

```ini
[app]
...

ExportSavePath = export/
```

修改 `setting.go` 的 `App` struct：

```go
type App struct {
	JwtSecret       string
	PageSize        int
	PrefixUrl       string

	RuntimeRootPath string

	ImageSavePath  string
	ImageMaxSize   int
	ImageAllowExts []string

	ExportSavePath string

	LogSavePath string
	LogSaveName string
	LogFileExt  string
	TimeFormat  string
}
```

在这里需增加 `ExportSavePath` 配置项，另外将先前 `ImagePrefixUrl` 改为 `PrefixUrl` 用于支撑两者的 HOST 获取

（注意修改 `image.go` 的 `GetImageFullUrl` 方法）

## 1.4. pkg

新建 `pkg/export/excel.go` 文件，如下：

```go
package export

import "github.com/EDDYCJY/go-gin-example/pkg/setting"

func GetExcelFullUrl(name string) string {
	return setting.AppSetting.PrefixUrl + "/" + GetExcelPath() + name
}

func GetExcelPath() string {
	return setting.AppSetting.ExportSavePath
}

func GetExcelFullPath() string {
	return setting.AppSetting.RuntimeRootPath + GetExcelPath()
}
```

这里编写了一些常用的方法，以后取值方式如果有变动，直接改内部代码即可，对外不可见

## 1.5. 尝试一下标准库

```go
f, err := os.Create(export.GetExcelFullPath() + "test.csv")
if err != nil {
	panic(err)
}
defer f.Close()

f.WriteString("\xEF\xBB\xBF")

w := csv.NewWriter(f)
data := [][]string{
	{"1", "test1", "test1-1"},
	{"2", "test2", "test2-1"},
	{"3", "test3", "test3-1"},
}

w.WriteAll(data)
```

在 Go 提供的标准库 `encoding/csv` 中，天然的支持 csv 文件的读取和处理，在本段代码中，做了如下工作：

### 1.5.1. `os.Create`：

创建了一个 `test.csv` 文件

### 1.5.2. `f.WriteString("\xEF\xBB\xBF”)`：

`\xEF\xBB\xBF` 是 UTF-8 BOM 的 16 进制格式，在这里的用处是**标识文件的编码格式，通常会出现在文件的开头**，因此第一步就要将其写入。

**如果不标识 UTF-8 的编码格式的话，写入的汉字会显示为乱码**

### 1.5.3. `csv.NewWriter`：

`NewWriter()` 的内部实现如下：

```go
func NewWriter(w io.Writer) *Writer {
	return &Writer{
		Comma: ',',
		w:     bufio.NewWriter(w),
	}
}
```

### 1.5.4. `w.WriteAll`：

```go
func (w *Writer) WriteAll(records [][]string) error {
	for _, record := range records {
		err := w.Write(record)
		if err != nil {
			return err
		}
	}
	return w.w.Flush()
}
```

`WriteAll` 实际是对 `Write` 的封装，需要注意在最后调用了 `w.w.Flush()`，这充分了说明了 `WriteAll` 的使用场景，你可以想想作者的设计用意

## 1.6. 导出

### 1.6.1. Service 方法

打开 `service/tag.go`，增加 `Export` 方法，如下：

```go
func (t *Tag) Export() (string, error) {
	tags, err := t.GetAll()
	if err != nil {
		return "", err
	}

	file := xlsx.NewFile()
	sheet, err := file.AddSheet("标签信息")
	if err != nil {
		return "", err
	}

	titles := []string{"ID", "名称", "创建人", "创建时间", "修改人", "修改时间"}
	row := sheet.AddRow()

	var cell *xlsx.Cell
	for _, title := range titles {
		cell = row.AddCell()
		cell.Value = title
	}

	for _, v := range tags {
		values := []string{
			strconv.Itoa(v.ID),
			v.Name,
			v.CreatedBy,
			strconv.Itoa(v.CreatedOn),
			v.ModifiedBy,
			strconv.Itoa(v.ModifiedOn),
		}

		row = sheet.AddRow()
		for _, value := range values {
			cell = row.AddCell()
			cell.Value = value
		}
	}

	time := strconv.Itoa(int(time.Now().Unix()))
	filename := "tags-" + time + ".xlsx"

	fullPath := export.GetExcelFullPath() + filename
	err = file.Save(fullPath)
	if err != nil {
		return "", err
	}

	return filename, nil
}
```

### 1.6.2. routers 入口

打开 `routers/api/v1/tag.go`，增加如下方法：

```go
func ExportTag(c *gin.Context) {
	appG := app.Gin{C: c}
	name := c.PostForm("name")
	state := -1
	if arg := c.PostForm("state"); arg != "" {
		state = com.StrTo(arg).MustInt()
	}

	tagService := tag_service.Tag{
		Name:  name,
		State: state,
	}

	filename, err := tagService.Export()
	if err != nil {
		appG.Response(http.StatusOK, e.ERROR_EXPORT_TAG_FAIL, nil)
		return
	}

	appG.Response(http.StatusOK, e.SUCCESS, map[string]string{
		"export_url":      export.GetExcelFullUrl(filename),
		"export_save_url": export.GetExcelPath() + filename,
	})
}
```


### 1.6.3. 路由

在 `routers/router.go` 文件中增加路由方法，如下

```go
apiv1 := r.Group("/api/v1")
apiv1.Use(jwt.JWT())
{
	...
	//导出标签
	r.POST("/tags/export", v1.ExportTag)
}
```

### 1.6.4. 验证接口

访问 `http://127.0.0.1:8000/tags/export`，结果如下：

```go
{
  "code": 200,
  "data": {
    "export_save_url": "export/tags-1528903393.xlsx",
    "export_url": "http://127.0.0.1:8000/export/tags-1528903393.xlsx"
  },
  "msg": "ok"
}
```

最终通过接口返回了导出文件的地址和保存地址

### 1.6.5. StaticFS

那你想想，现在直接访问地址肯定是无法下载文件的，那么该如何做呢？

打开 `router.go` 文件，增加代码如下：

```go
r.StaticFS("/export", http.Dir(export.GetExcelFullPath()))
```

若你不理解，强烈建议温习下前面的章节，举一反三

### 1.6.6. 验证下载

再次访问上面的 `export_url` ，如：`http://127.0.0.1:8000/export/tags-1528903393.xlsx`，是不是成功了呢？

## 1.7. 导入

### 1.7.1. Service 方法

打开 `service/tag.go`，增加 `Import` 方法，如下：

```go
func (t *Tag) Import(r io.Reader) error {
	xlsx, err := excelize.OpenReader(r)
	if err != nil {
		return err
	}

	rows := xlsx.GetRows("标签信息")
	for irow, row := range rows {
		if irow > 0 {
			var data []string
			for _, cell := range row {
				data = append(data, cell)
			}

			models.AddTag(data[1], 1, data[2])
		}
	}

	return nil
}
```

### 1.7.2. routers 入口

打开 `routers/api/v1/tag.go`，增加如下方法：

```go
func ImportTag(c *gin.Context) {
	appG := app.Gin{C: c}

	file, _, err := c.Request.FormFile("file")
	if err != nil {
		logging.Warn(err)
		appG.Response(http.StatusOK, e.ERROR, nil)
		return
	}

	tagService := tag_service.Tag{}
	err = tagService.Import(file)
	if err != nil {
		logging.Warn(err)
		appG.Response(http.StatusOK, e.ERROR_IMPORT_TAG_FAIL, nil)
		return
	}

	appG.Response(http.StatusOK, e.SUCCESS, nil)
}
```

### 1.7.3. 路由

在 `routers/router.go` 文件中增加路由方法，如下

```go
apiv1 := r.Group("/api/v1")
apiv1.Use(jwt.JWT())
{
	...
	//导入标签
	r.POST("/tags/import", v1.ImportTag)
}
```


### 1.7.4. 验证

![](pics/20220126162715363_1688227009.png)

在这里我们将先前导出的 Excel 文件作为入参，访问 `http://127.0.0.01:8000/tags/import`，检查返回和数据是否正确入库

## 1.8. 总结

在本文中，简单介绍了 Excel 的导入、导出的使用方式，使用了以下 2 个包：

* [tealeg/xlsx](https://github.com/tealeg/xlsx)
* [360EntSecGroup-Skylar/excelize](https://github.com/360EntSecGroup-Skylar/excelize)

你可以细细阅读一下它的实现和使用方式，对你的把控更有帮助 🤔

## 1.9. 课外

* tag：导出使用 excelize 的方式去实现（可能你会发现更简单哦）
* tag：导入去重功能实现
* artice ：导入、导出功能实现

也不失为你很好的练手机会，如果有兴趣，可以试试

## 1.10. 参考

[本系列示例代码 ：go-gin-example](https://github.com/EDDYCJY/go-gin-example)