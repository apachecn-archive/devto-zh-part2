# 16 只丑陋的虫子 Snap 懒得去修复 Snap 工具包和他们教的课程

> 原文：<https://dev.to/bbookman/16-ugly-bugs-snap-didnt-bother-to-fix-for-snap-kit-and-lessons-they-teach-636>

[原件出现在介质上](https://medium.com/adventures-in-ios-mobile-app-development/16-ugly-bugs-snap-didnt-bother-to-fix-for-snap-kit-and-lessons-they-teach-4dd6c1aa567)
[![](img/21f644160ef374de4b4ad6c41c9aad01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dLcqGtPC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2A-xvbtrI9d5aRFYR4.png)
[![](img/fd86b7f5cbe3365fe8ca7860caf6b473.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yott-GMi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2AI33vVGAVcpNuyCVF.jpg)

## Snap 有许多业务问题，包括 Snap Kit SDK 的糟糕推出

我是一名使用 Swift 4 和 iOS 12 的 iOS 爱好者开发者，已经使用 Snap Kit 好几个月了。在超过 10 篇中型文章中，我记录了我与 SDK 的斗争和胜利。我遇到了许多问题。

## Snapchat Snap Kit SDK Primer

Snap Kit 是 Snap 超越其主要应用产品的尝试。为了发展和保持相关性，Snap login 必须像脸书、Twitter 或谷歌的应用程序认证一样普及。GIFY 已经将自己插入到许多其他应用程序中，Snap 有机会在 AR 内容、故事、镜头和滤镜方面做同样的事情。

Snap Kit SDK 是 Snap 变粘的尝试。

所描述的 Bug 是有目的地以“Bug 风格”编写的，希望每个 Bug 都能在 Snapchat 应用程序的 Snap Kit SDK 中实际归档

下面记录的一些错误是吹毛求疵的，然而，Snap 不允许哪怕是最轻微的开发者摩擦发生。Snap 的未来在一定程度上取决于 Snap Kit 的成功，该业务几乎没有犯错的余地。赢得开发人员社区对采用 Snap Kit SDK 至关重要。

此外，其中一些可以被认为是特性请求、文档请求或“非 bug”的东西。作为一个有 20 年软件行业经验的人，我说这些是需要解决的“失败”(也就是 bug)。

## 商业课程

对于每一个 bug，我都添加了关于改进软件发布的商业方法的建议
[![](img/45fd8b575b40c70a8bb4c102fb68bfef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ooBro9ie--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2A6i1aTGVmYQm7Mcgs.jpg)

# 疯狂的

## 1 —单点支持

**再现步骤**

1.  前往[https://docs.snapchat.com/docs/](https://docs.snapchat.com/docs/)
2.  注意文字>反馈？我们定期更新我们的文档，为您提供进行创造性的、简单的集成所需的一切。如果有些事情对你不起作用，请向[snapkit-support@snap.com](mailto:snapkit-support@snap.com)报告。

自动回复短信说:

> 你好。“请访问 support.snapchat.com/news/snapkit.了解更多关于 Snap Kit 的信息，如果您还有其他问题，请联系我们，地址是 support.snapchat.com/i-need-help.，谢谢！

自动回复存在一些问题:

*   告诉寻求客户支持的客户查看新闻博客。新闻博客不是作为客户支持工具而设计的。
*   告诉客户如果他们需要进一步的帮助，请转到“我需要帮助”。Snapchat 网站的这一部分不是为了向开发人员或客户提供 Snap Kit 支持而设计的。查看该 URL 会发现，没有办法报告特定的 Snap Kit SDK 问题，用户会觉得他们在那里报告的任何事情都会以黑洞告终。
*   自动回复并没有说任何人真的会阅读客户发送的电子邮件，也没有帮助。只说去哪里，读什么。这对客户来说一点也不友好。

**建议的缓解措施**

奖励自动回复:

感谢您联系 Snap Kit 支持。此自动回复是为了让您知道我们已收到您的请求，并将很快与您联系

**商业课**

当介绍当前产品之外的产品时，回顾所有客户参与向量。适当配备人员。从一开始就包括质量保证和技术支持(包括所有利益相关者)

[![](img/003da10788fb248d62bcd1d9a77668b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RFNyD5wl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2A4cskYBastNPTg8De.jpg)

## 2 —将 Snap Kit SDK 支持选项添加到“我需要帮助”中

**再现步骤**

1.  前往[http://support.snapchat.com/i-need-help](http://support.snapchat.com/i-need-help)

**结果**

注意可用的选项:

> 我们能帮你什么？
> 
> *   我的帐户登录
>     
>     
> *   报告安全问题
>     
>     
> *   我的 Snapchat 坏了
>     
>     
> *   我的快照消失了。。。

这些都没有为寻求 Snap Kit SDK 帮助的人提供明确的选项

**建议的缓解措施**

添加 Snap Kit SDK 支持选项

**商业课**

与错误#1 相同。任何产品的发布都不仅仅是产品。这不仅仅是一个声明。产品发布团队必须是全方位的，并且代表公司中有客户接触点的每一个部分，或者是利益相关者

[![](img/4821417ab992b505165f9d0f3d687f90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9q6UwoqR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2A35rDznurqWcluDNq.png)

## Twitter 上的 Snapchat 支持不提供 SDK 支持

**再现步骤**

1.  前往[https://twitter.com/snapchatsupport](https://twitter.com/snapchatsupport)
2.  询问一个 Snap Kit 问题，例如“我们是否要求该客户提供 Snap Kit 帮助？”

**结果**

出现了许多有问题的结果。然而，关键是通过 twitter 提供支持的团队
显然没有处理 Snap Kit SDK
问题的人员。

上面第二步之后的最初反应是一个链接到
[https://kit.snapchat.com/](https://kit.snapchat.com/)的机器人。显然这不是一个有用的
回答问题。另外，请看这篇 bug 文章正上方的图片

下一个回应似乎也是固定的，可能是一个机器人

> 我们需要进一步调查此事。前往 spr.ly/help，选择符合您问题的
> 选项。。。

该 URL 指向“我需要帮助”页面，并产生了上面的错误#2，并且与错误#1 也有一些关联。

**建议的缓解措施**

修改 Snapchat 支持 Twitter 帐户上的机器人/自动响应，使
能够正确处理 Snap Kit 查询。

**商业课**

像你的目标客户一样思考。遵循你的目标和测试的工作流程，
测试，测试。网站体验、许可体验、入职体验、
测试一切可能与客户互动的东西——尤其是当
交付的产品与你的产品线完全相似的时候。

在
发布之前，利益相关方应对每个客户接触点拥有签准权

[![](img/88147bcb29d7b24b3cfc99617c2b3879.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G_PPt-xh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2AI_OIaePwnPZ2k41X.jpg)

## Creative Kit 文档中的代码示例错误

**再现步骤**

1.  前往[https://docs.snapchat.com/docs/creative-kit/](https://docs.snapchat.com/docs/creative-kit/)
2.  向下滚动到此处

**结果**

这条线是不正确的

应该是 **SCSDKPhotoSnapContent** 而不是 SCSDKSnapPhotoContent(参见下面的
错误 5)

**建议的缓解措施**

请更正文档。

SDK 本身存在一个更大的问题。命名不一致——参见
错误 5

**商业课**

所有文件都应由专业的质量保证工程师进行测试。文档是一种产品。文档是产品
的一部分，就像其他部分一样。

还有，吃你自己的狗粮！

[![](img/44ecd768d0cf7fb4a678cef7aa2bbfd3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uRPVgXxu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2A9zjf-1Su1udkgWJa.jpg)

## 5-命名与创意套件不一致

在 Creative Kit 中有一个名为“SCSDKSnapPhoto”的方法

仔细注意名字的模式。第一个字是**抓拍**第二个字是
照片**照片**

Creative Kit 也有方法“SCSDKPhotoSnapContent”

请注意，命名方法并不一致。单词 **Snap** 出现在
**Photo** 之前，但是在这里，单词 **Photo** 出现在单词 **Snap** 之前

SCSDK* *抓拍* **拍照*
SCSDK *拍照**T5】抓拍*内容

Creative Kit 中的这些方法对重复了相同的问题:

【scsdksnapvedio】和【SCSDKVideoSnapContent】

在“SCSDKSnapVideo”中，单词 **Snap** 出现在 **Video** 之前，然而在这里
“SCSDKVideoSnapContent”单词 **Video** 出现在单词 **Snap** 之前

**建议的缓解措施**

1.  浏览所有文档和示例代码以确保准确性
2.  考虑重命名，使这些对保持一致
3.  为开发人员提供多种途径来了解变更并进行全面的文档审查

**商业课**

进行多次代码评审，让组织中的人以全新的视角参与进来

[![](img/7beb7d790106199b241aa38b254b42c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8oFjnN7G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2A0P_WzVsrzHwHjYKG.jpg)

## 6-登录范围信息放错了位置

**再现步骤**

1.  前往[https://docs.snapchat.com/docs/login-kit/](https://docs.snapchat.com/docs/login-kit/)

**结果**

标题为**理解范围**的整个部分似乎放错了位置。
部分是登录工具包的第一部分。

此外，该部分提供了 iOS Info.plist 的代码**然而**该页面是一个
通用页面，包含 iOS 和 Android 说明。因此，以严格的 iOS 信息开始文档
是很奇怪的。

**建议的缓解措施**

将此文本移至本页的**入门**部分，移至更合理的
位置。

**商业课**

测试文档。运行“吃你自己的狗粮”测试会话。

[![](img/1c1bf362f2ad5d4a59ce29bca4d0fa89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5NuXknB3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2AAVKd7RXnfSCUKcyK.jpg)

## 7-嵌入式二进制文件可以被替换或扩充，以包括

椰子足类

**再现步骤**

1.  前往[https://docs.snapchat.com/docs/login-kit/](https://docs.snapchat.com/docs/login-kit/)
2.  检查**入门**部分

**结果**

> *在 Xcode 的 app 项目中，将 SCSDKCoreKit.framework 和
> SCSDKLoginKit.framework 添加到通用嵌入式二进制文件中。*

上面的文字是正确的，但不完整。Snap Kit 现在提供了 Cocoapod
安装解决方案。

**建议的缓解措施**

添加有关如何使用 Cocoapod 选项安装 Snap Kit 的信息

**商业课**

准备好清单、签核和工作流，这样当发生变化时
(例如发布一个 cocoapod)其他有客户接触的人可以行动起来并审查
面向客户的资产。

[![](img/10ee2d74ddaa4f543902d00b6db96f7c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KFr1onWE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2A_OSPtTa9JlCDDYAi.jpg)

## 8-Snap Kit SDK 命名

**再现步骤**

1.  Go to [https://github.com/SnapKit/SnapKit](https://github.com/SnapKit/SnapKit) or [http://snapkit.io/](http://snapkit.io/)

**结果**

这个已经很好建立的代码行的名字是 SnapKit，它会造成
与 Snap Kit 的混淆。

**建议的缓解措施**

虽然晚了，但是注意到这个问题还是很重要的。SnapKit 已经存在了至少 4 年，并且相当受 iOS 开发者的欢迎。在选择 Snap Kit 的名字之前，谷歌搜索一下
应该是明智的。此外，如果 Snap
没有使用外部名称搜索服务，那么考虑在未来命名时使用
将会很有帮助

**商业课**

运行命名搜索

[![](img/6813bb231ef03c99cbb3d1d63e0e6dbd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--80rzTb4r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2ATdEUjfYdsF1JEb2t.jpg)

## 9-开发人员门户的用户界面令人困惑

1.  用用户 id 和密码在[http://kit.snapchat.com](http://kit.snapchat.com/)登录

[![](img/3029ddb9e686ebb211b3544df25dafea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--98iLPJZ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2AEk7Kh-GhwZuWxVXg.png)

这个用户界面并不能清楚地理解有两个不同的区域。上图最能说明问题。不太清楚
这里有两段。它们之间没有界限，右侧的标题
*应用信息*没有清楚地表示左侧是将
应用于生产(而不是右侧的开发)。

**建议的缓解措施**

至少，在两个区域*应用信息*和
*开发应用信息*之间划一条垂直线。

此外，更改 OAUTH2 客户端 ID 字段的标题，使其显示为
* *生产**OAUTH2 客户端 ID 和* *开发**OAUTH2 客户端 ID

**商业课**

不要假设任何事情。让专业质量工程师参与产品发布的所有方面(T2)。当 UX/用户界面是产品的一部分时，雇佣 UX/用户界面设计师。
是的，考虑任何面向客户的东西——比如开发门户——都是产品的**部分
！**

[![](img/989ed0968c3aa15ee8018adabd546afd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oUFyJ9G2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/0%2AGvPd0vyVc79b7vLQ.jpg)

## 10-取消会话链接方法不包括抛出的错误

1.  前往[https://docs.snapchat.com/docs/login-kit/](https://docs.snapchat.com/docs/login-kit/)
2.  检查有关取消会话链接的信息

**结果**
在 unlink
失败的情况下，两种方法都不会抛出错误

*   scsdkloginclient . unlinkcurrentsessionwithcompletion
*   scsdkloginclient . unlinkallsessions with completion

### 建议的缓解措施

包含引发错误的代码

**商业课**

仔细考虑产品的用例。想通可能失败的
点。

[![](img/f9e7e995a7a790fcb38eea63c88cdf63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sQ3_jVYV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2AGMVgcavj4mdFRxMwF4kqWw.jpeg)

## 11-Snap Kit Twitter 帐户不活跃，也不为开发者提供信息

**再现步骤**

1.  前往[https://docs.snapchat.com/docs/](https://docs.snapchat.com/docs/)
2.  注意文本

> 若要了解我们的 SDK 的最新动态，请在 Twitter 上查看最新动态并关注@snapkit。

1.  在 Twitter 上查看来自@snapkit 的推文

虽然推特账号@snapkit 建立于 2018 年 5 月，现在是 2018 年
9 月，但是总共有 4 条推文。没有一个是针对软件开发人员的

**建议的缓解措施**

拥有该帐户的营销团队需要开始制作和发布
更多内容，该帐户还应该发布反映文档中的
“查看新内容”声明的信息。

**商业课**

与 bug #7 和其他问题一样，必须有一个内聚的发布策略，该策略包括产品的所有方面及其向市场的交付。应咨询可能拥有面向客户资产的所有部门

[![](img/a6667e68a460f6674b14b1a49f9161d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K68yfm3o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2AXrfkf3IP0RDMI3wVlYaSCQ.jpeg)

## 12—iOS 第一段的 API 参考似乎已过时

**再现步骤**

1.  前往[https://docs.snapchat.com/docs/api/ios/](https://docs.snapchat.com/docs/api/ios/)
2.  注意文本

**结果**

> 本文档假设您已经与我们联系过

我认为这篇文章是在 Snap Kit 处于测试阶段或拥有少量受管理的受众时写的。并且，文字混乱。这里没有关于某人如何联系的说明。

**建议的缓解措施**

删除这句话，或者，如果需要联系，解释如何联系和为什么联系

**商业课**

测试文档。

[![](img/c30e7ad13c1443a2996a34c8172d1af1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sAqKihkQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2ApDrmvNLp72HkYT9KhumJ1g.jpeg)

## 13-快照套件支持样板文件没有帮助

**再现步骤**

1.  向[snapkit-support@snap.com](mailto:snapkit-support@snap.com)发送支持请求
2.  在上面的错误#1 和其他错误中记录的初始自动响应之后，等待并阅读“真人”支持响应

**结果**

> *Chuck (Snap Connect)您好，感谢您的举报！为了让我们
> 更好地调试这个，您能提供以下信息吗*
> 
> *-用户名*
> 
> *-客户端 ID*
> 
> *-使用的特定 URL 或资产*
> 
> *- Info.plist(如果没有敏感信息)代码示例(如果可能的话)
> 屏幕录制和/或截图*
> 
> *- IP 地址*

**建议的缓解措施**

我已经发送了许多不同的支持请求，并且看到了 Chuck 的这个
样板回复。由于每个 SDK 项目都是不同的，此
文本没有帮助。事实上，我问了一个关于
开发者门户的具体问题，然后看到了这段文字。这让我觉得我的支持问题
甚至没有被阅读，支持技术人员也懒得阅读或关心
我的问题。

要么修改文本使其更具包容性，要么改变 SOP，使支持代表不再使用样板文件(或更慎重地使用)

(IP 地址..实际上为了什么？这不是一款企业应用。)

**商业课**

在发布前浏览模拟客户体验。测试客户
工作流程。

[![](img/04b03a82467da8bad4e0980a59b810d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OXQ8XihA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2ALXboWKmfr0857092m2rEWg.png)

## 14-缺少示例代码

**再现步骤**

1.  前往[https://github.com/Snap-Kit](https://github.com/Snap-Kit)

**结果**

*   没有 Android 的代码示例
*   iOs 有一个示例，但只涵盖了 Bitmoji

**建议的缓解措施**

为所有套件以及 iOS 和 Android 提供示例代码

**商业课**

过度支持你的目标客户，而不是支持不足。当产品是新产品并且是公司战略的重要组成部分时，这一点就更加重要了。显然，Snap Kit 是 Snap 在
市场行动的关键部分

[![](img/3ddf748705ef43b96b6dbe55ba7250f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hc-AyoHn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2A4xzADAmZ6Xuam0ir2l1uHQ.jpeg)

## 15 —快照套件支持(更多)样板文件没有帮助

**再现步骤**

1.  向[snapkit-support@snap.com](mailto:snapkit-support@snap.com)发送支持请求
2.  在上面的错误#1 和其他错误中记录的初始自动响应之后，等待并阅读“真人”支持响应

**结果**

> 我们的团队正在调查，如果我们需要更多信息，将会跟进。
> 
> 谢谢，恰克

本回复不包括任何关于回复时间的信息。顾客不知道会发生什么。

**建议的缓解措施**

要么不使用样板文件，要么更改样板文件文本，以包括用于期望值设置的措辞

**商业课**

参见错误 13 和其他。在发布任何产品之前，技术支持和质量保证应该成为客户的最后一道防线

## 16-意外状态代码

**再现步骤**

我现在正在与 snap support 合作解决这个问题。除了我的代码可以随意复制之外，我目前不知道如何复制。与代码
段相关的是:

```
let api = SCSDKSnapAPI(content: snap)

api.startSnapping { error in

if let error = error {
         print(error.localizedDescription)
     } 
```

**结果**

> 快照套件服务器响应了意外的状态代码。
> 
> 2018–09–14 15:22:22.249561–0700 fun snap[7881:4578160][snap kit]
> path =/v1/SDK/metrics/operational headers _ subset = {
> 
> " X-Cloud-Trace-Context " = " d 55dd 084290 c 4941 bb 1149 ce 33521 f 98/0；o = 1”；

**建议的缓解措施**

1.  错误需要是人类可读的，文本应该尽可能有助于自助故障排除
2.  不管根本原因是什么，这里有一个错误
3.  发布知识库或其他面向客户的自助服务工具

## TL；速度三角形定位法(dead reckoning)

Snap 发布的 Snap Kit 几乎在所有可以想到的方面都存在缺陷

商业经验包括:

*   发布管理必须在发布过程的早期将所有的涉众召集到一起。他们还必须在整个发布周期中咨询这些利益相关者
*   发布的涉众包括来自所有面向客户的部门以及质量保证部门的代表
*   文档是产品的一部分，必须和其他部分一样经过严格的审查
*   门户网站是产品的一部分，必须和其他部分一样接受严格的审查
*   在发布前进行“吃你自己的狗粮”
*   测试端到端的客户体验，包括技术支持服务、网站、社交媒体平台(您有渠道)、文档、许可等
*   进行“死角”审查，以帮助根除不完整、不准确或过时的文档和公告。例如，GitHub 上可能有需要更新的代码。

## 如果你喜欢这篇文章，试试这些

*   [Snapchat Snapkit 开发者支持可悲可悲](https://medium.com/adventures-in-ios-mobile-app-development/snapchat-snapkit-developer-support-sadly-sad-89d63011c6ad)
*   [介绍 Snapchat Snapkit DIY 文档](https://medium.com/adventures-in-ios-mobile-app-development/introsnapkitdiy-d7b1b677c7d9)
*   [非常破碎的硅谷招聘流程以及如何修复它](https://medium.com/adventures-in-ios-mobile-app-development/the-very-broken-silicon-valley-hiring-process-and-how-to-fix-it-683e0c851f14)

## 接触

*   [LinkedIn](http://linkedin.com/in/brucebookman)
*   推特: [@](https://medium.com/@brucebookman) brucebookman
*   乳齿象:@[Bruce bookman @乳齿象. technology](mailto:brucebookman@mastodon.technology)
*   [GitHub](https://github.com/bbookman)

[←
上一个](https://medium.com/adventures-in-ios-mobile-app-development/the-very-broken-silicon-valley-hiring-process-and-how-to-fix-it-683e0c851f14)
| [下一个
→](https://medium.com/adventures-in-ios-mobile-app-development/snapchat-snap-kit-sdk-tutorial-for-ios-swift-311863074bab)

*   [iOS](https://medium.com/tag/ios?source=post)
*   [卡扣套件](https://medium.com/tag/snapkit?source=post)
*   [Snapchat](https://medium.com/tag/snapchat?source=post)
*   [安卓](https://medium.com/tag/android?source=post)
*   [增强现实](https://medium.com/tag/augmented-reality?source=post)

从短暂的欢呼到起立鼓掌，鼓掌表示你有多喜欢这个故事。

## [布鲁斯·布克曼](https://medium.com/@brucebookman)

## 【iOS 移动应用中的冒险

开发]([https://medium . com/adventures-in-IOs-mobile-app-development？source =页脚 _ 卡片](https://medium.com/adventures-in-ios-mobile-app-development?source=footer_card)

一个 iOS Swift 开发者的旅程。问题、解决方案、观点等等