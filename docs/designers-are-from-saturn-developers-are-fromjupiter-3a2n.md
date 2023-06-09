# 设计师来自土星，开发者来自木星

> 原文：<https://dev.to/albinotonnina/designers-are-from-saturn-developers-are-fromjupiter-3a2n>

### 关于‘但是在规格上看起来不一样’的效果，UI 工具包和其他东西。

[![](img/ca7fdcc6dfb01ca464fcaf3eaadafc15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2YdojSg6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A8fAGPEkr7UAqhNf0z3T5Zg.jpeg)

两颗行星但至少是同一个太阳系**！与行星的类比到此结束。**

#### 过敏建议

这是一篇关于[设计系统](https://www.uxpin.com/studio/blog/design-systems-vs-pattern-libraries-vs-style-guides-whats-difference/)的文章，特别是关于 UI 工具包和设计师与开发者之间交流的动态的主题。

**设计师**，一些事情告诉我你了解设计系统，你可能会挖掘它们:)如果你想了解更多，内森·柯蒂斯写了很多关于它的东西。我非常喜欢和尊重他在设计系统上的[工作](https://medium.com/eightshapes-llc/tagged/design-systems)。

**开发者**，最后我要展示一些代码。操场是一个 React + CSS-in-JS 库(比如 emotion 或者 styled-components)的 app。

### 一种典型的场景

我们的设计师制作了一系列精美的设计，包括我们* Documents *页面的布局:

[![](img/3eedf454b4498a736656cec249573842.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mY61vmQa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Ak0iI0xz1Qls-w1vT-PwWLw.png)
[https://www . sketchappsources . com/free-source/2576-ooto-productivity-dashboards-sketch-freebie-resource . html](https://www.sketchappsources.com/free-source/2576-ooto-productivity-dashboards-sketch-freebie-resource.html)

很干净，很平衡，有点赏心悦目。对于设计师来说，这是一个漫长过程的高潮，包括研究、采访、思考、回顾、反思、白板、原型制作、线框图等一系列任务。这是一个漫长而乏味的过程，开发人员通常不会经历。

设计师到底是怎么产生这个形象的？他们可能使用了一个设计工具包。一个很流行的是[小品](https://www.sketchapp.com/)。

唉，这个软件的工作方式与开发人员的工作方式截然相反。**我说这就是我们问题的关键**。

设计师需要能够让他们快速重复的工具，回顾和更新，一个接一个的反馈，一个接一个的利益相关者会议。* *设计师需要素描这样的工具。**

[![](img/50a524157563dd88eef1604f9ea83775.png)](https://i.giphy.com/media/emqXnYodLaS4M/giphy.gif) 
img:为利益相关者设计。

#### 另一方面，开发人员的工作方式不同。

他们在不断变化的代码库上工作，这些代码库在任何时候都必须产生一个应用程序的工作版本。实现像我们例子中这样的布局需要付出努力，包括设计、抽象、实现、重构、测试、审查、重构、错误修复、重构。开发人员需要确保他们不会破坏其他任何东西或者用**坏的、重复的代码**污染代码库。

[![](img/445cf97f15c83827e3febb8bd30a242f.png)](https://i.giphy.com/media/shXBoJHIx6Lni/giphy.gif) 
img:开发者

> 设计师更像是在不断循环的开发中来回跳跃的开发者。

#### 可视化规格文件

现在是设计师和开发者交流的时候了，*交出接力棒。*

有布局，空间和颜色等等要记录。Sketch 或者其他任何工具都不太了解你当前的代码库，你的文件结构，你的抽象，那么 Sketch 能做什么呢？衡量事物。这就是将要产生的结果:

[![](img/319af1a7c79564bee4aa6d29ccb4d523.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JpLrti0J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2At8K0NcmFdWBkCTalH92aVw.png)

[![](img/e19102ae8222b88b7392cd094feeee21.png)T2】](https://i.giphy.com/media/i8tV2kJB8Gig8/giphy.gif)

img:开发人员对规范文件有着复杂的感觉。

### 几天过去了…

东西都准备好了，设计师们第一眼就看到了:

[![](img/16fe03fae35527f9741cb2cea6d9ca2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iWldHfSE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AOp-Fmt6HD_onew7zktxn9Q.png)

[![](img/8f295846e796fd317ff90d196a98aeb5.png)](https://i.giphy.com/media/y1WDIwAZRSmru/giphy.gif) 
img:设计师对最终的结果百感交集。

### 失意的设计师，失意的开发者。

那才是结界真正被打破的时刻。**规格文件**。颜色、间距、排版、布局、沟通错误的细节、缺失行为等小问题。

开发人员将不得不在代码库中动态地解释规范并使之适应他们自己的系统，而他们只需要担心新特性的业务逻辑的实现。设计师不应该受到责备，虽然他们可能只是不知道这样的系统。

我爷爷常说:

> 当设计师和开发人员没有很好地沟通时，获得一个设计系统，它有一套很好地共享和沟通的工具、抽象和约束。

### 你需要一个好的 UI 工具包

通过一个共享的系统，设计师和开发人员可以真正无压力地有效沟通。UI 工具包旨在**强化**设计系统中记录的原则。它由一套**高度共享和记录的**约定、UI 模式、行为来管理，由每个人设计、测试和同意。这是设计师和开发人员共同努力的地方。

### 为什么你真的需要一个好的 UI 工具包

*   你的应用程序越来越复杂了吗？
*   设计师们谈论了很多关于应用程序不一致性的问题？
*   CI/CD？走得快快快？
*   远程团队？
*   那些 CSS 文件变得有点乱？
*   开始在意 app 的大小了？
*   用户体验是你商业模式的核心吗？

不需要勾选所有的检查，甚至一个可能就够了:)

### 为什么您需要自己的 UI 工具包

一个设计系统是关于语言的。视觉语言、UI 设计语言等。**定义自己的**需要很大的努力:产品、设计、工程，所有这些部门都会大量参与。

很多时候那只是**不可行的选择**。有很多令人惊叹的框架，[语义界面](https://react.semantic-ui.com/)，[蚂蚁设计](https://ant.design/)，[引导](https://getbootstrap.com/docs/4.1/getting-started/introduction/)，[材质界面](https://material-ui.com/)。它们都带有一种预制的语言和一个久经考验的用户界面工具包。

**军规？**我的看法？很快它们就不再适合你了。**你会想逃避它。**此外，UI 工具包可能设计得很难控制。请记住，那些框架是用来传递无数案例的，可能比您需要的更多。另外，这种**的额外复杂性也是以 kb** 为单位的。

#### 盗作艺人

不要采用 UI 工具包。取而代之的是从别人那里复制，我的意思是选择最适合你的部分，然后从头开始实现它们。现在，高度以用户为中心的公司拥有自己的设计系统是很常见的，其中很多都是开源的！

看看这个设计系统列表:
[https://adele.uxpin.com](https://adele.uxpin.com/):

*   BBC: [凝胶](http://www.bbc.co.uk/gel)
*   特雷罗:[玉米片](https://design.trello.com/)
*   销售人员:[闪电](https://www.lightningdesignsystem.com/)

还有几十个。最终，这一切都是设计和交付这个**的问题。这是关于**为你的领域**打造特定的东西，也是**独特的**和代表你的**品牌的**。这是值得的，你也可以给它一个好听的名字:)**

### 我们做一个吧

我将向您展示引导我们自己的设计系统是多么容易。

> 我要称它为拉里。

让我们取一小部分布局，并尝试从头开始构建:

[![](img/262e9786022ebcc4a9b8b13f7e0b5a52.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6SgDfIG6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ACtk5K5_2twIWj8JBx2KrGw.png)

### 先结束结果

下面的 CodeSandbox 是世界上唯一一个实现 **Larry:** 的应用

[![Edit larry](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/github/albinotonnina/larry/tree/master/?module=%2Fsrc%2Fdesign-system%2Fconstants%2Findex.js)

你可以在 https://github.com/albinotonnina/larry 的**GitHub**:
T5】上找到**拉里**

### 文档化

这一点最重要。谁负责这个，可能是设计师？嗯，一般来说是的，但是相信我，你们应该平等地参与记录你们的语言。你们俩应该在这里的所有事情上都达成一致。

让我们开始定义一些非常基本的约定:

#### 颜色

让我们为我们的布局生成一个调色板。

[![](img/cd78af5786ef46bb87bdd19f5c77beb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Axz8XxaS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AKqcQkVqdJBKSuS7loVd7Yw.png)

我建议从这些颜色中定义一系列语义名称，就像这样:

**header text**= Japanese indigo
**paragraph text**= Japanese indigo

**element background default**= Snow
**element background hover**=
brilliant azure
**element button**= light gray—alpha 60%

当 * *指定** (这是一个单词)时，你们都要使用这些名字**。**

#### 间距

**格外注意间距。**如果没有明确的间隔策略，事情可能会变得非常糟糕。

定义并同意间距系统，例如:

[![](img/e9330e094ad362bc46760887e53027b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aoJANOx0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A8vWeubT5wjJs-cOQHf7cNQ.png)

**一个规格文件应该是这样的:**

[![](img/68359c868854ecdd193814adaba813d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tUvkvgdI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AwopFDrMRdrdLlPv-XMO6Zw.png)

#### 排版

确保标题、段落中的字体大小、字体粗细、行高、边距、颜色等完全匹配。用你喜欢的名字称呼他们，比如 HeaderHuge，HeaderLarge，HeaderTiny 或者适当的使用语义标签(h1，h2，h3)。只要**确保你在这个**上是一致的。

#### 图案

什么和 UI Toolkit 押韵？**图案库**！您需要开始填充您的模式库。你想要的是拥有你需要的组件，以你同意的方式运行，这样你就可以在任何时候以你想要的方式组合它们。

从**粒子**、**图元**这样一个盒子组件开始，用于当你必须在其他东西周围设置间距和边框的时候。

添加**更专业的新粒子**，比如一个文本组件或者一个 flex 组件，你可以把它想象成一个盒子组件+一些 Flex 工具。

将它们视为孤立存在的粒子，不知道它们将被使用的环境以及它们周围应该存在的空间。

继续更复杂的 UI 组件，其他最小组件的组合，等等。

这里重要的不是技术或者文档中的哪种抽象。重要的是你们一起做这个**。**

 **[![](img/0ca9533041858a535ddeb216f9ee6c66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZtycVXEv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AYYrdHo0lKQrNkVZQ9W7D_g.png) 
更复杂 UI 组件的例子

### 你抓住要点了吗？

你已经定义了常数，还需要制造一些粒子。

你会反复强调这些粒子，并很快扩展库，所以拥抱并准备好弹性。开发人员，你不希望设计师在开始实现代码之前完成整个系统的文档化。* *你们必须一起做这件事，否则它不会起飞。 *

所以，设计师和开发人员，** * * *在文章* *之后，如果你还没有 Larry * *的话，就自己动手做吧！

### 代码

你有一个拉里的副本，你可以克隆并玩它。您的 Larry 可能不同，或者您可能使用不同的框架，因此我将介绍这种方法的关键点。

#### 主题，定义常量

这是一个包含主题常量的对象，所以空间定义、颜色、字体、排版、断点等等。这是拉里的主题，这是它的一个样本版本:

[![](img/daf574b1fdfafca4e7ac11022e7564ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Oa9Y3oCR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ActftgBakeNeruWAQdJArfw.png)

这里您可以实现的复杂性/完整性没有限制，毕竟这是一个生成 JavaScript 对象的问题，想象一下您可以做什么！

[![](img/1a1481dd3687118eb1216b1379968ddc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o69uYdgL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ARjRcYjLRD5haC8DDgQYKrw.png)

这是一个核心文件。每个颜色、边距或填充、字体大小或字体粗细或断点都必须来自这里，并且只能来自这里。

CSS-in_JS 库是令人惊奇的工具， [styled-system](https://github.com/jxnblk/styled-system) 使它们变得更好。这是一组设计系统的实用程序，由将`props`作为参数并返回样式对象的函数组成，同时使使用主题中的值和跨断点应用样式变得更简单。

这种方法利用了这个工具，请随意对其进行评估。

#### 将主题插入你的应用程序

将这些常量提供给你的应用程序:应用程序中的每个组件都可以访问我们的主题常量。

[![](img/9b504aff4745fd72556252f9185e0313.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QUG9E7AL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A6M6Q9zuKzNlcWPphfUI9ww.png)

#### 创建基本 UI 组件

[![](img/ca28d321202644faa97ab2baaa1c9424.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fSWWdGl0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A1eIZb7yNcdppncpoj5fCLw.png) 
一个图元框 UI 组件

#### 更专业的 UI 组件

这是一个 Flex 组件。

[![](img/675847b67f91ea2ddb7a074f374ff346.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b-eUHsBZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Akd_s4cbWxxqcJTlC2N04fw.png)

#### 在你的特征文件中实现 UI 组件

[![](img/8b4d07cc73f2813a2cdde1acead1db08.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---vwD6T4a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AqSxzGojmAS-vfB3LTkZctw.png)

#### 渲染某物的时间

这是实现 UI 组件和业务逻辑的地方。

[![](img/495fe75919147974a141dc925e4ba7c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ar2azXSM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A5vObQKvSpUX9engeMaNX6A.png)

### 文件结构

这是拉里的文件结构。我对文件结构没有强烈的意见，实际上我相信一些不同的东西:移动你的文件直到你对它们感到舒服。

Larry 把所有东西都放到了一个“* * design-system”* *文件夹中，在这里你可以找到它的常量和通用的、可重用的 UI 组件。

还要注意文档布局文件夹中的 UI 文件夹，这是我定义和导出特定于我们特性的 UI 组件的地方。

[![](img/deed2a2b9087ba5518251deceeb063c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--At4H4xVV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AeN6vUZcqCJfmHMdVmKu5EQ.png)

### 结论

对于大型复杂的应用程序，保持 UI 的一致性和内聚性从来都不是一件容易的事情。设计系统有帮助。定制的设计系统和定制的 UI 工具包真的很有帮助。

设计师和开发人员可能对同一个问题有非常不同的方法，但这并不意味着他们不能有效地沟通。

[![](img/6ce95d3452acb1016b83435f141642fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nati_4Yt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ALRjLAzWqaLiYl4N5DZH8Kg.gif)
[https://dribbble.com/shots/2712522-Designer-vs-Developer](https://dribbble.com/shots/2712522-Designer-vs-Developer)

### 感谢阅读

你有积极的经验可以分享吗？

你好，我叫阿尔比诺·汤妮娜，我是一名在伦敦工作的软件工程师，你可以在 [Twitter](https://twitter.com/albinotonnina) 或 [Github](https://github.com/albinotonnina) 或 [Instagram](https://www.instagram.com/albino_tonnina/) 或城市周围找到我。

[![](img/093f8cf863d2d8c709628bc76eaaac49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zaEYIvVl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Andz2zpAwq7qvQhhQI0ieLQ.png)

#### 我的最新文章

[如何在 10 分钟内丢掉一份 IT 工作](https://hackernoon.com/how-to-lose-an-it-job-in-10-minutes-3d63213c8370)
[说到网页设计……介绍一下魔法帽技术🎩✨](https://medium.com/@albinotonnina/magic-hat-technique-408a3fa590bb)

在推特上关注我！**