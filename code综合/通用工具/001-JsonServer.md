[https://www.npmjs.com/package/json-server](https://www.npmjs.com/package/json-server) 是一个本地服务器。

## 1. 快速安装和使用

### 1.1. 基础使用

安装命令：`npm install -g json-server`

然后在本地创建一个 json 文件，假设名称为 `db.json`, 内容为：

```json
{
  "items": ["事项 A", "事项 B", "事项 C"]
}
```

然后从该文件所在的目录执行 `json-server db.json` 或 `json-server --watch db.json` 即可启动服务。

然后从浏览器中输入 `http://localhost:3000/items` 即可读取该文件中的 `items` 中的内容。

### 1.2. 修改端口号

默认的端口号是 3001 ，可以通过 `--port` 指定端口号

`json-server db.json --port 3001` 或 `json-server --watch db.json --port 3001` 





## 2. 其他参考：

* 更多内容可以参考官方文档：[json-server](https://www.npmjs.com/package/json-server)

* [前端接口神器之 json-server 详细使用指南](https://www.cnblogs.com/Megasu/p/15782353.html)
* [json-sever中文文档](https://rtool.cn/jsonserver/docs/installation)
* [Json-server 的使用教程](https://blog.csdn.net/xhmico/article/details/139607652)
* [Android中使用HttpURLConnection请求本地json数据模拟接](https://blog.csdn.net/qq_46269365/article/details/115592995)