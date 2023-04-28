# 0010-重命名gomodule


## 如何修改 go.mod 文件中的 module

要修改 go.mod 文件中的 module，可以按照以下步骤操作：

* 打开终端并进入项目目录。
* 运行命令 `go mod edit -module <new-module-name>`，将 `<new-module-name>` 替换成你想要的新模块名称。
* 运行命令 `go mod tidy`，重新整理依赖项以反映新的模块名称。

这些命令将自动更新 `go.mod` 文件中的 `module` ，并相应地更新其他文件中的导入路径。注意，如果你使用的是版本控制系统（如 Git），你需要提交并推送这些更改，以确保在其他人拉取代码时正确设置模块名称。