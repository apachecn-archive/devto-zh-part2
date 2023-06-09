# Azure Blob 的 Ghost 文件夹？

> 原文：<https://dev.to/catriname/azure-blobs-ghost-folders-1mkn>

本周，我不得不处理我在开发环境中构建的“上传图片到 blob”应用程序。它工作得很好，但是需要进行配置才能在生产中工作。

## 我的设置

对于整个应用程序，我使用了 [Azure Samples 将图像](https://github.com/Azure-Samples/storage-blob-upload-from-webapp/blob/master/ImageResizeWebApp/ImageResizeWebApp/Helpers/StorageHelper.cs)上传到存储(内置。网芯)。其中，appsettings.json 中的配置是这样的:

```
 "AzureStorageConfig": {
        "AccountName": "",
        "AccountKey": "",
        "ImageContainer": "images",
        "ThumbnailContainer": "thumbnails"
    } 
```

Enter fullscreen mode Exit fullscreen mode

在 Azure Portal 中很容易找到帐户名和 AccountKey。对于容器名，我使用了 [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) ，这样我就可以全面了解容器及其 blobs。

## 问题

问题是，在我的开发环境中，我是直接上传到容器的。在上面的例子中，我上传到“图片”容器。但是，在我的生产环境中，我的 ImagesContainer 有两个文件夹:

*   图片/小
*   图像/大

我尝试将“images container”:“images”改为:

*   " ImagesContainer ":"图像/小"

然后

*   " ImagesContainer ":"图像\小"

不过，我运气不好。找不到请求的 URI。

## 解

你看到的文件夹不是真正的文件夹，把它们看作是文件名的一部分，文件夹分隔符\，*意味着你把文件夹名加在文件名的前面*。

例如，上述配置中的正确答案是:

*   "图像容器":"图像"

但是，当我在代码中指定要上传的文件名时，它应该是:

*   “small\mypic.jpg”

这会将文件定向到图像容器，然后它自己的文件名会将它定向到哪个文件夹和文件名下来存储它。

Azure 存储有时有点奇怪，它有层次结构和区分大小写的行为，但一旦你意识到了，就很容易适应。

### 快速提示

该文件名中没有前导“\”。它应该是:

*   “small\mypic.jpg”