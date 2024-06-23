# 001-常用yarn命令

Yarn 是一个快速、可靠、安全的依赖管理工具，用于JavaScript项目。以下是一些常用的 Yarn 命令，这些命令覆盖了从项目初始化到依赖管理的各个方面：

1. **安装依赖**
   - `yarn` 或 `yarn install`: 安装项目的依赖项，根据`package.json`和`yarn.lock`文件。
   - `yarn install --production`: 只安装生产环境依赖。
   - `yarn add [package]`: 添加依赖到`dependencies`，并安装。
   - `yarn add [package]@[version]`: 添加指定版本的依赖。
   - `yarn add [package]@[tag]`: 添加特定标签的依赖版本。
   - `yarn add [package] --dev`: 添加依赖到`devDependencies`。
   - `yarn global add [package]`: 全局安装包。

2. **移除依赖**
   - `yarn remove [package]`: 从`package.json`中移除依赖，并从`node_modules`目录中删除。

3. **更新依赖**
   - `yarn upgrade [package]`: 更新单个依赖到最新版本。
   - `yarn upgrade [package]@[version]`: 更新到指定版本。
   - `yarn upgrade`: 更新所有依赖到最新版本。

4. **查看和管理**
   - `yarn list`: 列出项目中所有已安装的依赖。
   - `yarn list --depth [level]`: 限制列出依赖的深度。
   - `yarn info [package]`: 显示包的详细信息，可加上`--json`输出JSON格式。
   - `yarn why [package]`: 查看为什么某个包被安装，以及它是如何被依赖的。

5. **脚本执行**
   - `yarn run [script]`: 执行`package.json`中scripts定义的脚本。

6. **配置与环境**
   - `yarn config set key value`: 设置Yarn的配置项。
   - `yarn config get key`: 获取Yarn配置项的值。
   - `yarn config delete key`: 删除Yarn配置项。
   - `yarn config list`: 列出所有Yarn配置。

7. **缓存管理**
   - `yarn cache list`: 列出缓存中的包。
   - `yarn cache clean`: 清理缓存。
   - `yarn cache clean [package]`: 清理特定包的缓存。

8. **在Hadoop YARN集群中的命令**（非前端开发相关，但在大数据领域中使用）:
   - `yarn application -list`: 查看正在运行的应用列表。
   - `yarn logs -applicationId [applicationId]`: 查看指定应用的日志。
   - `yarn application -kill [applicationId]`: 杀死指定的应用。

9. **查看可用的最新依赖**（查看已过期的依赖项）
    - `yarn outdated`

这些命令覆盖了日常开发中使用Yarn进行依赖管理和项目维护的大部分需求。

