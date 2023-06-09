# Javascript/React 是你的画笔、镜头、舞台和车床

> 原文：<https://dev.to/davidrenne/javascriptreact-are-your-paint-brush-lens-stage-and-lathe-1427>

15 年来，我一直在一起研究和编写 javascript。我已经看到它成长为现在这样一个巨大的生态系统，不管你喜不喜欢，你作为一名 web 开发人员的工作(尤其是如果你自称为“fullstack”)和你的生活状态都极大地依赖于它。用户和商业人士现在希望您的应用程序的前端表现得像最好的 web 应用程序一样。因此，尽管 javascript 有时令人沮丧，但它是唯一一种可以将你的空白画布变成杰作的跨平台语言。

在过去的 15 年里，我见证并维护了至少 50 个或更多的 web 应用程序，从所有这些经历中，我知道 javascript 堆栈和架构是应用程序成功和可用性的最重要的方面。我们都希望仅仅选择一个惊人的库/框架/设计模式就能让我们正在编写的应用程序变得惊人。但显然这不是真的。它需要几个不同的库和你的设计，如何构建前端页面，以沟通到你的后端一致和有效。一旦为您的应用程序建立了这个基础，大多数开发人员会在构建下一个组件时复制并粘贴最好的代码片段或页面。我想我在这里的观点是，如果你的前端只是 jQuery 和一些像 2008 年那样的 HTML 模板，并且是“狂野西部”，那么是时候用 React、npm、babel/es6/webpack 以及可能是我最喜欢的 [material ui](http://www.material-ui.com/#/) 组件来更新你的应用了。

我的 javascript 之旅就是从这个“document.getElementById”开始的。早在 2002 年，这是你能写的最强大的一行代码，因为它给我带来了使用 DHTML 的可能性。动态 HTML 允许任何 DOM 元素的一个 javascript 事件操纵我的网页的另一部分，这很可能是作为原始文本/html 下载的。那时候没有足够流行的框架(或者也许我是个菜鸟…谁知道呢)。但是我完全着迷于我可以操纵 CSS 或者在我的网页上添加元素，它们会神奇地出现在浏览器中。那时候我仍然讨厌并且不理解很多 javascript。

然后在 2006-2010 年？jquery 风靡了大多数应用程序。由于 jQuery 的优势，看起来$。post 和 AJAX 是你构建前端的方式。现在应用程序可以用 JSON 从你的后端获取更多的数据了！耶，我们赢了！但是使用 jQuery 几年后，您意识到您的代码看起来像 garabage，很难维护。

现在对我来说，从 2010 年到 2015 年，从 javascript 的角度来看，我有一个新技术的平静期。我花了 8 年时间从编写 PHP 转到学习 Ruby on Rails，然后是 Python，现在是 Golang。在我的舒适区之外学习一门新的后端语言是一次大开眼界的经历。现在我对“多线程地鼠”和后端可能需要的所有互斥锁感到非常舒服。我不认为我能再回到像 php 或 python 这样松散的鸭子式语言。从那时起，我能记得一般开发同事的一些角度用法，但我从未真正接触过角度/余烬/击倒/骨干。我想我从来没有真正喜欢过双向编码的方式或者那个时候更大的 javascript 框架。

最后，在 2015 年，我在 uptime.com 工作时认识了 React。Barak 是我的同事，他实现了所有最初的 react 架构，他和我一起参与了这个新库的一些项目，我的生活将从此不同。我记得我仍然试图理解生命周期功能和 render 是如何真正工作的，但这是学习它的一个惊人的一年。现在，我不认为我会使用或编写另一个没有 react 的应用程序。

到了 2016 年，我开始为 [Atlona](https://atlona.com) 工作。我和我的兄弟([丹尼尔·雷恩](https://github.com/DanielRenne))开始了一次机会之旅，这一旅程导致了我们第一个主要项目的发布，我们与[合作发布了速度控制系统](https://atlona.com/velocity-av-control-systems/)！我从第一天起就坚持使用 react 来设置和编写整个应用程序。我设置了 webpack 依赖项和构建过程，并幸运地选择了 react UI 组件和 CSS 基础的良好开端(主要使用来自 google 的 [react material UI 组件](http://www.material-ui.com/#/))。我们团队中有几个开发人员从未使用过 react，甚至没有使用过 ES6(我也没有真正深入了解 React ),所以我们一起学到了很多东西。我明确选择不使用 FLUX、react router 或 redux，因为我不想让我们整个团队的学习曲线变得过于复杂(尤其是 redux reducers，我仍然不喜欢它，因为它与我习惯的完全不同)。只是用 react 启动和学习状态/道具/组件就可以了。我们昨天刚刚推出了我们的通用产品，我们组装的前端看起来非常棒、时尚和直观。这是我迄今为止最好的作品。这里是这个系统的一个小玩笑。

我和我的兄弟 Dan 构建了我们自己的 golang 路由器和 react 库，以便能够快速高效地编写页面代码。我们从后端 post/get/puts 下推 JSON 有效负载，我们从 javascript 改变这些对象，我们将相同的有效负载推回到服务器进行验证/处理/保存。我们最初认为这种方法就像拖运戴尔电脑的 UPS 卡车。如果你正在修理一台电脑，而且运费很低，你不会仅仅因为零件和部件增加了卡车的重量就把它们排除在外。我们推测，您甚至可能想要发送显示器，以防虚拟用户遇到的问题是键盘、显示器或外部组件需要维修。在这个理论世界中，您不需要在发货前打电话给戴尔来解决具体问题，在这个世界中，您只需发送整个办公室，因为它是免费和便宜的(在您的内部网中，没有来自 ISP 或托管公司的带宽成本)。想象一下如果物流(网络)便宜、快捷且近乎免费的可能性。

我们过去开发的许多应用程序开发人员会构造许多对 page JSON 的请求，并且只发送与开发人员正在处理的 ajax 请求相关的信息。我认为这有时会导致一些令人讨厌的前端和后端代码，因为在为页面获取的数据和推回的数据之间没有一致性，所以我们为每个页面使用 golang 中的 viewModel 结构，该结构使用 get 或 post 控制器在您认为页面需要的内容上构造和加载 JSON。从前端获取 JSON 后，它会将对象加载到一个窗口变量中来保存页面状态。我们的路由器还在一个窗口变量中提供了应用程序的通用状态，您可以在其中保存通用的应用程序状态。这两个状态变量与从后端获取的两个英语翻译变量一起运行每个页面的可见英语翻译内容和应用程序数据。

一些 python 脚本或 php 脚本将一些随机键值对不一致地返回到前端(无论 joe developer 那天想做什么)的日子已经一去不复返了，因为 golang 结构总是有一个预定义的 JSON 结构，您可以一直依赖它。如果有一行数据，那么它肯定会将对象定义中的所有键都用空值/数据填充。无论如何，我们有一个很好的经验来建立从前端到后端的桥梁，我们的应用程序非常容易维护和调整。我的观点是，不要仅仅依赖其他库来处理从前端到后端的所有通信，如果您想出自己的系统来构建整个应用程序的页面，您可能会非常喜欢。有一个易于编码和与新的或现有的页面接口的可维护的解决方案是很好的。我们的 UPS 卡车总是将整个计算机和显示器运送到目的地戴尔。有时这是一个非常繁重的请求，根据具体情况可能会对性能造成负担，但另一方面，所有或大部分数据都在前端由您处理。但是对于在局域网上运行的应用程序来说，当您在千兆位交换机上时，让管道在每个请求上稍微拥挤一些是完全可以接受的。我们很喜欢它，只有几次我们发现 posts 或 get 上的有效负载太大，从而影响了前端的性能。

我最初写这篇博客的目的是谈论一个关于 javascript 如何像画笔的类比，而你的后端就像一种画布，有许多形状、大小和各种材料。如果没有一块好的画布，没有你在“工作室”或“画廊”展示你的作品所需要的形状，油漆本身就什么都不是。颜料的颜色显然是 CSS，但 javascript 是将一切结合在一起的粘合剂。它是摄影中照相机的镜头，戏剧舞台等。基本上，你可以以 javascript 为中心做一些类比。您可以塑造 javascript 来处理任何输入到您的编写精美且高效的后端，但是您也必须保持您的 javascript 和页面非常高性能。对于任何“纯后端工程师”来说，我的观点是，你需要找到更多的灵感和更多的安慰，因为对于大多数从事 web 工作的工程师来说，javascript 实际上是把食物带回家的。越来越多的客户(我可以推测)雇佣全栈工程师，因为他们知道他们可以构建系统的两个重要部分。当然，你可以只做后端工作，只做数据库工作，但如果我在办公室遇到你，我肯定会和你谈谈走出你的舒适区，走出你的左脑，开始用你的右脑感受和创造一些惊人的东西。如果你是一个右脑思考型的数学工作者，并且对后端数据的前端设计不太适应，不要担心，因为 React/MaterialUI/ES6 是开始探索应用程序的流畅用户体验的好方法。我完全见证了我的兄弟是一个惊人的后端工程师，写了一些绝对恶心的前端东西，现在他看到了光明。当他开始的时候，他因为所有正确的理由讨厌前端，事实上 Javascript 语言很烂，所有的前端都是垃圾。但现在他也变了个人，感谢反应。前端是赢家，因为终端用户、你的老板和其他所有的商业人士都不关心或不理解后端是做什么的，或者它是如何运作的，或者构建一个好的后端需要什么。他们只看到前端的数据问题，以及画布上显示应用程序设置好坏的画面。最终，前端会使您的数据发生最大的变化，并且可能不可避免地会产生最多的数据问题。每个开发人员都应该每天思考如何编写更好更高质量的 javascript，这样你的后端会感觉更稳定。

你的后端有多好的名声，都反映在你的前端是如何设置的。大多数应用程序的用户不使用命令行标志来运行您的神奇工具。他们必须看到它，并且你必须通过你的 UI 提供你的程序的所有输入和输出的透明性。就像我之前说过的，它从停止你正在做的事情并安装 react/ES6/Material UI 开始。许多 web 开发人员的工作正在转向这种类型的堆栈，其中所有 UI 的中心是 React。

让我们去写一些代码，踢一些屁股，在你的职业生涯中赚一些钱！如果你不做出反应，你可能会失业，或者远远落后于 NPM 和 javascript 革命。这意味着你是“世界的 MCFW”(一个经历了和我相似的 javascript 之旅的朋友)，你可能还没有完全爱上 React！即使 Javascript 有时会惹恼所有的开发人员，但它是我们赚钱的生命线，也是我们表达前端 web 开发和编程的艺术和美感的唯一方式。尊重它，并意识到 react 解决了我们都有的前端问题。