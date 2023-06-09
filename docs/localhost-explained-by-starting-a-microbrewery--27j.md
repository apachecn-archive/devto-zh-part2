# Localhost 解释说，他创办了一个微型酿酒厂

> 原文：<https://dev.to/kbk0125/localhost-explained-by-starting-a-microbrewery--27j>

如果你能理解从在你的厨房里酿造啤酒到开始你自己的微酿酒厂的过程，那么你就能理解网络服务器的基础。

当我开始学习 web 开发时，我首先学习了如何在前端使用 HTML、CSS 和 JavaScript。之后，我就准备学习 node.js，编写我的第一个服务器端代码。不幸的是，我对 web 服务器如何与浏览器一起向访问者显示网站一无所知。

我不明白我是如何在笔记本电脑上运行服务器的。我的笔记本电脑是一台个人电脑。它怎么可能也是一个测试 web 服务器呢？因此，当我试图理解什么是 IP 地址、端口和 HTTP 请求时，我就笨手笨脚地往前走。

这是我用来帮助理解不同部分的一个类比。你用来测试你的第一个网络应用程序的本地主机，有点像你在家里酿造啤酒，测试不同的配方。从技术上来说，你可以酿造啤酒，装瓶，冷藏，然后从家里分发出去。但这并不意味着可以扩展到数千加仑的啤酒——这只是一个测试环境。

当你想让一切更上一层楼时，你需要在一个仓库里租一个空间，准备运送几千瓶啤酒。系统的每个部分都需要为此做好准备。你需要一条真正的装瓶线，一个酿造用的大缸，一个接单的人，一辆配送用的大卡车。这是相同的一般功能-酿造你的啤酒-但准备为成千上万的顾客。

