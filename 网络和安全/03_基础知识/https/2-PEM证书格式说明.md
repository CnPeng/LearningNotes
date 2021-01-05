原文：[《SSL证书转PEM格式 – HTTPS SSL 教程》](http://blog.itpub.net/31483669/viewspace-2684049/)


证书转成 PEM 格式，如果服务器或者 web 软件只需要一个证书文件就需要这种格式。

证书和 key 按下面顺序排列，另存为 pem 文件就可以了。

```
-----BEGIN RSA PRIVATE KEY-----
(Private Key 私钥)
-----END RSA PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
(SSL certificate 域名证书)
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
(Intermediate certificate  中级证书)
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
(Root certificate 根证书)
-----END CERTIFICATE-----
```

**注意：通常我们对外暴露的只是后面的证书，不需要暴露私钥。**