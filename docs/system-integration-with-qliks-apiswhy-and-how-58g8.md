# 与 Qlik API 的系统集成—为什么和如何

> 原文：<https://dev.to/qlikbranch/system-integration-with-qliks-apiswhy-and-how-58g8>

> 首先，本文不是“如何做”的教程，而是一份白皮书。它不是为技术用户设计的。如果你了解 API 的价值以及如何使用 Qlik 的 API，这不适合你。

**_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _***Qlik Pro 的停在这里***_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _**

“Qlik 的 API 是什么？”🤔

“为什么有人想要使用 API？”🤔

"使用 Qlik 的 API 你能构建什么？"🤔

> 如果你有这些问题，你可能会发现这篇文章很有帮助。这篇博客是关于使用 Qlik Sense APIs 的各种类型的系统集成。

### 所以让我们一头扎进去。有了 API，你可以做令人惊奇的事情。但是等等，什么是 API？

[![Alt text of image](../Images/256747a8ba675af03eabc4703a2ae08e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k56IR9Dv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AFZ3oq1iOJfMl0_x8VFgPIw.png)

[![Alt text of image](../Images/32a452a2dbf35560a62ac186b0ded16d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6FenLcci--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A9djymVQcqlaN0lWsDtbewQ.png)

[![Alt text of image](../Images/a05f98a5093aba4a22d6a0a58d883482.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qy6Sg-Y3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A6UNxUlrS_taO6h0uZa36pA.png)

> 在每次点击的背后，后端会触发各种事件。

[![Alt text of image](../Images/95c953455669f15096f10828d56e17b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zC5uYB_4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AVghBhnFTIHy1fqofKBgjFQ.png)

> API 是前端和后端之间的桥梁。

[![Alt text of image](../Images/33beb167070d0d3b2f94ade69bf83d7f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C66LugQZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A1fcG3qHoAzWrgXwXuNzyng.png)

> 通常，用户交互仅限于前端元素:按钮、图表、过滤器等。

[![Alt text of image](../Images/7460679b0edcfcd6cb43074e08160b47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S6ice_Ur--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AFokNr-uBKh5HwBn2YPOj7w.png)

> 因此，当一个软件实现 API 时，这意味着与后端的交互不再局限于软件的原生前端，这打开了许多可能性。

[![Alt text of image](../Images/be6c25ec598bf199a2611cb0f8ca4636.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KCk_NHzG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AciZOzGjf4T5JSVYsk3EIKw.png)

### 现在你知道什么是 API 了…你能用 Qlik Sense APIs 构建什么？

API 一直是 Qlik 重要而独特的一部分。它们是混搭和扩展的构建块。如果你对那些不了解，下面简单解释一下:
[![Alt text of image](../Images/e85f17b10623cc0c62685782502ef7a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l4Tgwk5W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AGlU82u9waWIu6v9WeqE1Jw.png)

但是因为这篇文章是关于系统集成的，所以我们将把重点放在混搭上。

> 这里有一个随机的事实:您可以使用扩展构建混搭。这样，从技术上讲，您可以将放入 Qlik 系统的可视化内容放入您自己的系统中。

#### Mashup Lv.1 —构建 iframes 的网页

这可能是最简单的系统集成。Qlik Sense 中的每个图表或表格都有一个 ID 和一个 URL，可以在 [Qlik Sense DevHub](https://help.qlik.com/en-US/sense-developer/June2018/Subsystems/Dev-Hub/Content/dev-hub-at-a-glance.htm) - >单一配置器门户中找到。
T3![Alt text of image](../Images/825324d675d7f55b359fda552abba566.png)T5】

如果您将 URL 复制到浏览器中，您可以在页面上看到该对象。因此，从 DevHub 中提取的一行代码可以添加到现有的网站中，以将 Qlik 图表嵌入到网页中。将多个 iframes 放到一个 html 文件中，你就有了一个仪表板。

> 所以在不到 1 分钟的时间里，你就可以用从 DevHub 复制来的四行代码构建一个网页。👍

[![Alt text of image](../Images/3de733fdffc2bca217513c53b8d06e30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ODbZqKMR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AysdWbTLCoQ6Jl8j5jsaw-Q.png)

要求技能等级:★☆☆☆

#### Mashup Lv.2 —带有 API 和库的 Mashup

<figure>

[![Alt text of image](../Images/553c082e1296bd53ae0a2d5e25b3a205.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8vZ0dero--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AqCi-Lhi5uO77ztGtz66FaA.png)

<figcaption>See how it is implemented in the [Walkers Dashboard](http://playground.qlik.com/liveshowcase/playground-zombie/main.html)</figcaption>

</figure>

步行者仪表盘是由 [Francis Kabinoff](https://github.com/fkabinoff) 创建的 Qlik Playground 上的一个例子，它使用网页中的 Qlik 图表显示电视节目《行尸走肉》的统计数据。这是一个用 Qlik Sense [功能 API](https://help.qlik.com/en-US/sense-developer/1.0/Subsystems/Workbench/Content/BuildingWebsites/API/qswb_mup_apireference.htm)构建的典型混搭。与 iframe 不同，图表是使用 Qlik javascript 库来控制用户交互和对象检索的。这意味着更快和更干净的执行，可定制的元素和其他功能。

也有一种简单的方法来创建和托管自己的混搭。在 Qlik Sense DevHub 中，你可以找到一个名为 [Mashup Editor](https://help.qlik.com/en-US/sense-developer/June2018/Subsystems/Dev-Hub/Content/MashupEditor/mashup-editor-overview.htm) 的工具。它允许用户通过将 Qlik 对象拖放到预定义的模板上来设计自己的 web 应用程序。但如果你想构建一些不同的东西，比如 Walker Dashboard，你需要学习 javascript 和 html。

要求技能等级:★★☆☆

#### Mashup Lv.3 —构建一个看起来与 Qlik 完全无关的 Qlik 仪表盘

<figure>

[![Alt text of image](../Images/8364ea98cf924fdfde1958d5ddc6976a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yU5S42NW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AWS5K5tnKzIMINrVPi0MOaA.png)

<figcaption>See how it is implemented in the[Maple Leafs Dashboard](http://playground.qlik.com/liveshowcase/maple-leafs/index.htm)</figcaption>

</figure>

Qlik Sense 是商业智能软件，但不仅仅是商业智能软件。通过使用 API，用户可以在 Qlik 应用程序上创建自己的可视化效果，而无需使用 Qlik 中的图表。在这个用例中，Qlik 不再是一个可视化服务器。它是仪表板后面的数据仓库加数据引擎(Qlik 的关联引擎)。它处理数据处理、存储和关联部分，但让第三方技术处理表示部分。

> 如果你这样做了，你就有资格称自己为 Qlik Pro。😁Qlik Sense DevHub Mashup 编辑器在这个层面上不会帮到你。前端的每一行代码都要自己写。

要求技能等级:★★★☆☆

#### Mashup Lv.4 —使用 enigma.js 和引擎 API

大多数 Qlik 开发者不使用 [enigma.js](https://help.qlik.com/en-US/sense-developer/April2018/Subsystems/APIs/Content/enigmajs/enigmajs-introduction.htm) 和[引擎 API](https://help.qlik.com/en-US/sense-developer/April2018/Subsystems/EngineAPI/Content/introducing-engine-API.htm) 。对于许多人来说，能力 API 对于大多数用例来说已经足够了。

Enigma.js 是位于引擎 API 之上的包装库。虽然功能 API 是从用户的浏览器上运行的，但是 enigma.js 可以在应用服务器上运行，以便在向用户提供网页之前与 Qlik 进行交互。事实上，能力 API 还在使用 require.js 打包的 Angular 应用程序中实现了 enigma.js。

[![Alt text of image](../Images/93cab9b63cd4206bc9f0f1296a801838.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9uUeYied--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2A0wY-WYnmMD-PsxSL) 
引擎 API 是 Qlik 感知服务器和 Qlik 感知客户端之间的通信协议。[引擎 API Explorer](https://help.qlik.com/en-US/sense-developer/June2018/Subsystems/Dev-Hub/Content/EngineApiExplorer/engine-api-explorer.htm) 是 DevHub 中的一个工具，在这里你可以学习前端如何与后端对话。除非您正在构建自己的支持库，否则 Enigma.js 应该可以满足您的大部分集成需求。

要求技能等级:★★★☆

#### 混搭(？)吕。MAX —利用引擎本身

当您对技术了如指掌时，您可能希望在商业智能领域之外使用数据引擎(也称为 Qlik 的关联引擎)。例子是不言自明的，看看吧...

<figure>

[![Alt text of image](../Images/d0edf10e597269315e86101bdd5d117e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i6X5P-VF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A0013QeegwbHZeZpkgE4Yig.png)

<figcaption>See how it is implemented in the[Boston Building Emissions By Axis Group](http://opensrc.axisgroup.com/emissions/)</figcaption>

</figure>

<figure>

[![Alt text of image](../Images/2926947ae79a8f0dd254ad1df39618c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e_IFUg3i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AiT_tkVFOP7IDletZwXThcQ.png)

<figcaption>[C40 cities building energy by Slalom](http://playground.qlik.com/liveshowcase/hack-challenge-slalom/index.html)</figcaption>

</figure>

<figure>

[![Alt text of image](../Images/1269205885e840e8c4c3d7e2ad2cc03a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--64g9N43V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ah7zXqGZFq4YWnrY1-rcoQw.png)

<figcaption>[Qlik Core](https://www.qlik.com/us/products/qlik-core)</figcaption>

</figure>

还有一些用例很难定义——它们肯定不是 mashups，其中一些甚至不是 web 应用程序。

<figure>

**例如，一个商业智能聊天机器人……**
[![Alt text of image](../Images/6a205c45bdaedc49f93f62965aaeed96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o4Zb6BoC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ANdQmvB4llgNaC6YDa8NGFA.png)

<figcaption>[Qlik BotNet](http://branch.qlik.com/#!/project/5a2fb8474666f1a641bc8e76)</figcaption>

</figure>

**和镜子……**
液体错误:内部

<figure>

**很多人不知道——在[branch.qlik.com](http://branch.qlik.com/)背后有一个 Qlik 应用程序。**该应用专门处理搜索和内容管理。Qlik Branch 是一个开源项目，你可以在这里找到代码。
[![Alt text of image](../Images/1f05f78dc8949cbc8c472d42d9968a48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--clcIBTjW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AglZM5DXXaqIZU6QZinSt7A.png)

<figcaption>[Qlik Branch](http://branch.qlik.com/#!/)</figcaption>

</figure>

所需技能等级:★★★★

### 那么，应该如何入门呢？

开始尝试吧！在 [Qlik Playground](http://playground.qlik.com/) 上有资源和示例，在 [Qlik Branch](http://branch.qlik.com/#!/) 上有开源示例，在我们 [Qlik Branch Slack](http://bit.ly/BranchSlack) 上有大量社区成员。你还可以几乎毫不费力地在网上找到关于 Qlik API 的博客和 Youtube 视频。让我知道你的想法。
快乐编码。向所有出色的开发人员大声疾呼。
干杯。
—吴