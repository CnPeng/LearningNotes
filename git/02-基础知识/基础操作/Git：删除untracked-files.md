# 1. 删除 untracked 文件

## 1.1. 预览并删除

在 Git 中批量移除 untracked（未跟踪）文件，最常用且安全的命令是 `git clean`, 但删除后无法通过 Git 恢复，所以使用时需格外谨慎，必须先执行 `git clean -n` 预览哪些文件将要被删除。

* 预览将要被删除的文件：

```bash
git clean -n
# 或
git clean --dry-run
```

执行上述命令后，将看到类似下面的内容：

```text
Would remove build/
Would remove src/temp.txt
Would remove .env
```

* 执行删除命令：

```
git clean -f
# 或
git clean --force
```

## 1.2. 常用选项


| 选项 | 含义 | 说明 |
|------|------|------|
| `-n` / `--dry-run` | 预览 | 显示将被删除的内容，不实际删除 |
| `-f` / `--force` | 强制删除 | 实际执行删除操作（必须指定才能删除） |
| `-d` | 包含目录 | 同时处理未跟踪的目录（否则只处理文件） |
| `-x` | 包括忽略文件 | 删除 `.gitignore` 中忽略的文件 |
| `-X` | 仅忽略文件 | 只删除 `.gitignore` 中忽略的文件 |
| `-e <pattern>` | 排除 | 排除特定文件或目录不被删除 |



## 1.3. 常用选项组合

### 1.3.1. 常用组合选项

|            命令             |                                         说明                                          |
| ------------------- | ---------------------------------------------------------- |
| `git clean -n`     | 预览将被删除的文件（不实际删除）                                        |
| `git clean -f`     | 删除 untracked 文件（不包括目录）                                      |
| `git clean -nd`   | 预览将要被删除的文件和目录                                                 |
| `git clean -fd`   | 删除 untracked 文件 + 目录（最常用）                                  |
| `git clean -fdx` | 删除 所有 untracked 内容（包括 `.gitignore` 忽略的文件） |
| `git clean -fdX` | 仅删除 `.gitignore` 中忽略的文件（大写 X）                      |

### 1.3.2. 排除特定文件或目录

|                           命令                           |                              说明                              |
| -------------------------------------- | ------------------------------------------ |
| `git clean -n -e *.txt`                 | 预览将要被删除的文件，但 排除 `*.txt` 文件 |
| `git clean -fd -e .env -e *.key` | 删除所有 untracked，但保留 .env 和 *.key      |
| `git clean -fd -e uploads/`          | 删除所有 untracked，但保留 uploads/ 目录    |


### 1.3.3. 只删除特定类型文件

|                                            命令                                             |                            说明                             |
| -------------------------------------------------------------- | ----------------------------------------- |
| `git clean -f --include="*.log"`                                     | 删除所有 .log 文件。（只删除特定类型文件） |
| `git clean -fd --include="build/" --include="dist/"` | 删除 build/ 和 dist/ 目录                            |

## 1.4. 最佳实践


```bash
# 步骤 1：查看当前状态
git status

# 步骤 2：预览将要删除的内容（文件 + 目录）
git clean -nd

# 步骤 3：确认无误后执行删除
git clean -fd

# 步骤 4：再次确认状态
git status
```