# 使用 Cloudflare 和 AWS S3 实现 99.9%的正常运行时间静态站点部署

> 原文：<https://dev.to/alex_barashkov/999-uptime-static-site-deployment-with-cloudflare-and-aws-s3-1b5i>

欢迎使用我们的简单指南，为您寻找 99.9%的正常运行时间和几乎免费的静态网站部署解决方案，而不必维护和配置您自己的服务器。在本文中，我们将使用 Cloudflare 作为 CDN 服务，使用 AWS S3 作为文件存储。

Cloudflare 在世界各地有 150 多个数据中心，他们的基本计划(免费)对大多数网站来说绰绰有余。虽然它的功能是基本的，但你仍然可以获得 99.9%的正常运行时间，免费的 SSL 证书，DDoS 保护，Brotli 压缩，自动 JS/CSS 缩小，隐藏你的目标服务器 IP，以及正确的缓存设置。

我们决定写这篇文章，因为互联网上的大多数其他指南用多余的设置淹没了人们，这些设置没有任何意义，似乎是从其他网站复制/粘贴的。

### 起动要求

*   AWS 帐户
*   您的现有 NS 已经迁移到 Cloudflare 帐户

迁移到 Cloudflare 是一个非常简单的过程，因为他们有一个很好的 UI/UX。他们将扫描您的 DNS 记录，并建议您添加现有的记录。

### 利弊

✅高性能免费 cdn

●99.9%的正常运行时间；

✅免费 SSL 证书；

✅简单的配置和用法；

❌如果您发现您当前的站点名称正被用作存储桶名称，则不可能将 S3 与 Cloudflare 一起使用(解释如下)；

❌只有一级子域兼容 https 网站的免费计划。

### 1。创建 S3 存储桶

您需要通过 AWS 控制台导航到 S3，然后单击“创建存储桶”。

