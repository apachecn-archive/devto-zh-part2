# 建立我自己的网站

> 原文：<https://dev.to/thermatix/building-my-own-website-5af0>

# 介绍

据我所知，大多数开发者都有自己的网站，但对我来说，遗憾的是我还没有这样做，所以我决定是时候开始一个由来已久的传统，建立自己的个人网站了！

但我想我会通过故意过度设计让它变得有趣。

我的理由有几个:

*   我可以将我在系统架构方面积累的知识付诸实践
*   我可以用我一直想尝试的有趣技术来建造
*   我可以学习如何使用新事物
*   好玩！

所以，我决定计划一下我想在这里做什么，任何讨论或想法都是受欢迎的！

这个文档也可以作为一个有用链接的存储库，这些链接与我选择的内容相关。

## 清单

### 选择

*   基础建筑
    *   []t0【消息队列】:Kaka | rabbitq | AWS SQS
    *   [√] [消息格式](https://dev.to/thermatix/building-my-own-website-5af0#message_format):消息包
    *   [ ] [路由](https://dev.to/thermatix/building-my-own-website-5af0#routing) : Nuster|Nginx
    *   [√] [调配](https://dev.to/thermatix/building-my-own-website-5af0#deployment):码头工人&厨师？
    *   [√] [CI](https://dev.to/thermatix/building-my-own-website-5af0#ci) :特拉维斯

*   前端
    *   [√] [视图框架](https://dev.to/thermatix/building-my-own-website-5af0#view_framework) : Marko
    *   [状态 : Vuex
    *   [√] [视觉](https://dev.to/thermatix/building-my-own-website-5af0#visual):实体化

*   后端
    *   [√] [Ruby](https://dev.to/thermatix/building-my-own-website-5af0#ruby) :机架+定制中间件
    *   [√][Rust](https://dev.to/thermatix/building-my-own-website-5af0#rust):tokio-serde-msg pack
    *   [√] [API](https://dev.to/thermatix/building-my-own-website-5af0#api) : Roda

### 我可以添加的功能

*   [ ] [开发到后馈](https://dev.to/thermatix/building-my-own-website-5af0#dev_to_feed)

### 服务

*   前端(当我想到更多服务时，我会添加到这一部分)

    *   [ ]前端应用发行版
    *   [ ]路由/缓存(Nuster/Nginx)
    *   [ ]前端应用程序分发服务(可能是节点，但无论如何都是最好的)

*   前端面向后端(当我想到更多服务时，我会添加到这一点)

    *   API(我将在添加 API 时添加)

*   面向后端(当我想到更多服务时，我会添加到这一点)

    *   [ ]消息队列(也许是，也许不是)

### 有用的链接

*   码头工人
    *   [苹果电脑上的 Kubernetes](https://gist.github.com/kevin-smets/b91a34cea662d0c523968472a81788f7)
    *   [微服务和 Kubernetes 入门](https://hackernoon.com/getting-started-with-microservices-and-kubernetes-76354312b556)

# 这个计划

我的计划的想法是列出我想到的事情和我能为这些事情做些什么。我的想法是填写列表，其中一些我不确定。我会放两个或更多的项目，我会感谢一些输入！

我不知道我是否能抽出时间来完成这个项目，但至少如果我有一个计划，我就有了努力的方向。

最后，我可能会在计划中加入一些事情，但这也是为了好玩，所以如果一些事情变得令人讨厌或超出了好玩的范围，我可能不会去做。

## 基本架构:

### 主持

我已经决定使用 AWS，虽然它可能相当接近金属，但它将允许我了解本质

### 密码

我已经决定在后端使用 ruby 和 rust，因为我正在用微服务构建，我可以用我喜欢的任何语言构建小服务，或者先在 ruby 中构建，让一些东西运行，然后迁移到 rust，或者用 ruby 混合构建，用 FFI 构建 rust。

### 基础设施

我决定使用微服务，因为这一直是我想尝试做的事情，为此我需要一些基础设施。

如果我决定我不能用这个，我就用标准的 monolith，然后在旁边添加微服务，或者从 monolith 开始，然后迁移到微服务。

*   #### 消息队列我本来想和[卡夫卡](https://kafka.apache.org/)或者 [RabbitMQ](https://www.rabbitmq.com/) 一起去，但是考虑了一下，我决定和 [SQS](https://aws.amazon.com/sqs/) 一起去。尽管设置起来很有趣，但我真的不需要像 Kafka 那样吞吐量大的东西，也不需要像 RabbitMQ 和 SQS 那样笨重的东西，它们由 Rust、Ruby 和 Javascript 支持。

*   #### 消息格式

    我已经决定使用[消息包](https://msgpack.org/index.html)，这是我一直关注的东西，应该很有兴趣跨 Ruby、Rust 和 JavaScript(前端)实现和使用。
*   #### 按指定路线发送我决定试试 [Nuster](https://dev.to/nuster/nuster---a-web-caching-proxy-server-based-on-haproxy-40k2) ，因为它看起来很有趣，而且我以前从未用过。但是如果我不能让它如我所愿地工作，我会用 [Nginx](https://www.nginx.com/) ，因为我过去一直用它

*   #### 部署

    我已经决定使用 [Docker](https://www.docker.com/) ，因为我以前从未使用过它，所以实现起来会很有趣，但我不确定我是否需要像 Chef 这样的东西来进行部署，或者我是否应该将它们结合起来，因为我有点困惑我是否需要它们两者。
*   #### 海峡群岛

    我决定和[崔维斯](https://travis-ci.org/)一起去，还是因为我从未用过它，而且它是免费的。

我想除了使用 S3 作为资产和东西，Postgresql(或 amazon aura)作为我的数据库之外，基础设施也是如此

## 网站结构:

### 前端

*   #### 视图框架

    我已经决定选择[马尔科](https://markojs.com/)，因为马尔科的[语法](https://gist.github.com/patrick-steele-idem/cd291a2c21ba7ea2a7e12a06467249e5)非常好，而且非常有[表现力](https://hackernoon.com/server-side-rendering-shootout-with-marko-preact-rax-react-and-vue-25e1ae17800f)，老实说，马尔科看起来更有趣。
*   #### 状态

    [Vuex](https://vuex.vuejs.org/en/intro.html) ，没有竞争，只是看起来比 redux 更容易使用(老实说，就理解它而言，突变比 redux 更容易理解&上下文)
*   #### 视觉的

    [实体化](http://materializecss.com/)，老实说这是我的第二选择，我的第一选择是 fluent design，但我唯一能找到的是一个 [React 组件框架](https://www.react-uwp.com/)，它虽然好看(我会说性感)但当我想使用 Vue 或 Marko 时仍然是 React。实体化仍然很好，如果我想的话，我可以做一些丙烯酸效果来增加一些。不过，作为框架，我确实喜欢它，比 bootstrap 更喜欢。

### 后端

*   #### 红宝石

    [Rack](https://rack.github.io/) +定制 Rack 中间件:在大多数情况下，使用 Rack 来安装带有一些定制中间件的应用。因为我打算把它建成一个带有 API 后端的前端 JS 应用程序，如果有人知道如何分发基本的 HTML +资产而不需要像 Sinatra 这样的东西，那么请指出方向！
*   #### 锈

    T2【tokio-serde-msg pack】T4
*   #### 应用程序接口

    我已经决定使用[【洛达】](https://github.com/jeremyevans/roda) [用于](http://blog.davydovanton.com/2016/05/20/develop-api-with-roda/) [API](https://github.com/beno/roda-rest_api) [东西](http://gafur.me/2017/10/30/building-api-with-roda-and-sequel.html)因为它看起来很有趣，然后我将使用一个消息库将消息传递到消息队列，然后任何 MS 将拾取适当的消息，发送响应，然后 API 服务将响应。老实说，虽然我不确定，正如我所说的，我这样做是作为学习经验，因为我的知识目前几乎为零，一旦我有了更好的理解，我会改变这一部分。

## 特征

*   #### 开发到发布信息

    我宁愿只是发布 dev.to，并让我的帖子的链接出现在我的网站上，而不是建立一个博客。为此，我可以使用 dev.to articles api `dev.to/api/articles?username=thermatix`，有一个 RSS 提要，但我更愿意直接使用 api。

目前这是我所有的，我会添加更多的部分，因为我认为他们或决定他们应该被添加，一旦我的计划完成，我会创建一个列表，我要建立一个单独的文件，发布它，然后用任何适当的链接和信息更新它。

### 作者注释

[1]:建立一个开发网站可能不是一个由来已久的传统，但我认为它很合适。