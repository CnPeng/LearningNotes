# 1. 003-yarn常用命令

## 1.1. 常用命令汇总

Yarn 是一个快速、可靠、安全的依赖管理工具，尤其适用于JavaScript项目。以下是一些Yarn的常用命令：

1. **安装 Yarn**:
   - 全局安装 Yarn: `npm install -g yarn`

2. **查看 Yarn 版本**:
   - `yarn -v` 或 `yarn version`

3. **初始化项目**:
   - 创建 `package.json` 文件: `yarn init`

4. **安装依赖**:
   - 安装 `package.json` 中列出的所有依赖: `yarn` 或 `yarn install`
   - 添加依赖到 `dependencies`: `yarn add [package]`
   - 添加依赖到 `devDependencies`: `yarn add [package] --dev`

5. **升级依赖**:
   - 升级单个包: `yarn upgrade [package]`
   - 升级所有包: `yarn upgrade`

6. **移除依赖**:
   - 从 `node_modules` 和 `package.json` 移除包: `yarn remove [package]`

7. **查看依赖**:
   - 列出所有依赖: `yarn list`
   - 查看某个包的具体信息: `yarn info [package]`

8. **配置设置**:
   - 设置配置项: `yarn config set key value`
   - 查看配置: `yarn config list`
   - 获取特定配置项: `yarn config get key`

9. **镜像配置**:
   - 设置淘宝镜像: `yarn config set registry https://registry.npm.taobao.org`

10. **特殊选项**:
    - 只安装单一版本（避免多版本冲突）: `yarn add [package] --flat`
    - 强制重新下载安装: `yarn install --force`
    - 输出安装时的网络性能日志: `yarn install --har`
    - 不生成或忽略 `yarn.lock` 文件: `yarn install --no-lockfile`
    - 生产环境安装（跳过开发依赖）: `yarn install --production`

11. **运行脚本**:
    - 执行 `package.json` 中定义的脚本: `yarn run [script]`

这些命令涵盖了日常使用Yarn进行依赖管理的基本操作。更多高级功能和详细选项，可以参考Yarn的官方文档。

## 1.2. 部分命令详解

### 1.2.1. 查看某个依赖包的最新可用版本

要查看某个依赖包的最新可用版本，你可以使用 `yarn info` 命令加上包的名称。这将会展示包括最新版本在内的关于该包的详细信息。具体命令如下：

```bash
yarn info [package-name]
```

运行这个命令后，你将在输出的信息中找到 `"latest": "x.x.x"` 这样的字样，其中 `x.x.x` 就是该包的最新版本号。

另外，如果你想要直接在终端中获取最新版本号而不查看全部信息，可以通过组合使用 `yarn info` 和一些命令行工具来提取，例如：

```bash
yarn info [package-name] --json | jq '.data["latest"]'
```

这里使用了 `jq` 工具来解析 JSON 输出并提取最新版本号。请注意，使用此命令需要先确保你的系统中已安装 `jq`。如果未安装，你可以根据你的操作系统安装指南来进行安装。

### 1.2.2. 查看某个依赖包的具体版本号

在使用Yarn对项目依赖进行管理时，要查看某个依赖包的具体版本号，可以通过以下步骤操作：

1. **直接查看`package.json`文件**：打开你的项目中的`package.json`文件，这里会列出所有直接依赖及其版本号。但请注意，这个方法只显示你直接声明的依赖及其版本范围，并不一定展示实际安装的版本（尤其是当你使用了如`^`、`~`这类语义化版本控制符时）。

2. **检查`yarn.lock`文件**：`yarn.lock`文件会锁定每个依赖的具体版本，包括间接依赖的版本，确保每次安装时能得到完全相同的依赖版本。因此，打开`yarn.lock`文件，搜索你要查看的依赖名称，旁边标注的就是实际安装的版本号。

3. **命令行查询**：虽然CSDN的技术社区帖子没有直接提到通过命令行查看单个依赖版本的Yarn命令，但你可以通过以下方式间接实现：
   
   - 首先，进入你的项目根目录。
   - 运行`yarn list`命令，这会列出项目中所有依赖及其版本。这个命令的输出可能比较冗长，但你可以配合grep或find等命令在终端过滤出你需要的信息，比如：`yarn list | grep <dependency-name>`来查找特定依赖的版本信息。

如果需要更精确地控制输出或有其他高级需求，查阅Yarn的官方文档或更新的社区资源，因为随着Yarn版本的更新，可能会有更便捷的命令或选项被引入。