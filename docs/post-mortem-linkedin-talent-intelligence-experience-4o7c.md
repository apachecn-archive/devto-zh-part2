# 事后分析:LinkedIn 人才智能体验

> 原文：<https://dev.to/gaijinity/post-mortem-linkedin-talent-intelligence-experience-4o7c>

# 事后分析:领英人才智能体验

### 本项目总结介绍了 Craft CMS 3、GraphQL 和 Vue JS 的独特用途，旨在为互联智能体验创造互动体验

安德鲁·韦尔奇

[![Unique Project Linkedin Talent Insights](img/20ee8dd1a5b043e66f2cd381c5391d22.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yzMpXNNo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/unique-project-linkedin-talent-insights.jpg)

作为 LinkedIn 智能体验的一部分，我最近被指派做一个不寻常的项目。在纽约市，然后是 T2，然后是 T4【Syd ney】T5，现场活动突出了 LinkedIn 的新[智能英特尔](http://business.linkedin.com/talent-solutions/blog/product-updates/2017/what-is-talent-intelligence)平台，tar 通过这个平台获得了企业客户。

<aside>Enjoy short talks from glob­al lead­ers, inter­ac­tive ses­sions and net­work­ing oppor­tu­ni­ties. Immerse your­self in an inter­ac­tive expe­ri­ence, infused with insights focused on recruit­ment agencies.</aside>

这一系列的活动由第九单元牵头，由 T2 制作，恩科姆通过了一个巨大的拍摄项目，包括多个房间，有互动视频、战略场景、演讲、公鸡尾巴等等。我在这里只关注我参与的这个项目的一小部分，因为我认为这是一个有趣的利用科技来实现互动体验的项目。

我在《丹·德·莱昂+布尔·多克》的尼尔·汤普森和《T2》的乔纳森·梅尔维尔的指导下工作。io 进行设计并提供模板编码。我的任务是规划、架构、开发和核心应用程序设计，所以这是我在这里要关注的。

我希望你能从这次剖析中学到的是，开始思考如何以非传统的方式利用网络技术和你的技能。

## 问题

客户希望获得一种体验，在这种体验中，人们可以与安装在大型投影墙前基座上的多个平板电脑上的应用进行互动。接近平板电脑的人得到一家希望搬迁到另一个城市的公司的信息，他们有责任选择搬迁地点。

[![Linkedin Ti Unit9 Nyc 201806 00229](img/622872fc39af4ad446db0ca5252fd7f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9lhJzouF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/LinkedIn-TI-Unit9-NYC-201806-00229.jpg)

他们会收到许多关于各城市空房情况的问题。然后，他们会被鼓励去探索现有的多项选择答案，并从他们面前的一面大投影墙上看到他们的选择所展示的见解。

然后，在通过与应用程序交互获得知识后，他们选择在哪里重新发布该公司。任何事情都没有正确或错误的答案。重点是能够看到 Linked In 的智能智能可以为他们提供的洞察力类型。

除此之外，该应用程序需要能够:

*   使用任意数量的平板电脑/投影屏幕对(“工作站”)
*   与活动将要到达的任意数量的城市合作
*   汉德尔对每个城市都有不同的看法、不同的问题和不同的见解
*   当在平板电脑上选择答案时，会动态改变投影屏幕上显示的“洞察力”
*   有一种方法允许客户和我们的团队积极地编辑问题、见解等。在后端
*   将每个唯一用户的回答记录在数据库中作为条目
*   向他们展示最终的“洞察力”屏幕，显示他们的答案与该城市活动中其他人的答案相比如何
*   在末尾写上他们的名字和电子邮件地址，这样 LinkedIn 就可以跟进了
*   导出所有捕获的数据，以便 LinkedIn 数据团队可以对其进行分析

此外，我们希望 ed 设计它，以便如果客户最终想把它变成一个网站，它不会是一个完整的重写。

## 结果

在我们深入研究如何着手这个项目之前，让我们先看看最终的结果。然后我们就能解开我们是如何从这里到那里的。

[![Linkedin Ti Unit9 Nyc 201806 00435](img/8b3c51e250384d7a1a2178bd149b4cee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dnYzkNpL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/LinkedIn-TI-Unit9-NYC-201806-00435.jpg)

[![Linkedin Ti Unit9 Nyc 201806 00003](img/fcda65d8a42ed1b20cce422e6ffc2a24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zi0UbtYT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/LinkedIn-TI-Unit9-NYC-201806-00003.jpg)

[![Linkedin Ti Unit9 Nyc 201806 00351](img/d036e4f6f8c7f125f17bbfb5e4be24a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Le1fcx3Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/LinkedIn-TI_Unit9-NYC-201806-00351.jpg)

[![Linkedin Ti Unit9 Nyc 201806 00280](img/a00fbe5ae59fd627d5d14143793d8001.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TrKpGElt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/LinkedIn-TI-Unit9-NYC-201806-00280.jpg)

这一切都以一种与品牌和活动主题相融合的方式融合在一起。如果你想看它的动态，这里有一个 YouTube 视频。

## 解

我们没有太多的时间来把它们整合在一起…而且瞄准镜一直在变化。

因为所有的东西都必须快速地组合在一起，而且为了适应不断变化的规格，我们选择将一些现有的技术组合在一起，以便快速起步。

*   为后端设计 CMS 3 ,因为我们对它了如指掌，我们需要一种方式让不同的内容作者一起工作。我们还需要一个记录和导出数据的地方，所以这是再合适不过的了。
*   为网页设计风格，因为随着项目的发展，它允许我们快速输入和输入
*   做这个应用程序“胆量”,因为它又一次让我们能够快速地输入一些东西，而这种反应能力对于我们正在做的应用程序来说只是一种本能
*   [GraphQL](http://graphql.org/) 通过 [CraftQL plu g in](http://github.com/markhuot/craftql) 从 Mark Huot 处理 Craft CMS 的读/写数据，因为它非常容易使用，而且数据以一种非常自然的方式打包成 VueJS

我们已经讨论了[开发模式中的每一项技术。豆荚演员，所以也许我们选择他们并不意外。但有趣的是，这些“网络”技术在一个实时互动应用中配合得如此之好。](http://devmode.fm/)

## 硬器皿

为了使这一切顺利进行，我们需要硬件来运行它。这是大概的直径图:

[![Linkedin Hardware](img/734245a1f4a029fcaf6730b426a90aad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nTT14kZ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x811_crop_center-center_100_line/LinkedIn-hardware.png)

我们选择了通过 [Forge](http://forge.laravel.com/) 提供的云服务器来托管 Craft CMS，这样整个团队就可以从不同的位置进行协作。

然而，对于任何现场活动来说，依靠一个足够稳定的互联网连接来处理工作，甚至工作，并不是一个好主意。事实上，我们在纽约事件的第二天就失去了互联网接入。

我们选择使用一个小型的 [Meerkat](http://system76.com/desktops/meerkat) 服务器，它运行在我们的云服务器上的 Ubun tu 16.04 Lin ux 上。然后，它使用 Craft CMS 文章中的[数据库&环境间的资产同步中描述的技术，从云服务器上同步数据。](https://dev.to/gaijinity/database-asset-syncing-between-environments-in-craft-cms-3fca-temp-slug-480513)

然后，我们让 dan de lion + bur dock 的巫师们把平板电脑和猫鼬接到当地的私人网络上，然后我们就出发了。

Sur face Pro 平板电脑被配置为在 [Chrome Kiosk Mode](http://dandelion-burdock.com/articles/chrome-kiosk-mode) 文章中所讨论的那样，因此它们可以像谷歌 Chrome 一样在阿宁现代浏览器上运行，但不能被任何用户篡改。

## 软件

在软件方面，uni verse 的中心是 Craft CMS 3。这就是要显示的数据的来源，也是存储来自用户的任何结果答案的地方:

[![Linkedin Software](img/f7f7b7dd0319f07af82b995ab7a943bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N44x8pHx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x941_crop_center-center_100_line/LinkedIn-software.png)

我们为`tablet`和`display`(投影墙)准备了两个单独的模板，每个模板都有 HTML/ VueJS 代码。这样，我们可以固定平板电脑来加载`/tablet`并使用[触摸设计 er](http://www.derivative.ca/) ，让它加载`/display`的网络视图，以将其发布在直播视频的顶部。

因为我们需要能够处理多个城市，以及每个城市的多个站点，所以我们传递了`stationSlug` URL 参数来指示网页应该加载哪个站点。例如:`/tablet?stationSlug=new-york-green`将加载纽约绿色车站的平板电脑视图。

然后，cus tom Vue JS 应用程序将通过与 giv en `stationSlug`对应的工作站通道中的 GraphQL/ CraftQL 加载适当的 Craft CMS 3 条目。

在 Craft CMS 的后端，条目看起来像这样:

[![Linkedin Station Craft Cms Entry](img/7ff73b3c4eb62b2fa7fbaa522b32481a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OdxItXWP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x1352_crop_center-center_100_line/LinkedIn-station-craft-cms-entry.png)

在这里，内容作者可以选择要问的第一个问题，设置背景图像，选择要播放的声音作为线索(通过 [howler.js](http://howlerjs.com/) )等等。

最初，该系统被设计成一种“选择你自己的观点”的书籍式问题，其中一个问题的答案可能导致另一个问题。这就是为什么 Sta tions 条目只设置了第*个第*个问题。

数据库模式非常简单，大致如下所示:

[![Linkedin Db Schema](img/3de0ec866234c98578af362384fa2129.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6XcIcp1F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x747_crop_center-center_100_line/LinkedIn-db-schema.png)

…每个答案都可以链接到下一个问题(如果有的话)。

除了为内容作者提供定制内容的方法之外，这个 Sta tions 条目还保存了应用程序的个人内容状态。图形输入板允许人们通过改变图形输入来改变状态，投影墙通过图形查询来查询状态。我更愿意做 GraphQL 的 Sub scrip tions，但是这在 CraftQL 中还不存在。

想添加一个附加站吗？没问题，只需添加一个新条目。想增加一个新城市？也没问题，只需更改工作站链接的目录。

这最终被证明是一个很好的方法，因为随着项目的进展，计划使用的工作站的数量会发生几次变化。根据活动空间的不同，每个城市也可能有不同数量的展位。

<aside>Nev­er solve a prob­lem for 2 things; always solve it for N things</aside>

这允许很大的灵活性(可能比最终需要的灵活性多一点)。问题在一个单独的比率通道中，在中的[超级表格 plu g 用于为添加任意数量的答案提供一个漂亮的 UX:](http://github.com/verbb/super-table)

[![Linkedin Questions And Answers](img/200cceaccc9a554cf85f7901ea86b2fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VktRaaTH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x1386_crop_center-center_100_line/LinkedIn-questions-and-answers.png)

对于数据导出，我们使用了 Fred Carlsen 的[Beam plug g in](http://superbig.co/plugins/beam)和一个定制模板，以便在每个城市的基础上轻松导出到 CSV 文件。这使他们能够访问每个独特的用户会话，包括他们选择的所有答案，以及所有的销售线索信息。全部包裹在一个整洁的小 CSV 弓里。

## 给我看看 App！

cus tom Vue JS 应用程序本身主要负责跟踪状态，并对用户的各种查询和输入做出响应。下面是一个示例，展示了如何使用`stationsQuery` GraphQL 查询来检索给定工作站的当前状态:

[![Linkedin Stations Query](img/215c985bd2cce66a7fd1c3db214b79f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xSTJFlDS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x745_crop_center-center_100_line/LinkedIn-stations-query.png)

Craft QL plu in 让你在 Craft CMS 3 中读取(查询)和写入(修改)数据是多么容易，这真是令人惊叹。我们最终使用了 [GraphQL-Request](http://github.com/prismagraphql/graphql-request) 来简化 GraphQL 请求。我们所要做的就是设置一个带有 JSON Web 令牌的客户端(JWT):

```
 const client = new GraphQLClient('/api', {
    headers: {
        Authorization: 'Bearer XXXXXXXXXXXXXXXXXXXXXXXXXXXXX',
    },
}); 
```

然后我们可以像这样声明一个简单的 GraphQL 查询:

```
 const settingsQuery = `
    {
      globals {
        settings {
          recordAnswers
        }
      }
    }
    `; 
```

在这种情况下，所有的查询都是询问设置全局中`recordAnswers`灯开关的状态。然后我们可以这样执行它:

```
 // Load in our global settings
            loadSettings: function() {
                client.request(settingsQuery)
                    .then(data => {
                        this.recordAnswers = data.globals.settings.recordAnswers;
                        console.log(data);
                    })
                    .catch(err => {
                        console.log(err);
                        console.log(err.response.errors);
                        console.log(err.response.data);
                    });
            }, 
```

要做一些事情，比如在一个会话结束时写出受限的 lead 信息，我们可以简单地进行如下的图形变换:

```
 const writeLeadMutation = `
    mutation writeLead($firstName: String!, $lastName: String!, $email: String!, $cityIdentifier: String!, $stationIdentifier: String!, $userIdentifier: String!)
    {
        upsertLeads(
            authorId: 1
            title: "lead"
            firstName: $firstName
            lastName: $lastName
            email: $email
            cityIdentifier: $cityIdentifier
            stationIdentifier: $stationIdentifier
            userIdentifier: $userIdentifier
        ) {
        id
        }
    }
    `; 
```

在这种情况下，Craft CMS 中的通道是 Leads，而`upsert`是 CraftQL 的“写”的命名结构。然后是每个键/值对，如`title`、`firstName`等。所有这些都对应于我们希望保存数据的销售线索通道中的字段。

由于 Vue JS 的反应性，一旦我们改变了应用程序中的属性，这些改变会立即反映给用户。这为使用平板电脑的人带来了非常好的互动体验。

我们也很好地利用了一些小细节，比如电脑、手表、翻译等等……但那是以后的事了。

## 把 ping 包起来！

cus tom Vue JS 应用程序只有 864 行代码，所以没什么复杂的。但是要让所有异步运行的各种部件在 har mo ny 工作还需要一点时间。

在实际的模板、CSS 等方面，整个项目也做了相当多的工作。但是很大一部分工作是简单地找出我们需要的各种各样的东西(包括硬件和软件)，然后把它们组合在一起。

我只是一个大制作中很小的一部分，但我在尝试将网络技术应用到现场互动活动中获得了很多乐趣。希望这能让你考虑一些网络技术的非传统应用！

如果你第一次错过了，看看 YouTube 上展示这个项目的视频！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计