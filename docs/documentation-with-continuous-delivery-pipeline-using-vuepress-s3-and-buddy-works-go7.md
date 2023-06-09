# 使用 VuePress、S3 和 Buddy Works 的连续交付管道文件

> 原文：<https://dev.to/fernalvarez590/documentation-with-continuous-delivery-pipeline-using-vuepress-s3-and-buddy-works-go7>

[![](img/6c1b471f4af0f96d4aad8002346e4f40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RWhEXWW7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/0%2AG6f3Yd5joymz5MzS.png)

前几天，有人告诉我，我可以为项目的文档创建一个连续的交付管道，我同意了，对于这个特殊的案例，我们使用 VueJS 的创造者尤雨溪的新工具 VuePress 来为您的站点创建文档。我们为它创造了一些简单的东西，但我如何改进管道的好奇心开始工作，然后，这篇文章出现了。

### 先决条件

*   AWS 帐户
*   [NodeJS](https://nodejs.org) (版本必须≥ 8)
*   [纱线](https://yarnpkg.com/en/)
*   一个[好友工作 CI/CD](https://buddy.works/) 账户
*   Github 的一个账户

### 代码就绪示例

如果您只想测试管道并跳过开发部分(做您自己的文档)，您可以将这个项目分叉为 S3 部署，并开始试验它。

[jefer 590/vue press-S3-CD-示例](https://github.com/jefer590/vuepress-s3-cd-example)

### 设置项目

首先，我们需要使用 yarn 初始化一个新的 NodeJS 项目，使用下面的命令:

```
$ yarn init 
```

然后，是时候为这个项目安装唯一的依赖项了，即 [VuePress](https://vuepress.vuejs.org/)

$纱线添加-D vuepress

之后，你必须遵循 VuePress 的[入门指南](https://vuepress.vuejs.org/guide/getting-started.html#inside-an-existing-project)。如果你准备好了，让我们继续前进！

### 设置管道

在 Github 上创建一个新的公共或私有项目，你可以随意命名。

在进入 Buddy Works 之前，我们需要另一个配置，即在 [AWS IAM](https://aws.amazon.com/iam/) 上以编程方式创建一个用户，这将允许 Buddy Works 将构建的资产部署到一个 S3 存储桶中，显然，还有一个配置为支持静态网页的 S3 存储桶。

在 AIM 控制面板中，您必须转到*用户>添加用户*，然后在下一页中，您将编写具有编程访问权限的 AIM 用户名

[![](img/9d237accf43833e8531986c228d9bcf1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7Ol-2LSj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AH6VpflfqiVpmWfTlSsxpMg.png)

在权限屏幕中，您将附加一个现有策略。出于演示目的，我们将使用***“amazons 3 full access”***策略。您可以创建自己的策略，只对好友作品有必要的权限，您可以在此链接中查看它们

[![](img/a385950a897bcd585ba790a945f5967d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WABPbLD6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AB9A-jtXlG5gjpXslsnJy3Q.png)

在下一个屏幕中，验证您的用户信息，然后单击 ***“创建用户”*** ，在下一个屏幕中，您将获得访问密钥 ID 和秘密访问密钥。保存此信息，因为您以后会用到它。

[![](img/78af48c053b5b5cc9addb8004b1ee54b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CPCmsC22--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbNMlW5UG5D2z8wsqgzI1vA.png) 

<figcaption>我们的用户准备好了，谢谢 IAM！</figcaption>

是时候创造我们的 S3 桶了！转到 S3 仪表板，点击按钮 ***“创建桶”*** ，会出现一个模态，开始创建桶。第一步，添加一个作为域名一部分的存储段名称，并选择您的首选地区。

[![](img/ffa29e04ba8c84e3d0e2c6a079c8a188.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IjzRAZRM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AwAyBwO4Q5swqibhdctaZPg.png)

在下一步中，您将把 ***【版本控制】*** 添加到桶中。静态网站配置稍后提供。

[![](img/bde701cb33a3d943694a711557e11aa9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9N3ATj4w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJQb0-gc5EtOC-nfPO9EiJw.png)

下一步，向所有用户授予读取权限。

[![](img/d884811e2964f64d193b49c41f53d060.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1gMtqiRD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKD6Zzu6P3PYg1QwO34-h_Q.png)

最后一步是回顾你的选择，就是这样！你有了一个全新的 S3 桶，但只剩下一件事，为这个桶和那个桶设置 web 主机，在你的桶的仪表板中，转到*属性，然后点击 ***“静态 Web 主机”****

 *[![](img/600cbe86c1a14f3e56dcfc3dd44f068b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iw3cMqvS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJFO7YQdezC2JOy7Sx_QpNg.png)

又会出现一堆选项，点击 ***“用这个桶托管一个网站”*** 。在 ***索引文档*** 上，您将输入值***【Index . html】***，在 ***错误文档*** 上，您将添加值***【404 . html】***。这两个文件都是由 VuePress 构建过程生成的。你救了，就这样！AWS 上一切就绪。

[![](img/0eb893b0453b9384150c5a5c96179120.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vtGT5neQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AY7EJJCDfbkv6daJe7o-okA.png)

现在我们来看有趣的部分，配置管道！进入你的好友工作项目仪表板，点击 ***“创建新项目”***

[![](img/6641e68a61e10aeb8e255a2201d94602.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z7LEejWB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuiIV89fDsimFnjaqTsf0dA.png)

然后，一个包含你所有回复的列表将会出现。搜索您为 VuePress 项目创建的那个

[![](img/5f122240f8505adb9d81184980c7f1b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y09lzs_p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APqfZn-6hC7fZioUIek79dg.png)

选择项目后，将出现该项目的管道仪表板。点击 ***【添加新管道】***

[![](img/5387787ce36c48c5d3bf1d2f482ac658.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NXdkmp_k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATTQpEz_g2YwkfmUTxBa5jQ.png)

在下一个屏幕中，我们将设置如何触发管道，以及您希望触发哪个分支或标记。在我的例子中，我将在每次推送 master 时触发这个管道。您可以添加目标网站来预览已部署的项目。对于本例，我们将使用 S3 静态网站 url，其模式为. s3-website-.amazonaws.com

[![](img/9abd6d6f683d890eb7bce1fa9d3b7996.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xpi0zyN2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVY3Fuwzel3Tzb-Ns5NY2yg.png)

之后，会出现一个屏幕，为您的管道选择一个新的操作。因为我们需要构建 VuePress 项目来将其上传到我们的 S3 桶中，所以我们将使用一个节点动作。

[![](img/462bfd05bea93bfc42c1304664c41b15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qj7eUj6C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARyAdJUGkXHvSLNBvXHECDQ.png)

是时候配置我们的任务了！我们需要 yarn 来构建下载包并下载所有东西，所以让我们在这个动作中安装 yarn。转到 ***“环境定制”*** ，添加下一个命令$ npm install -g yarn，并尝试安装 yarn，在命令屏幕中，使用$ yarn -v 命令。保存操作，点击*“运行管道”*按钮，手动运行我们的任务

[![](img/1bd62cb33cdbec426a980cee0f6c9d0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gwUrEMJi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/251/1%2Augds9dR-L2kmUPeY90c5Gg.png)

如果一切顺利，检查你的节点操作日志，你将看到最新的 yarn 版本

[![](img/265d137f7e6e7f839203594489b95ad0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AHqrmINM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AS3TQMn_SUhmhN4T1VZAj9g.png)

好吧！Yarn 已经在我们的管道中准备好了，让我们将构建操作添加到我们的节点任务中！使用命令$ yarn 和$ yarn build:docs 安装依赖项并构建文档。

[![](img/c2a105510f88b9462e8d6fd27e7159cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jdUTxclH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_sJRNV0qZIqbFjebKxDyTQ.png)

最后需要做的是设置节点运行时，从 Docker 公共中心获取最新的 8 版本的节点

[![](img/bb9c851e27055e92e51499b786e56114.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b-QSCl2T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AunfODlz05Gh10YkNQZPegw.png)

最后，我们在 node 上执行了第一个操作！！！

[![](img/431c239b6490020687780ebe5eb993c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kZoZyyWR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFH5IVuQ1RX3CNu1CmsL7VA.png) 

<figcaption>我们全新的行动已经准备就绪！</figcaption>

身材应该不错！现在让我们实际上传我们所有的文件到我们的 S3 桶。为管道创建另一个操作，但这次搜索 S3 操作。它将要求你 S3 用户访问密钥 ID 和秘密访问密钥。使用您用 S3 策略创建的那个。

[![](img/f0453b8be1b631a37987cc8f79947f45.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4kB1bGt1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AchDTNQ1FlozZOUcEkPPIUw.png)

如果集成顺利，应该会出现一个 S3 上传配置的屏幕。确保您正在使用 ***“管道文件系统”*** 来使用过去操作中的构建档案，并确保您的源路径是/docs/。vue press/区

[![](img/123abc44cd1524ef310142548252fcb8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EGW3GUUl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2gOqF-1wybzp1HtOt4dwRg.png)

此外，确保您将使用 S3 帐户进行此操作，并将所有内容上传到具有公共访问权限的 bucket 根目录。最后，为你的行为起一个名字。

[![](img/9e08f06c5822e21cc04f1f1e130c9c58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pRBLl-Mr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AiX0g_80PeM1rj2JirrF83Q.png)

点击 ***“添加此动作”*** 以完成我们的第二个动作

[![](img/7faa6cd9643e2017b059739906584004.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5BVVdHQ3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ada1Z3i-uz0mUrrWsw8G3xA.png)

一切都很顺利，我们有一个步骤来建立和上传，但我认为唯一剩下的事情是一个行动来通知我，当这是部署成功。让我们通过创建一个新的电子邮件操作来处理电子邮件。

[![](img/b902a669829a2011d99ec31649eb40a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l-LJ__9f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AosYTRlZl_4FwZZE5v1ngLQ.png)

让我们在将要通知的电子邮件中添加一条成功消息

[![](img/4114d522e3ff28b55f1e00ac229c2c32.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nww58xN9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMbWf3jUAIrWrt_Xr6yjlFQ.png)

最后，让我们添加行动的名称。

[![](img/9b16999fc04980f7f9145dde567ae123.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1n2jFvTB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUq1KDPL7omyZjp0PNDqgfQ.png)

我们的管道应该是这样的。

[![](img/cfe7bfbae0d260f439b849b1f6c87e3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PFBeSiHk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AI7coS2XTYBB-d5GiTBd-_Q.png) 

<figcaption>漂亮！</figcaption>

现在，是真相大白的时候了。让我们通过点击**运行管道**按钮来测试我们的新管道。如果一切顺利，您可能会看到您的管道*已完成*，并且您可能会收到一封电子邮件。

[![](img/c39e73a399c446639891818fe87a2686.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C74ae4-w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ab9jN_kKkOQ8r3onhHKPWoQ.png) 

<figcaption>巴迪工作一切顺利！</figcaption>

[![](img/83351eb2b505fa5d20cd015505d5ee80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XCeKBzBP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A4jNmKE3ns8UtqhW-kxgcEQ.png) 

<figcaption>我收到邮件了！</figcaption>

当然，我们的网站必须是在线的。

[![](img/ae169fe3e8bee3a2a6a4662dbecd2644.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cGISRX59--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUlRRw8FL8i8QunwOSvpC2w.png) 

<figcaption>我们的网站工作非常出色！</figcaption>

[![](img/010a6bef06aed477cba45de4a56806ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--agiRQA9L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVIKak45G3d5w1CX0lucRxA.png) 

<figcaption>回购中完成的工作看起来不错！</figcaption>

### 结论

有了 buddy，我们可以创建非常好、非常简单、非常棒的 CI/CD 工作流。VuePress 是静态文档的优秀工具，因为它使用了 Markdown + Vue 组件+模板。如果我们一直上传对这个回购的修改，回购就会自动触发。建议你去查一下！

#### 🙌感谢您的阅读，下次再见🙌*