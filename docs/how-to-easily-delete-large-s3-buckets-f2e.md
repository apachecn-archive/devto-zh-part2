# 如何轻松删除大型 S3 桶？

> 原文：<https://dev.to/veermanhas/how-to-easily-delete-large-s3-buckets-f2e>

[原帖](https://blog.totalcloud.io/how-to-easily-delete-large-s3-buckets/)

删除亚马逊 S3 桶可能是一项艰巨的任务。如果其中有大量的对象，这甚至是一个更大的任务，因为您不能删除非空的桶。

“S3cmd”或“S3nukem”等自动化解决方案可能会中断或需要数周时间来删除 S3 存储桶，具体取决于其大小和其中的对象数量。

最佳方法是使用 AWS，它具有自助服务平台的优势，并提供了为 S3 对象设置生命周期策略的选项。

AWS 生命周期策略有助于定义对象生命周期内亚马逊 S3 上的操作。例如，将对象转移到另一个存储类别，对其进行归档，或者在指定的时间段后将其删除。

使用共享前缀(即名称以公共字符串开头的对象)，您可以为 S3 存储桶中的所有对象或对象子集定义生命周期策略。使用生命周期策略，您可以定义特定于当前和非当前对象版本的操作。

注意:自动气象站 S3 内的物体会在一天后永远消失。

此解决方案不是即时的，但需要一天时间来执行，这比其他解决方案要快。

# 以下是如何设置 S3 时段的生命周期

*   登录 AWS 管理控制台，在 https://console.aws.amazon.com/s3/[打开亚马逊 S3 控制台](https://console.aws.amazon.com/s3/)。
*   要为存储桶创建生命周期策略，请从存储桶名称列表中选择存储桶的名称。![Select bucket](img/67e3dfd9d3d9b32bb36395d954713fe4.png)
*   选择“管理”选项卡，然后选择“添加生命周期规则”。![Add lifecycle rule](img/f6694cfee8b1ab56364768b5bd0a6e1b.png)
*   如果要删除整个存储桶，请为规则命名并单击 Next。此外，您可以为具有指定名称前缀的对象(即，名称以公共字符串开头的对象)设置生命周期规则，将生命周期规则范围限制为一个或多个对象标签，并将一个前缀和多个标签组合起来。![Name lifecycle rule](img/91abc4fcdd4ba8a8c21986d20d1a211f.png)
*   单击“转换”选项卡上的“下一步”,不做任何更改。
*   如下图所示选择选项。![Set expiration](img/9e46801b9b9505e6e2d0182b8d1ec02b.png)
*   验证正在审查的规则的设置。![Verify rule](img/5d82c946fa6f547e8515d65567d29497.png)
*   如果需要进行更改，请选择“上一步”。否则，单击保存。

从现在开始，AWS 为你做脏活。S3 会自己做家务，一天后，你桶里的所有东西都会被删除。

如果您经常使用 CloudFormation 或 Terraform 创建堆栈，则可以使用这种方法，如果桶中有对象，则桶不会删除。

无需编写脚本和增加工作量，您可以轻松设置生命周期规则来对 AWS S3 采取行动。

使用生命周期规则可以完成许多事情。你可以在下面了解更多。

[对象生命周期管理](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lifecycle-mgmt.html)

[如何为 S3 时段创建生命周期策略？](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-lifecycle.html)