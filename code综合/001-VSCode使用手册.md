# 1. 003-VSCode使用手册

## 1.1. 创建代码模板（用户代码片段）

在 VSCode 中，你可以通过创建用户代码片段（User Snippets）来实现类似于 Android Studio 中的代码模板功能。以下是具体步骤：

### 1.1.1. 打开用户代码片段设置

1. 打开 VSCode。
2. 点击左侧活动栏中的齿轮图标，然后选择 `用户代码片段`。
3. 选择你正在使用的编程语言（例如 JavaScript、TypeScript、Python 等）。

### 1.1.2. 创建代码片段

在打开的代码片段文件中，添加你的代码片段模板。例如，创建一个 `toast` 代码片段：

```json
{
  "Toast Message": {
    "prefix": "toast",
    "body": [
      "Taro.showToast({",
      "  title: '$1',",
      "  icon: 'none',",
      "  duration: 2000",
      "});"
    ],
    "description": "Show a toast message"
  }
}
```

### 1.1.3. 使用代码片段

1. 在代码编辑器中，输入 `toast`。
2. 按下 `Tab` 键或 `Enter` 键，代码片段会自动展开为你定义的模板。

### 1.1.4. 示例代码片段文件

以下是一个完整的示例代码片段文件，假设你正在为 JavaScript 创建代码片段：

```json
{
  // 代码片段名称
  "Toast Message": {
    // 触发代码片段的前缀
    "prefix": "toast",
    // 代码片段的内容
    "body": [
      "Taro.showToast({",
      "  title: '$1',",
      "  icon: 'none',",
      "  duration: 2000",
      "});"
    ],
    // 代码片段的描述
    "description": "Show a toast message"
  }
}
```

### 1.1.5. 代码片段占位符

在代码片段中，你可以使用占位符来定义可编辑的区域：

- `$1`, `$2`, ...：定义光标的跳转位置。
- `${1:defaultText}`：定义带有默认文本的占位符。
- `$0`：定义光标的最终位置。

### 1.1.6. 示例：创建多个代码片段

你可以在同一个代码片段文件中创建多个代码片段。例如：

```json
{
  "Toast Message": {
    "prefix": "toast",
    "body": [
      "Taro.showToast({",
      "  title: '$1',",
      "  icon: 'none',",
      "  duration: 2000",
      "});"
    ],
    "description": "Show a toast message"
  },
  "Console Log": {
    "prefix": "log",
    "body": [
      "console.log('$1');"
    ],
    "description": "Log output to console"
  }
}
```

通过以上步骤，你可以在 VSCode 中创建和使用代码片段模板，类似于在 Android Studio 中使用代码模板的方式。这将大大提高你的编码效率。

## 1.2. Markdown 编辑

### 1.2.1. 安装

先安装 `Markdown All in one` 插件：

![](pics/20250326111559778_567466533.png)

### 1.2.2. 使用命令执行相关任务

先打开一个 markdown 文档：

![](pics/20250326112503791_1078817305.png)

然后打开命令面板：

> 如下图所示，也可以通过快捷键 `Shift + CMD + A` 打开命令面板。注意，该快捷键根据个人设置会有不同。

![](pics/20250326111749213_1280095793.png)

然后在命令窗口中输入 ：`Markdown All in One`（或者直接输入 `markdown`），即可查询所有可用的命令：

![](pics/20250326113050450_478013514.png)

如上图，自动添加序号配置的快捷键为 `CTRL + CMD + S`


## 1.3. 覆盖默认快捷键

### 1.3.1. 修改快捷键

使用这种方式只可以修改，无法清空。

#### 1.3.1.1. 方式1

![](pics/20250326115451568_552529445.png)

#### 1.3.1.2. 方式2

先打开命令面板（也可以直接通过 `Shift + Cmd + A` 或 `Shift + Cmd + P` 打开）：

![](pics/20250326115654267_170233658.png)

然后打开系统的快捷键配置界面（）：

![](pics/20250326115203030_717479506.png)

### 1.3.2. 修改或清空快捷键

先打开用户自定义配置页面：

![](pics/20250326115401217_557379437.png)

再打开系统快捷键配置页面：

![](pics/20250326120027824_1805690616.png)

上图中的 6 就是上面需要的 command 命令，打开该窗口的目的就是确认功能对应的命令。

接下来直接在用户配置的 json 文件中编辑即可，通过这种方式，既可以更改快捷键，也可以清空快捷键。


## 1.4. 快捷执行脚本

加入项目目录下有 xx.sh 文件，我们执行时通常是打开终端，然后手动输入 `./xx.sh` 来执行，如果该脚本文件的名称比较长，输入就会比较麻烦。所以需要配置快捷执行脚本的方式。

在 GoLand 中，可以通过下图的方式配置快捷执行方式：

![](pics/20250529190319631_2001239247.png)

那么 VsCode 中该如何配置呢？我们可以通过 tasks.json 配置终端任务来运行的脚本。

### 1.4.1. 步骤：

#### 1.4.1.1. 基础步骤

* 打开命令面板 (`Cmd + Shift + P` 或 `Ctrl + Shift + P`)
* 输入并选择：Tasks: Configure Task

![](pics/20250529191339391_939145015.png)

* 点击 "Create tasks.json file from template"（如果不存在）
* 选择模板为 Others
* 替换生成的 tasks.json 内容如下：

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Run modelGenWithInput4Manual.sh",
            "type": "shell",
            "command": "./modelGenWithInput4Manual.sh",
            "options": {
                "cwd": "${workspaceFolder}"
            },
            "problemMatcher": ["$eslint-stylish"],
            "group": "build"
        }
    ]
}
```

#### 1.4.1.2. 快捷步骤

直接在当前项目根目录下的 `.vscode` 中新建 `tasks.json` 文件，并编辑内容即可。（如果 json 文件不存在则新建，存在则直接编辑即可。）

![](pics/20250529191642880_205461655.png)




### 1.4.2. 使用方法：

按 `Cmd + Shift + B` 或 `Ctrl + Shift + B` 打开任务面板，然后选择你定义的任务运行即可。

注意：`group` 的名称需要设置为 `build` 或者 `test` ，否则在任务面板中找不到该任务。

![](pics/20250529191813382_40331209.png)


### 1.4.3. 确保脚本具有执行权限

在终端中执行以下命令，确保脚本有执行权限（如果不执行该命令，可能会出现 `permission denied` 提示）：

```bash
chmod +x ./modelGenWithInput4Manual.sh
```