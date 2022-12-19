# 在数据库中存储图像文件(二进制)与图像 Base64

> 原文：<https://dev.to/khophi/storing-image-file-binary-vs-image-base64-in-database-26m3>

使用像 Django 这样的框架，文件保存在硬盘上，而指向文件的链接保存在数据库中，这很好。

在我当前上传内容的 API 方法中，我使用标准的表单数据发布(通常的`multipart/form-data`)提交表单，即有图像的部分。

所以我简单地创建了一个有效载荷，模拟通常的表单数据提交(使用 Angular ),然后推送到接受表单数据提交的端点。

最近，我在考虑使用完整的 base64 图像。

就像，

*   用户选择要上传的图像
*   在幕后(在浏览器上)，我将图像编码为 base64 字符串
*   用户提交表单，然后发送通常的 JSON 有效负载，图像字段为 base64

大概是这样的:

```
{ data: 'string'
  image: 'base64 string'
} 
```

Enter fullscreen mode Exit fullscreen mode

我曾经做过 base64 图像方法，它很棒(至少对我来说)。

使用 base64 图像字符串存储在数据库中会有什么性能问题吗？有什么需要注意的地方吗？