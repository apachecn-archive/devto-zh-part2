# 工艺 CMS 3 定位指南-欢迎！

> 原文：<https://dev.to/gaijinity/craft-cms-3-orientation-guide-welcome-43fp>

# Craft CMS 3 迎新指南——欢迎光临！

### 如果你是 CMS 3 新手，这是你的入门指南！我们会帮助你理解建造一些东西的工艺

安德鲁·韦尔奇

[![Welcome To Craft Cms Orientation Guide](../Images/2fa820f09b0ad881b39efa90ea47bb3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VL13cVMA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/welcome-to-craft-cms-orientation-guide.jpg)

## 欢迎光临

随着 [Craft CMS 3](http://craftcms.com/) 的发布，这个平台已经[在最近](http://trends.builtwith.com/cms/Craft)的流行城市成长，所以我们有很多新的平台人进入社区。

[![Craft Cms Usage Trends](../Images/5f65e1e2a230e85d0da15ca377edee9a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yNcaro0X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x1020_crop_center-center_100_line/craft-cms-usage-trends.png)

[![Craft Cms Marketshare Gains](../Images/cdd1223493fb2f47472725d2109aaba1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VYGh6NgL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x589_crop_center-center_100_line/craft-cms-marketshare-gains.png)

本文旨在作为一个入门指南，帮助您了解什么是 Craft，您可能如何使用它，并了解 Craft 使用的所有程序。

无论你只是来打造你自己，或者你是一个新手开发者，或者你只是浏览风景看看有什么，这都是为你准备的博客。

本着“授人以渔”的精神，我们也将提供一个资源列表，在那里你可以得到帮助，并学习更多的手艺！

## 什么是工艺 CMS？

Craft CMS 3 是一个内容管理系统，由 Pix el & Ton ic 创建，它是为那些想从系统中得到更多东西的人而设计的。它遵循用于内容创作的管理后端和用于前端的服务器端托管内容的转换模型。

[![Craft Cms Admin Cp](../Images/4096ce5a04805a24ccc03cc44acea772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MPtoz4oY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x637_crop_center-center_100_line/craft-cms-admin-cp.png)

Craft 使用 PHP(需要 7.0 或更高版本)作为基础语言，健壮的 Yii2 框架位于其上。然后，Craft 本身被*放置在*之上，提供内容模型、管理接口和一组 API，让您可以管理编辑内容的最新用户。

它使用 [Twig](http://twig.symfony.com/doc/2.x/) tem plat ing 语言与其 API 和 render content 进行交互，但它也可以通过 [Ele ment API](http://github.com/craftcms/element-api) 或通过中的[craft QL plug g 作为“无头”con tent serv er。](http://github.com/markhuot/craftql)

所以你可以用 Craft 通过 HTML/ Twig 来呈现你的内容，或者你可以用它来创作内容，然后用 [Vue JS](http://vuejs.org/) 或 [React](http://reactjs.org/) 或任何尤尔喜欢的方式来呈现你的内容。

<aside>It’s up to you.</aside>

与许多基于 API 的 CMS 不同，Craft 是自托管的，所以没有月费或使用费。参见文章[自由职业者应该如何做虚拟主机](https://dev.to/gaijinity/how-agencies-freelancers-should-do-web-hosting-ih4-temp-slug-5481912)来获得更多关于自主主机的信息。

[![Craft Cms 3 Blank Canvas](../Images/3afef8dade3aaf6907ded4935da7858c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rw_Ry7uV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-cms-3-blank-canvas.jpg)

Craft 与其他此类 CMS 系统的不同之处在于其后端丰富的内容创作系统，以及本质上是一张空白画布的前端。

对于第一次接触 Craft 的人来说，它缺乏假设可能会让他们感到不快。没有“页面”或“帖子”或类似的概念。为什么不呢？因为如果这些是 CMS 的基础构件，那么它将会对网站的结构做出一些大胆的假设。

也没有“主题”前沿是你的；Craft 不像 CSS 主题那样注入任何东西。相反，你可以使用你提供的(或你自己的)CSS 框架来创建一些自定义的、个性化的和令人敬畏的东西。正如在[开发模式中所讨论的，一个正在流行的框架是](http://devmode.fm/)[顺风 CSS](http://tailwindcss.com/docs/what-is-tailwind/) 。fm 插曲[尾风 CSS util i ty-first CSS 与亚当瓦森](http://devmode.fm/episodes/tailwind-css-utility-first-css-with-adam-watham)。

<aside>The answer to ​“How do I do X in Craft?” is often ​“How­ev­er you want!”</aside>

Craft CMS 中内容的基本构建模块包括:

*   **条目**。条目包含您想要在网页上显示的内容
*   **猫 e 类**。分类允许你为你的内容创建税收经济
*   **资产**。资产是可上传的文件，如图像、pdf 和其他内容
*   **用户**。用户是可以用来登录系统和编辑内容的成员帐户

几乎工艺中的每一个内容都是一个元素。条目、类别、用户、资产等等都是元素。这意味着他们都有相同的能力:

*   **字段**。所有元素都可以添加允许您向其中添加客户数据的字段
*   **关系**。所有元素都可以与其他元素相关联
*   **可搜索的**。所有元素自动材质都可以对其内容进行索引，并且可以进行搜索

[![Craft Cms Field Layout](../Images/77b1296c5391a1fb57352421f630be17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yf6tf-vB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x754_crop_center-center_100_line/craft-cms-field-layout.png)

在手工艺中，段落是用来组织你的作品的。部分可以是:

*   **通道**。通道是相似内容的流；它们包含条目
*   **结构**。结构就像频道，但它们可以人工排序，并以层次结构的方式排列
*   **罪格**。单页用于一次性内容，如单页单数据

当一个页面被加载到前端时，Craft 会找到与 URI 匹配的小枝模板，并使用从相应条目注入的数据对其进行复制。

有了这些构建块和它们的能力，您就可以设计您的网站所需要的任何信息体系结构。当然还有更多可用的，如标签、部分等等，但这些都是你应该意识到的基本要素。

Craft 还具有强大的本地 iza tion、 [mul ti-lin gual 和 mul ti-site sup port](http://docs.craftcms.com/v3/sites.html) 内置于系统的基础中。

你可以用 Craft 创建一个网站的一种方法是为你的内容作者创建一个灵活的“内容生成器”,但这并不是唯一的方法。在 Craft CMS 中创建内容生成器的[有一个使用矩阵块字段创建“内容生成器”的简单例子](https://dev.to/gaijinity/creating-a-content-builder-in-craft-cms-29ap-temp-slug-2753628)

[![Craft Cms Live Preview](../Images/df3552227a13ac52151000e182565e52.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UOB9M6E4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x733_crop_center-center_100_line/craft-cms-3-live-preview.png)

还为内容作者提供了一个实时预览，ecosys tem 中的一个强大的 plu g(带有一个存储的集成 plu g ),以及一个强大的[调试器/分析器](http://nystudio107.com/blog/profiling-your-website-with-craft-cms-3s-debug-toolbar)来帮助加速项目的开发。

您甚至可以制作一个完整的定制应用程序，正如在[用定制模块增强 Craft CMS 3 网站的文章](https://dev.to/gaijinity/enhancing-a-craft-cms-3-website-with-a-custom-module-33g1-temp-slug-8101978)中所讨论的。

## 使用 cas es 进行 Craft CMS 3

有句老话是这么说的:“使用合适的工具完成工作。”这是一个经久不衰的概念，因为它适用于从汽车、写作到选择内容管理系统(CMS)的每一件事情。Craft 也不例外。

[![The Right Tool For The Job](../Images/f5b4539c9778ea9b25e461e522314afa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d1zuSsMu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/the-right-tool-for-the-job.jpg)

在最近的 inter view [Craft CMS 3 与 Pix el & Ton ic 创始人 Bran don Kel ly！](http://devmode.fm/episodes/craft-cms-3-with-pixel-tonic-founder-brandon-kelly)在[开发模式下。pixel&tonic 的首席执行官 FM](http://devmode.fm/)表示，对于需要简单网站的朋友来说，他从来没有修改过 Craft CMS。

这并不是说 Craft *不能用来创建简单的网站，而是说它简单得有些过头了。当您需要时，您会想要使用 Craft CMS:*

*   一个定制的，客户设计的网站，从竞争中脱颖而出
*   从设计、SEO 和性能角度对前端进行严格的现场控制
*   一个丰富、健壮、用户友好的内容创作系统

Craft CMS 已经被用于创建各种东西，从名人网站到完整的电子商务商店(使用 [Craft Com merce](http://craftcommerce.com/) )到客户内部网窗口。

您可以用它来创建一个传统的网站，或者您也可以利用健壮的 Yii2 框架来创建一个定制的应用程序，它需要一个对内容作者友好的后端。

查看[事后分析:LinkedIn Tal ent Intel intelligence experience](https://dev.to/gaijinity/post-mortem-linkedin-talent-intelligence-experience-4kn2-temp-slug-9519038)文章，了解 Craft 的独特使用案例。以下是使用 Craft CMS 的网站列表:

*   [Moz .com](http://moz.com/)
*   [空足伯爵夫人](http://barefootcontessa.com/)
*   [Nook 睡眠系统](http://www.nooksleep.com/)
*   [Asso ci at ed Press 博客](http://blog.ap.org/) &
*   [视频](http://www.ideo.com/)

…以及更多。查看 [Craft CMS 案例研究](http://craftcms.com/in-the-field),了解 Craft 如何被用于解决现实世界问题的更深入讨论。

## 我该何去何从？

这篇文章只是你的观点；现在是时候开始学习 Craft CMS 了。这是我们教你如何钓鱼的地方。

[![Craft Learning To Fish](../Images/3d7260a23aec3b8325f9b70ac3376162.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iM3ZTLg2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-learning-to-fish.jpg)

以下是一些我发现非常有用的资源:

*   **[工艺 CMS 3](http://docs.craftcms.com/v3/)**。所有工艺的明确来源
*   **[手艺 CMS 栈交换](http://craftcms.stackexchange.com/)** 。一个寻找你可能有的问题的答案的好地方
*   **[点全了](http://dotall.com/)** 。目前每年举行一次的官方工艺会议
*   。与志同道合的工艺开发人员的粉丝实时聊天
*   **[工艺探索](http://craftquest.io/)** 。通过 Ryan Irelan 提供的这些教程视频，快速了解 Craft CMS
*   **[敬畏某飞船](http://github.com/craftcms/awesome)** 。精心制作了一些工艺品、资源和闪亮的东西。
*   **[飞船链接列表](http://craftlinklist.com/)** 。一个收集关于手工 CMS 的兴趣链接的公共图书馆
*   **[开发模式。调频吊舱施放](http://devmode.fm/)T3。适应现代 web 开发的播客(通常带有工艺倾向)**
*   **[nys tu dio107 .com 博客](http://nystudio107.com/blog)** 。纯粹的海王星主义……但是在工艺上有许多有用的发展

[建立一个新的工艺 CMS 3 项目](https://dev.to/gaijinity/setting-up-a-new-craft-cms-3-project-107b-temp-slug-1098923)如果你想开始的话，这篇文章将更深入地介绍如何建立一个新的工艺项目。《T2》是《T4》的免费视频系列。io 帮助你满怀信心地着手一项手工艺项目。

现在出去创造一些奇妙的东西吧！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计