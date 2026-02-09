# 1. 002-Content-type

`Content-Type` 是 HTTP 请求头和响应头中的一个重要字段，用于指示消息主体（body）的 MIME 类型。MIME 类型是一种标准，用于描述文档或文件的格式和编码方式。`Content-Type` 常见的值包括但不限于以下几种：

## 1.1. 常见的 MIME 类型

### 1.1.1. Text 类型

   - `text/plain`: 纯文本
   - `text/html`: HTML 文档
   - `text/css`: CSS 样式表
   - `text/xml`: XML 文档
   - `text/csv`: CSV 文档

### 1.1.2. Image 类型

   - `image/jpeg`: JPEG 图像
   - `image/png`: PNG 图像
   - `image/gif`: GIF 图像
   - `image/svg+xml`: SVG 图像

### 1.1.3. Audio 类型

   - `audio/mpeg`: MPEG 音频文件（mp3格式音频属于这一种）
   - `audio/x-wav`: WAV 音频文件
   - `audio/ogg`: Ogg 音频文件
   - `audio/amr`: AMR 音频文件
   - `audio/mp4`: MP4 音频文件
   - `audio/x-flac`: FLAC 音频文件

### 1.1.4. Video 类型

   - `video/mp4`: MP4 视频文件
   - `video/x-matroska`: Matroska 视频文件
   - `video/x-msvideo`: AVI 视频文件
   - `video/quicktime`: QuickTime 视频文件

### 1.1.5. Application 类型

   - `application/pdf`: PDF 文档
   - `application/zip`: ZIP 压缩文件
   - `application/x-rar-compressed`: RAR 压缩文件
   - `application/json`: JSON 数据
   - `application/xml`: XML 数据
   - `application/javascript`: JavaScript 代码
   - `application/octet-stream`: 二进制数据
   - `application/x-www-form-urlencoded`: 表单提交的数据
   - `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet`: Excel 文件 (.xlsx)
   - `application/vnd.openxmlformats-officedocument.wordprocessingml.document`: Word 文件 (.docx)
   - `application/vnd.openxmlformats-officedocument.presentationml.presentation`: PowerPoint 文件 (.pptx)

### 1.1.6. Other Types

   - `multipart/form-data`: 用于上传文件或多部分消息
   - `message/rfc822`: 电子邮件消息
   - `font/woff`: Web Open Font Format
   - `font/ttf`: TrueType Font
   - `font/otf`: OpenType Font

## 1.2. 使用Content-Type的注意事项

- **字符集**: 有些 MIME 类型可以指定字符集，例如 `text/html; charset=utf-8`。
- **二进制数据**: 如果不确定数据的具体类型，可以使用 `application/octet-stream`。
- **JSON 数据**: 对于 JSON 数据，通常使用 `application/json`。
- **XML 数据**: 对于 XML 数据，通常使用 `application/xml`。
- **音频和视频**: 对于音频和视频文件，确保使用正确的 MIME 类型，例如 `audio/mpeg` 或 `video/mp4`。
- **自定义类型**: 有时会使用自定义 MIME 类型，例如 `application/vnd.example+json`。

## 1.3. 示例

下面是一些 `Content-Type` 的示例：

- **HTML 页面**:

```http
  Content-Type: text/html; charset=UTF-8
```

- **JSON 数据**:

```http
  Content-Type: application/json
```

- **AMR 音频文件**:

```http
  Content-Type: audio/amr
```

- **JPEG 图像**:

```http
  Content-Type: image/jpeg
```

- **ZIP 压缩文件**:

```http
  Content-Type: application/zip
```

- **多部分表单数据**:

```http
  Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW
```

这些 MIME 类型都是合法的，并且在实际开发中经常使用。如果你需要发送或接收特定类型的文件或数据，确保使用正确的 `Content-Type`。