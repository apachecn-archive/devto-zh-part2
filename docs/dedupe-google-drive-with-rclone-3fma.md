# 使用 RClone 对 Google Drive 进行重复数据删除

> 原文：<https://dev.to/sheldonhull/dedupe-google-drive-with-rclone-3fma>

试验

## 一期重复

我从亚马逊云驱动迁移到一个付费的谷歌驱动账户。为了加快这一进程，我使用了一项名为 MultCloud 的付费服务。对我来说，康卡斯特禁止无限制的数据传输，所以通过下载然后重新上传到 Google Drive 来管理 1.5TB 的视频和照片文件移动是一个挑战。

由于 Multcloud 的命中率限制，我遇到了一些问题。结果，我不得不通过他们的 awkard 用户界面重新启动那些仍然失败的作业。我基本上不相信我所有的文件都已经成功传输了。

更糟糕的是，我发现 MultCloud 的编程访问似乎在驱动器中创建了副本。显然，Google Drive 将允许你拥有相同名称的并排文件，因为它不像 Windows 那样以这种方式操作，而是每个文件都被认为是唯一的。文件夹也是如此。

[![Duplicate Images](img/31df58a6643d7ce873f07d6144bc3ab6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EZe143WC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/img/2018-08-20_00-09-09.png)

## rcon

不久前，我偶然发现了 RClone，并跳过了它，只是为了回到关于 Google Drive 的文档，意识到他们对此有特定的功能: [dedupe](https://rclone.org/commands/rclone_dedupe) 。在解决了一些初始问题后，我的情况似乎有所改善，Google Drive 又可以使用了。事实上，现在是打扫房间的时候了。

## 成功运行

我建议你一定要找到[开发者 api 部分](https://console.developers.google.com/apis/api/drive.googleapis.com/)并创建一个 api 访问密钥。如果你不这样做，而只是使用 Oauth2，你将会得到可怕的消息:`Error 403: Rate Limit Exceeded`并且很可能最终花费 30 多分钟试图找出对此该做些什么。 [![403 Rate Limit Messages](img/d0a86eb6a0a0e4a0ca971c34b7700f83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IMdoLX_Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/img/TailBlazer_2018-08-19_18-06-22.png)

你会看到活动开始出现在开发人员控制台，并看到你是如何做你的速度限制。[![Developer Console](img/cbc8f08397ea5c777515c906019fa292.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V_DpecWj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/img/chrome_2018-08-19_23-45-28.png)

## 从简单开始，然后一步一步来

为了避免大的错误，并确认行为如你所愿，从小处着手。在我的脚本底部，我走了一遍我做了什么。

[![Magic As It Works](img/9759eb6e62f449883b9a8d0c3e38d92a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tqshueL6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/img/TailBlazer_2018-08-19_21-14-28.png)

## r clone 的其他炫酷用途

### 合并

虽然我认为 RClone 中的`dedupe`命令是特定于 Google Drive 的，但您可以利用它的逻辑来合并其他系统中的文件夹，以及发出服务器端的远程命令，并且在继续之前不需要在本地下载。

### 服务器端操作

这意味着，基本上我可以通过 MultCloud 节省资金，而不是使用 Rclone 来实现从 Amazon Cloud Drive 到 Google Drive 的复制，所有这些都是远程的服务器端执行，并且没有本地下载来实现这一点。这对于数据迁移有一些很好的应用。

关于他们对服务器端操作支持的更新列表，请看这个页面:[服务器端操作](https://rclone.org/overview/#optional-features)

### AWS

这包括不少漂亮的 S3 操作。尽管我对 AWSPowershell 功能更有经验，但这可能会为同步到 s3 bucket 提供一些很好的替代方案

### 将远程存储挂载为本地

其中还提到了将任何存储系统作为本地驱动器挂载到 Windows 中的能力。参见[rcount Mount 文档。](https://rclone.org/commands/rclone_mount/)。这意味着您可以使用 RClone 安装一个 S3 存储桶作为本地驱动器。在我试用之后，我会试着发布一个更新。很有希望。