# 检查文件是否是用载波上传的

> 原文：<https://dev.to/gaetanm/check-if-a-file-was-uploaded-with-carrierwave-1h5l>

# TL；速度三角形定位法(dead reckoning)

用途:

```
user.avatar.present? 
```

Enter fullscreen mode Exit fullscreen mode

不是:

```
user.avatar.file.nil? 
```

Enter fullscreen mode Exit fullscreen mode

# 详细信息

当您想要检查文件是否附加到您的模型时，官方的 Carrierwave 文档说明如下:

> 注意:`u.avatar`永远不会返回 nil，即使没有与之关联的照片。要检查照片是否保存到模型中，请使用`u.avatar.file.nil?`代替。

很好，很有效。但是！如果您使用的是像亚马逊 S3 这样的云存储服务，每次执行行`file.nil?`时，都会向云存储发送一个请求来检查文件的存在。正如你所猜测的，这并不酷，因为它会使你的应用程序变慢。

解决方法就是简单的使用`u.avatar.present?`。这样就不用再打外线了。