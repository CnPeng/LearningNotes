原文：[《HTTPS证书转换成PEM格式》](https://help.aliyun.com/knowledge_detail/40526.html)


`PEM` 格式的证书文件（`*.pem`）由Base64编码的二进制内容和开头行（`-----BEGIN CERTIFICATE-----`）、结束行（`-----END CERTIFICATE-----`）组成，支持使用 `notepad++` 等文本编辑器打开。本文介绍了将不同格式的证书转换为 PEM 格式的方法。


## 3.1 CER、CRT 格式证书转换为 PEM 格式

对于 CER、CRT 格式的证书，您可通过**直接修改证书文件扩展名的方式将其转换成 PEM 格式**。例如，将 `server.crt` 证书文件直接重命名为 `server.pem` 。

## 3.2 PFX 格式证书转换为 PEM 格式

PFX 格式的证书一般出现在 Windows Server 服务器中，您可通过 `openssl` 工具进行转换。

例如，通过执行以下两条 openssl 命令即可把 certname.pfx 证书转换成 PEM 格式。

提取私钥命令：`openssl pkcs12 -in certname.pfx -nocerts -out key.pem -nodes`
提取证书命令：`openssl pkcs12 -in certname.pfx -nokeys -out cert.pem`

## 3.3 P7B 格式证书转换为 PEM 格式

P7B 格式证书一般出现在 Windows Server 和 Tomcat 服务器中，您可通过 `openssl` 工具进行转换。

参照以下步骤，将 P7B 格式证书转化为 PEM 格式。

* 将 P7B 证书转换为 CER 格式。例如，执行 `openssl pkcs7 -print_certs -in incertificat.p7b -out outcertificate.cer` 命令将 incertificat.p7b 证书文件转换成outcertificate.cer 格式。
* 获取 outcertificat.cer 文件中`-----BEGIN CERTIFICATE-----`与`-----END CERTIFICATE-----`中的证书内容。
* 将证书内容保存为PEM格式。

## 3.4 DER 格式证书转换为 PEM 格式

DER 格式的证书一般出现在 Java 平台中，您可使用 `openssl` 工具将其转化为 PEM 格式。

例如，通过执行以下两条 `openssl` 命令可以把 certificate.cer 证书转换成 PEM 格式。

* 提取证书：`openssl x509 -inform der -in certificate.cer -out certificate.pem`
* 提取私钥：`openssl rsa -inform DER -outform PEM -in privatekey.der -out privatekey.pem`