[![](../Images/0fa1a70b31598bec01294fff02781efa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YkwDy9WJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AXwcFpBTNuzcyVqeDoKWtwQ.jpeg)

在下一个屏幕上，选择您的存储桶名称并定义区域。存储桶名称应该与您的网站名称相同。这是 AWS 的要求，没有它，您将无法通过 Cloudflare 访问您的网站。地区并不重要，因为该站点反正是由 Cloudflare 缓存的。

[![](../Images/24e446d5a56a38427d4490b9f3a7a51e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K2kao9Wl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AaTPbmqoeaBNraAmXZqvIEA.jpeg)
[![](../Images/8b18a0ca90c7311135e574e467fad575.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--jIqsnTZS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AwrcEiT4YBrJBm6dT92AdIQ.jpeg)

在该屏幕之后，单击“下一步”,直到创建您的存储桶。您不需要更改这些设置中的任何内容。注意:如果在这一步中收到“Bucket name 已经存在”的消息，请参阅本文末尾的“故障排除”部分了解详细信息。

### 2。创建 IAM 用户。

首先，我们需要按照这个链接安装 AWS 命令行工具。准备就绪后，转到 AWS 中的 IAM 选项卡，然后转到 Users，并添加用户。请确保您检查了编程访问，因为尽管发布了网站内容，我们并不需要该用户的额外权限。

[![](../Images/2558761fd7d149e1438f99dc2730bd0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UCPnqHP9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AKSkVCsUpAkgvbleaoZmSdQ.jpeg)
[![](../Images/8542d9c6bb155576b1980dc4a70af1b6.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--DlwI06bU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AEO8Vj-MVp_eR1HrKqjkAag.jpeg)

默认情况下，创建您的用户时没有额外的权限。只需点击“下一步”,直到您到达带有用户凭证的“成功”页面

[![](../Images/24094b447ddb44b9f65c487399946936.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VKMb-rJn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AxnwMHpPMQJNiTmJwnP6_Qw.jpeg)

下载你。csv 并安全存储。AWS CLI 需要这些详细信息。

### 3。为您的用户设置正确的权限

创建用户后，单击它，然后在“权限”选项卡上单击“添加内嵌策略”

[![](../Images/12b6b7b4b1b731602fe5c7e1522cd36b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RqM6DbbQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AzTIg5d0QklCOw0xKD4xdKg.jpeg)

您应该会看到策略生成器页面，在该页面中您会看到以下设置:

[![](../Images/2e7a71a12904d38407298704e282dda4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GlOqhjN---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AYDuSWYWBegpyeIKSqH-VBQ.jpeg)

**动作** —用 PutObject 和 DeleteObject 写段；PutObjectAcl 的权限部分

**资源**–你的 ARN 桶名，看起来像这样 arn:AWS:S3:::medium-widget . pixel point . io/*

在 JSON 视图中，应该是这样的

[![](../Images/1c8b3806f7f6cc94cf406dc73ac1aafb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RPgWh2_j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ANji-ebhG8oD6IKySFmJCog.jpeg)
[![](../Images/9490f5b5249380193a9b229995f2994b.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--tdxxuwfj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A5gA2gzAf6K-2ds-taBUWog.jpeg)

查看策略并输入名称。我们现在完成了权限配置，现在可以设置 aws-cli 来推送您的网站。

### 4。使用 AWS CLI 部署

转到终端并运行 aws configure。您将被要求填写来自。在 IAM 用户创建过程中下载的 csv 文件。输入您的访问密钥 ID 和秘密访问密钥。其他字段是可选的，您只需点击“回车”即可。

[![](../Images/4c0b575bebcb923d7d06741a6194bc78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qrMY7WSf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AXi3KQLf_Enk-mpKqfBoW8A.jpeg)

之后，您就可以使用 AWS CLI 部署您的网站了。转到您的网站文件夹；它应该包含 index.html 和其他文件。然后执行:

```
aws s3 cp {folder name} s3://{bucket name} --recursive --acl public-read 
```

Enter fullscreen mode Exit fullscreen mode

**真实例子**:

' AWS S3 CP dist/S3://test . pixel point . io-recursive-exclude '。' DS_Store' - acl 公共读取

在命令中，我们将文件夹复制到 S3 上的远程 bucket。

`--recursive`复制所有嵌套文件夹；
使用 exclude 可以定义不需要复制的文件列表，如一些临时文件、IDE 或 OS 相关文件；公共阅读是使你的网站具有可访问性的必要条件。

我们使用 copy 而不是 sync 文件夹，因为 sync 不会像预期的那样工作，并且有些文件即使在修改后也不会被复制到 bucket 中。

### 5。发布网站

要最终发布您的网站，请转到您的 S3 存储桶列表，单击该存储桶，然后选择属性选项卡。启用静态网站托管，并输入 index.html 作为您的索引文件。复制网站的 url，然后单击“保存”

[![](../Images/2e1f08d01141ee03c498a93324eceb36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j9sl1XnB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AqEYwQJOQAybO04eJZqQd8Q.jpeg)

最后一步是转到您的 Cloudflare DNS 设置并添加新的 CNAME 记录。如上所述，您的存储桶名称应该与您的 CNAME 相同。在 Cloudflare 中的域名字段，你应该放一个没有“http”和“/”的链接到你在 S3 上的网站，就像这样:test.pixelpoint.io.s3-website-us-east-1.amazonaws.com。在名称字段中，你应该输入不带域名的网站名称。例如，对于“test.pixelpoint.io ”,我们只会将“test”作为一个名称。

[![](../Images/d865f99fc12a89ee5c7d223075c4fe11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A5MJmoTd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AznbtRLBskhApFyQWeegxBw.jpeg)

就是这样。现在你的网站应该可以通过你选择的域名访问了。

### 故障排除

*   确保您在 Cloudflare 的 SSL 部分启用了“灵活”设置，否则您可能会面临 SSL 证书问题。
*   要从 www 域重定向到非 www 域，请确保作为第一级子域进行(Cloudflare 限制)。您可以通过 Cloudflare 上的页面规则部分或通过在 S3 创建一个具有重定向规则的新 bucket 来实现这一点。
*   如果您在创建存储桶的过程中收到错误“存储桶名称已经存在”,则此解决方案不适合您，因为在这种情况下您将无法使用 Cloudflare。