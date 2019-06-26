使用 pull 拉取远端代码时提示：

```
fatal: refusing to merge unrelated histories
```

解决方案：

```
git pull 仓库别名 分支名 --allow-unrelated-histories
```

仓库别名指的是通过 `git remote add 仓库别名 仓库地址` 创建的在本地显示的别名 