[https://www.npmjs.com/package/json-server](https://www.npmjs.com/package/json-server) 是一个本地服务器。

安装命令：`npm install -g json-server`

然后在本地创建一个 json 文件，假设名称为 `db.json`, 内容为：

```json
{
  "items": ["事项 A", "事项 B", "事项 C"]
}
```

然后从该文件所在的目录执行 `npx json-server db.json` 或 `json-server --watch db.json` 即可启动服务。

然后从浏览器中输入 `http://localhost:3000/items` 即可读取该文件中的 `items` 中的内容。

更多内容可以参考官方文档：[json-server](https://www.npmjs.com/package/json-server)