[![](img/febe65dc4c04ed38740a2f602adee717.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DC9WK6k---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2Ao5lKCc1agNCw9q0O.)

### 了解本地主机

在你把你的网站投入生产之前，你需要能够在本地测试它，并确保所有的部分能够一起工作。这有点像在你家测试你的酿造设备。你可能只自己品尝啤酒，或者邀请几个朋友来品尝。你主要是想看最终产品味道好不好。在那之后，你可以想出如何扩大经营规模。因为如果你不能得到正确的配方，扩大规模是没有意义的！

首先，让我们了解一下在你家酿造啤酒的所有环节。

[![](img/a3d7b5a8982a46b8321487527b0ec018.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7kF2KZqv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AKaxH2fgLW_PxXQOF.)

*   你需要正确的配料和正确的组合来完成你的食谱
*   你需要购买适合你房子的设备
*   你需要一辆卡车来运所有的东西
*   你需要瓶子来把啤酒存放在冰箱里

同时，你喜欢酿造三种不同的啤酒

1.  一杯黑啤
2.  淡啤酒
3.  国际音标

每个食谱需要不同的设置和成分。例如，与黑啤酒相比，啤酒花啤酒需要不同的温度以及啤酒花和麦芽的混合物。这一点很重要。

当您使用 localhost 时，所有内容都在您的笔记本电脑或台式计算机上。客户端**请求**是本地的，决定**响应**的服务器端代码也是本地的。(如果您需要更新请求和响应，请点击此处查看我的指南。)

**我们来看一个场景:**你想尝试一种新的柚子 IPA 食谱。

客户端(您)提交一个**请求**来获得一个新的葡萄柚 IPA。

服务器端(也包括你)基于服务器端代码创建啤酒，
提交一个**响应**:几加仑的葡萄柚 IPA。是的，在这种情况下你是双方！

[![](img/a88759eb28b3f0ae91cec6ff1d2ccc14.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KsQ5o6ge--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AxjfOBSzv_QX7mFuf.)

我的意思是:您已经编写了客户端和服务器端代码，将 IPA 食谱转化为几加仑啤酒。而且，您可以完全控制原始请求。

我们从技术角度来看。

[![](img/9867106102e8f25aec70b6e93ba7b86f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QlxwSgnB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AW2zGft0SmY6EEAbh.)

虽然我让客户端作为浏览器出现，但它显然与服务器在同一台笔记本电脑或台式机上！因为你发出请求…并且你写了服务器端的代码…这一切都发生在同一台机器上。

这里有一个关于如何扩大规模的提示:IP 地址。 **IP 地址**是计算机的唯一标识符。但是，localhost 其实和 127.0.01 的 IP 地址一样，都是指回自己的电脑！因此，无论您使用哪台计算机，相同的 IP 地址总是表示您正在使用自己的计算机作为本地主机。当我们看下一节时，IP 地址将根据您的代码所在的位置而改变。

现在，我将假设您在学习如何设置您的第一个节点服务器时正在使用 Express.js。Express 使用
[app.listen()](http://expressjs.com/th/api.html) 语句启动节点服务器，如下所示:

*app . listen(3000)；*

但是 3000 不是上面的 IP 地址，那是什么呢？它实际上是
**港**。Web 服务器有 65535 个可能的端口，但以下是一些常见的端口:

所有这些都是 web 服务器可以处理的不同类型的请求。HTTP 是 80 端口常见的一种。例如，POP/IMAP 与电子邮件相关。DNS 和域名有关。无论如何，这有助于服务器快速了解传入的是什么类型的请求，因此它可以最有效地工作。

把**端口**想象成我们上面列出的特定类型的啤酒。您的服务器已经过预配置，可以处理不同的请求，就像我们知道我们需要为每种想要酿造的啤酒改变设备和程序一样。

因此，这里是图表的更新版本，以反映端口 3000 与我们的葡萄柚 IPA 配方一致。即使这是一个 HTTP 请求，我们也不想使用端口 80，因为我们不想干扰我们的浏览器在我们个人使用互联网时发出的请求。

因此，如果你在浏览器中输入 [http://localhost:3000](http://localhost:3000/) ，下面是你的电脑上发生的事情:

[![](img/3a81ec067677774ef26e1bc155cc5d2e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZXCZcqMh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AUljl3uwvosx5Gf-S.)

1.  Http://表示这是一个 Http 请求
2.  Localhost 指向 IP 地址 127.0.0.1，这意味着我们需要查看我们自己的计算机
3.  :3000 是端口，这意味着我们将在该端口寻找所需的服务器端代码。

当然，没有 65，000 多个预编程端口。我们只是使用 3000 作为一个任意的数字，以确保我们避免上面列出的常见端口。我们可以使用端口 3001 处理淡啤酒请求，使用端口 3002 处理浓啤酒请求。

### 将你的代码放在网络服务器上

假设您现在想要批量生产所有三种配方。你需要在仓库里租一个空间，购买一些专业设备，并准备好处理更多的需求。

[![](img/d45a6d20300bf288cb38e96015df6d39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zNbhj6BQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AGyvB2r_lWRbRiTtp.)

将会发生两大变化。

1.  服务器，或者说仓库，已经不在你家了！它位于一个可以通过互联网访问的远程位置。Heroku 是一个用来启动你的第一个网络应用的流行工具，[在世界各地都有服务器。](https://devcenter.heroku.com/articles/regions#view-the-list-of-available-regions)
2.  这些请求将来自全国各地，或者世界各地。这不再只是几个朋友在家里喝啤酒了！来自个人、餐馆和经销商的需求，你需要满足这些需求。

假设您有 100 名客户订购了 6 包柚子 IPA。这是它的样子。

[![](img/4cb3397ec879d816050b0b8f45835842.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iyiuiK2E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2ALjdZu2yaFR60FVHB.)

这就好比一个网站上有 100 个访问者提交一个**请求**，然后你的服务器给每个人发送一个**响应**。

[![](img/1bb72e6c76d9d362a16fcdf3f8a39824.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pxQQNQwX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AyEAuTFscPsboDmwO.)

假设你的域名是*mybrewery.com*。当这 100 个网站访问者在他们的浏览器中输入时，他们会连接到一个特定的服务器，该服务器的 IP 地址是**并托管该网站。然后，服务器向他们各自的浏览器发送适当的响应。

在本地主机版本中，我们有一对一的关系。一个浏览器请求一个服务器。现在，我们有一个多对一的关系。对一台服务器的许多客户端请求。

让我们继续到**端口**。 [Heroku 实际上抽象了这个](https://scotch.io/tutorials/how-to-deploy-a-node-js-app-to-heroku)，让你添加一个条件语句，这样他们就可以在自己的终端设置端口。

像这样:

*var port = process . env . port | | 3000；*

因此，我们不知道 Heroku 使用哪个港口，但我们知道它是一个港口。这意味着我们无法使用 Heroku 轻松配置多个端口。如果您使用具有更多可配置性的服务，您可以手动设置多个端口，但是 Heroku 会处理这些以简化过程。

### 租用服务器的一部分

使用远程服务器的最后一部分是可伸缩性。在过去(好吧，大概 10 年前)，你有几个昂贵的选择:

1.  您可以购买服务器并自行管理。
2.  您可以在一个远程位置一次性租用一整台服务器。

在我们的微酿酒厂的例子中，这有点像你刚刚起步时租用或购买一个大型仓库。你不知道整件事是否有必要。

幸运的是，您现在可以租用服务器内的容量！服务器运行允许它们被分区的软件，并在**容器**中运行应用程序。这有点像租用仓库的一部分来启动你的酿酒厂。

[![](img/1d52df8f7fc22da1b7f46f8c70976f92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tqi8RqcS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2ACymSv1WLo_SuL8Ni.)

Heroku 称他们的容器为“ [dynos](https://www.heroku.com/dynos) ”。比如说，你的微酿啤酒只有 100 份订单，就像上面的例子一样。你只需要租仓库的一小部分。但是，如果需求突然暴涨，你现在需要每周生产 1000 份订单，你就需要扩大经营规模。您可以很容易地在服务器中租用更多的**容器**来满足这种需求。而且，由于它是虚拟空间，您可以快速扩展，这与租用物理空间不同。因此，您不需要去租一台全新的服务器并进行配置。

### 对更多可视化教程感兴趣？

如果你喜欢这个教程，可以查看主要的[代码类比网站](http://codeanalogies.com/)或者注册下面的我的时事通讯，获取更多关于基本 web 开发主题的
教程。