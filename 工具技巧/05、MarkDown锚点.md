MarkDown 页面内锚点，


MarkDown 本身没有锚点语法，但由于 MarkDown 内部支持 html ，所以，可以使用 html方式实现：


要点击的地方这么写：

```html
<a href="#入门" target="_self">点击跳转到 入门 章节</a>
```

目的地这么写:

```html
<h3 id = "入门">2、入门</h3>
```


注意：

- 如果把 .md 文档导出为 pdf ，那么锚点点击后不会跳转。其他样式会保留

- 如果导出为 html 可以实现跳转
