# 如何将谷歌云图片转移到其他账户

> 原文：<https://dev.to/mfahlandt/how-to-move-google-cloud-images-to-other-accounts-1iod>

我为我的客户处理多个帐户，我有一些图像，我想用在其中一些帐户上。

你现在有不同的选择来实现这个目标。最好的方法是为你的图片准备一个 docker 文件，并以此作为基线，我们将在另一篇文章中讨论这个选项。

然而，最简单的选择是，如果我们已经有一个基于一台机器的映像，将其复制到另一个帐户。

创建图像有几种方法，最简单的方法是在你的 CloudConsole 中创建。

计算引擎=> Images => [+]创建图像

[![alt text](../Images/d68603af8610187e13537dd9827c3d6f.png "Create Image")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AP5ja7l5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m682lm7t0p3i4gyn2gvr.jpg)

但是，如果您更喜欢控制台，请使用以下命令:

```
gcloud compute images create IMAGE_NAME --source-disk=SOURCE_DISK 
```

现在我们有了一个映像，我们希望将它导出到一个云存储桶中

```
gcloud compute images export --destination-uri gs://bucket-name/imagename.tar.gz --image imagename 
```

在这之后，我们将在我们的云存储中找到 tar.gz 文件的图像。现在我们必须让其他用户也能使用它。为了简单起见，我们将它公之于众。

[![alt text](../Images/bd2836d4429a2e07b2a3fa090869cae0.png "Manage rights")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cTcYcYwe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u5ysbbt7iryvqkzje4sr.jpg)

这里的技巧是，添加一个名为 allUsers 的用户，并授予它读取权限。现在它可以免费下载。

现在我们切换到需要图像的帐户并导入它

```
 gcloud compute images create imagename     --source-uri gs://bucket-name/imagename.tar.gz 
```

就是这样。

如果你想看谷歌云的视频教程，请告诉我

干杯