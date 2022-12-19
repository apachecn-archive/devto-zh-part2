# 理解 React 挂钩

> 原文：<https://dev.to/dan_abramov/making-sense-of-react-hooks-2eib>

*原载于[媒体](https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889)T3】*

本周， [Sophie Alpert](https://mobile.twitter.com/sophiebits) 和我在 React Conf 上提交了“Hooks”提案，随后是来自[的 Ryan Florence](https://mobile.twitter.com/ryanflorence) 的深入探讨:

[https://www.youtube.com/embed/dpw9EHDh2bM](https://www.youtube.com/embed/dpw9EHDh2bM)

我强烈建议观看这个开场主题演讲，看看我们试图用 Hooks 提案解决的问题。然而，即使是一个小时也是一个很大的时间投资，所以我决定在下面分享一些关于钩子的想法。

> 注意:钩子是一个实验性的提议。你现在不需要了解他们。另外，请注意，这篇文章包含我的个人观点，并不一定反映 React 团队的立场。

### 为什么要挂钩？

我们知道组件和自顶向下的数据流帮助我们将一个大的 UI 组织成小的、独立的、可重用的部分。然而，我们通常不能进一步分解复杂的组件，因为逻辑是有状态的，不能提取到一个功能或另一个组件。有时，当人们说 React 不允许他们“分离关注点”时，这就是他们的意思

这些情况非常常见，包括动画、表单处理、连接到外部数据源，以及我们希望从组件中完成的许多其他事情。当我们试图仅用组件来解决这些用例时，我们通常会以如下方式结束:

*   **难以重构和测试的庞大组件**。
*   **不同组件和生命周期方法之间的逻辑**重复。
*   **复杂模式**如渲染道具和高阶组件。

我们认为 Hooks 是解决所有这些问题的最佳选择。**钩子让我们将组件内部的逻辑*组织成可重用的隔离单元:***

> ![unknown tweet media content](../Images/2e0c4edf646df4350118be4be083c75a.png)![Sunil Pai profile image](../Images/a31448fc6bf24085d4d58df848142883.png)Sunil Pai[@ three pointone](https://dev.to/threepointone)![twitter logo](../Images/65e26e35707d96169ec8af6b3cbf2003.png)好了，我从 react conf 中取出了丹的职业/钩子代码，涂黑了“不必要的”位，然后用“关注”对这些位进行了颜色编码。好多了。这种影响在更复杂的组件中被放大，在这些组件中，关注点在生命周期方法中被分割和混合。2018 年 10 月 28 日下午 17:11[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1056594421079261185)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1056594421079261185)320[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1056594421079261185)1182

> ![unknown tweet media content](../Images/505e46cc0169c71bcdd6e5ebfbb82635.png)![Play butt](../Images/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/DqsCilOU0AAoS7P.mp4" type="video/mp4"></video>![Pavel Prichodko profile image](../Images/11f7a7cb34d58ba6c457a32b00e4b841.png)Pavel prichodk[【prchdk】](https://dev.to/prchdk)![twitter logo](../Images/65e26e35707d96169ec8af6b3cbf2003.png)从 [#ReactConf2018](https://twitter.com/hashtag/ReactConf2018) 中取出 [@dan_abramov](https://twitter.com/dan_abramov) 的代码并将其可视化，以便您可以看到2018 年 10 月 29 日下午 17:26[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1056960391543062528)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1056960391543062528)386[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1056960391543062528)1272

**钩子在一个组件内部*应用 React 哲学(显式数据流和组合)，而不仅仅是在*组件之间*。***这就是为什么我觉得钩子非常适合 React 组件模型。

与渲染道具或高阶组件等模式不同，钩子不会在组件树中引入不必要的嵌套。他们也没有 mixins 的缺点。

即使你有发自内心的第一反应(就像我一开始做的那样！)，我鼓励你给这个建议一个公平的尝试和发挥。我想你会喜欢的。

### 勾手会让 React 臃肿吗？

在我们详细研究钩子之前，您可能会担心我们只是添加了更多的概念来对钩子做出反应。那是一个公平的批评。我认为，虽然学习它们肯定会有短期的认知成本，但最终结果将是相反的。

**如果 React 社区接受了 Hooks 的提议，这将*减少*编写 React 应用程序时需要处理的概念数量。**钩子可以让你一直使用函数，而不用经常在函数、类、高阶组件、渲染道具之间切换。

就实现大小而言，钩子支持增加的 React 只有大约 1.5kB (min+gzip)。虽然这并不多，但是采用钩子也有可能减少你的包的大小，因为使用钩子的代码比使用类的代码更容易缩小。下面的例子有点极端，但它有效地证明了为什么(点击查看整个线程):

> ![unknown tweet media content](../Images/1a6d832240cfc90cb671e2308665aa31.png)![BOOlean 👻🐶🏳️‍🌈 profile image](../Images/5f34ee3e55c667f3ddb9c58276c592cf.png)布尔👻🐶🏳️‍🌈[@ Jamie builds](https://dev.to/jamiebuilds)![twitter logo](../Images/65e26e35707d96169ec8af6b3cbf2003.png)我们也来比较一下这些 minify 有多好:2018 年 10 月 27 日上午 02:51[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1056015484364087297)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1056015484364087297)53[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1056015484364087297)352

Hooks 的提议不包括任何突破性的改变。即使你在新编写的组件中采用了钩子，你现有的代码也会继续工作。事实上，这正是我们建议的——不要做任何大的重写！在任何关键代码中采用钩子都是一个好主意。尽管如此，如果你能对 16.7 alpha 进行实验，向我们提供关于[钩子提议](https://github.com/reactjs/rfcs/pull/68)和[报告任何错误](https://github.com/facebook/react/issues/new)的反馈，我们将不胜感激。

### 钩子到底是什么？

为了理解钩子，我们需要退一步考虑代码重用。

今天，在 React 应用程序中有很多方法可以重用逻辑。我们可以编写简单的函数，调用它们来计算一些东西。我们也可以编写组件(组件本身可以是函数或类)。组件功能更强大，但是要渲染一些 UI。这使得它们不便于共享非可视化逻辑。这就是我们如何结束复杂的模式，如渲染道具和高阶组件。如果只有*一种*通用的重用代码的方式而不是这么多，反应不是更简单吗？

函数似乎是代码重用的完美机制。在功能之间移动逻辑最省力。然而，函数内部不能有局部反应状态。如果不重组代码或引入 Observables 这样的抽象，就无法从类组件中提取“观察窗口大小并更新状态”或“随时间动画化值”这样的行为。这两种方法都伤害了我们喜欢的 React 的简单性。

钩子正好解决了这个问题。钩子允许你通过一个函数调用来使用函数的反应特性(比如状态)。React 提供了一些内置的挂钩，公开了 React 的“构建块”:状态、生命周期和上下文。

由于钩子是常规的 JavaScript 函数，你可以将 React 提供的内置钩子组合成你自己的“自定义钩子”。这使您可以将复杂的问题转化为一行程序，并在您的应用程序之间或与 React 社区共享它们:

> ![Laurie Voss profile image](../Images/b2c6b8615fb33371b900846f231881f4.png)劳里·沃斯@谢多![twitter logo](../Images/65e26e35707d96169ec8af6b3cbf2003.png)“你可以用 npm 安装比以前更大比例的应用程序代码”，我认为这将是 Hooks 的一个重要卖点。2018 年 10 月 29 日 22 点 05 分[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1057030727512911874)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1057030727512911874)2[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1057030727512911874)23

请注意，从技术上讲，自定义挂钩不是 React 特性。编写自己的钩子的可能性自然来自钩子的设计方式。

### 给我看看一些代码！

假设我们想要为一个组件订阅当前的窗口宽度(例如，在一个狭窄的视窗上显示不同的内容)。

现在有几种方法可以编写这种代码。它们涉及编写一个类，设置一些生命周期方法，或者甚至可能提取一个渲染道具或一个更高阶的组件，如果你想在组件之间重用它的话。但是我认为没有什么比这个更好的了:

<figure>[![](../Images/8ecac79d450ffe7ce76191e1534a133a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D68Dgqh6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1010/1%2Aj8U3U0nZvmEKJrSOK7iH5g.png)

<figcaption>[https://gist . github . com/gae Aron/cb5 add 26336003 ed8c 0004 C4 ba 820 EAE](https://gist.github.com/gaearon/cb5add26336003ed8c0004c4ba820eae)</figcaption>

</figure>

如果你阅读这段代码，它会完全按照它所说的去做。我们*在组件中使用窗口宽度*，如果组件发生变化，React 会重新渲染组件。这就是钩子的目标——使组件真正具有声明性，即使它们包含状态和副作用。

让我们看看如何实现这个定制钩子。我们将*使用 React 本地状态*来保持当前的窗口宽度，并且*使用一个副作用*来设置当窗口调整大小时的状态:

<figure>[![](../Images/bee824446171203c001199627bd645ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uK5507Ix--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9QhpwSGTKM-c8sc4UNcxqA.png)

<figcaption>[https://gist . github . com/gae Aron/cb5 add 26336003 ed8c 0004 C4 ba 820 EAE](https://gist.github.com/gaearon/cb5add26336003ed8c0004c4ba820eae)</figcaption>

</figure>

正如你在上面看到的，像 *useState* 和 *useEffect* 这样的内置 React 钩子充当了基本的构建模块。我们可以直接从我们的组件中使用它们，或者我们可以将它们组合成定制的挂钩，比如 *useWindowWidth* 。使用定制钩子就像使用 React 的内置 API 一样习惯。

你可以从这个概述中了解更多关于内置钩子的信息。

钩子是完全封装的——每次你调用一个钩子，它都会在当前正在执行的组件中获得独立的本地状态。对于这个特殊的例子来说，这无关紧要(所有组件的窗口宽度都是相同的！)，但这正是钩子如此强大的原因。它们不是共享*状态*的方式——而是共享*状态逻辑*的方式。我们不想打破自上而下的数据流！

每个钩子可能包含一些局部状态和副作用。您可以在多个钩子之间传递数据，就像您通常在函数之间做的那样。它们可以接受参数和返回值，因为它们是 JavaScript 函数。

这里有一个 React 动画库实验钩子的例子:

[https://codesandbox.io/embed/ppxnl191zx](https://codesandbox.io/embed/ppxnl191zx)

请注意在演示源代码中，交错的动画是如何通过在同一个渲染函数中的几个自定义钩子传递值来实现的。

<figure>[![](../Images/e42b0dafdd82ac2426e05ca97a6abaa3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DmFM93-o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANJ2G1R_32k95WiPel5JHpg.png)

<figcaption></figcaption>

</figure>

(如果你想了解这个例子的更多信息，请查看本教程。)

在钩子之间传递数据的能力使它们非常适合表达动画、数据订阅、表单管理和其他有状态抽象。与渲染道具或高阶组件不同，钩子不会在你的渲染树中创建一个“虚假的层次”。它们更像是附加在组件上的“记忆细胞”的简单列表。没有多余的层。

### 那么班级呢？

在我们看来，定制钩子是钩子提议中最吸引人的部分。但是为了让定制钩子工作，React 需要为函数提供声明状态和副作用的方法。这正是像*使用状态*和*使用效果*这样的内置钩子让我们做的。你可以在[文档](https://reactjs.org/docs/hooks-overview.html)中了解它们。

事实证明，这些内置钩子不仅仅是对创建定制钩子有用。它们*也*足以定义一般的组件，因为它们为我们提供了所有必要的特性，比如状态。这就是为什么我们希望钩子成为未来定义 React 组件的主要方式。

我们没有不上课的计划。在脸书，我们有成千上万的班级组成部分，像你们一样，我们不打算重写它们。但是如果 React 社区支持钩子，那么推荐两种不同的方式来编写组件是没有意义的。钩子可以覆盖类的所有用例，同时在提取、测试和重用代码方面提供更大的灵活性。这就是为什么 Hooks 代表了我们对 React 未来的愿景。

### 但是钩子不是魔法吗？

钩子的[规则你可能已经惊讶了。](https://reactjs.org/docs/hooks-rules.html)

虽然必须在顶层调用钩子并不常见，但你可能不希望在条件中定义状态，即使你可以这样做。例如，你也不能在一个类中有条件地定义状态，在与 React 用户交谈的四年多时间里，我没有听到任何关于这一点的抱怨。

这种设计对于启用定制挂钩而不引入额外的语法噪声或其他缺陷至关重要。我们认识到最初的不熟悉，但我们认为这种权衡是值得的。如果你不同意，我鼓励你在实践中玩它，看看它是否会改变你的感觉。

我们已经在生产中使用钩子一个月了，看看工程师们是否被这些规则弄糊涂了。我们发现，在实践中，人们在几个小时内就会习惯它们。就我个人而言，我承认这些规则一开始对我来说也“感觉不对”，但我很快就克服了。这次经历反映了我对 React 的第一印象。(你喜欢立即做出反应吗？直到我第二次尝试才知道。)

注意，钩子的实现也没有什么“魔力”。正如杰米[指出的](https://mobile.twitter.com/jamiebuilds/status/1055538414538223616)，它看起来非常类似于这个:

<figure>[![](../Images/106fe2234a03835033bd91716c942a06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rSSObFNs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxNeUnpwUvFMuQu9Zr6A3AA.jpeg)

<figcaption>[https://gist . github . com/gae Aron/62866046 e 396 F4 de 9 b 4827 EAE 861 ff 19](https://gist.github.com/gaearon/62866046e396f4de9b4827eae861ff19)</figcaption>

</figure>

我们为每个组件保留一个钩子列表，每当使用一个钩子时，就移动到列表中的下一项。由于钩子的规则，它们的顺序在每次渲染时都是相同的，所以我们可以为组件提供每次调用的正确状态。不要忘记，React 不需要做任何特殊的事情来知道哪个组件正在渲染——React*就是调用你的组件的*。

([Rudi yard ley 的这篇文章](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e)包含了一个很好的视觉解释！)

也许您想知道 React 将钩子的状态保存在哪里。答案是它保存在 React 保存类状态的同一个地方。React 有一个内部更新队列，它是任何状态的真实来源，无论您如何定义您的组件。

钩子不依赖于现代 JavaScript 库中常见的代理或 getters。因此，可以说钩子没有解决类似问题的一些流行方法那么神奇。我要说钩子和调用 *array.push* 和 *array.pop* 一样神奇(调用顺序也很重要！)

钩子的设计并不局限于反应。事实上，在提案发布后的最初几天，不同的人提出了针对 Vue、Web 组件甚至普通 JavaScript 函数的相同 Hooks API 的实验性实现。

最后，如果您是一个函数式编程纯粹主义者，并且对依赖可变状态作为实现细节的 React 感到不安，您可能会发现使用代数效果以纯粹的方式实现处理挂钩是令人满意的(如果 JavaScript 支持的话)。当然，React 在内部一直依赖可变状态——正是为了让*您*不必如此。

无论你是从更务实还是教条的角度来考虑(如果你是这样的话)，我希望这些理由中至少有一个是有道理的。如果你好奇的话，Sebastian(Hooks 提案的作者)也在 RFC 的评论中回应了这些和其他的问题。最重要的是，我认为钩子让我们用更少的努力构建组件，并创造更好的用户体验。这也是我个人对 Hooks 感到兴奋的原因。

### 传播爱，不是炒作

如果钩子对你来说仍然没有吸引力，我完全可以理解。我仍然希望你能让他们尝试一个小的宠物项目，看看这是否会改变你的看法。无论你还没有经历过 Hooks 解决的问题，或者你有不同的解决方案，请在 RFC 中告诉我们！

如果我*让你兴奋了，或者至少有一点好奇，那太好了！我只想请你帮个忙。现在有许多人正在学习 React，如果我们匆忙地为一个才推出几天的特性编写教程和声明最佳实践，他们会感到困惑。有些关于钩子的事情甚至对 React 团队中的我们来说还不是很清楚。*

**如果你在钩子不稳定的时候创建了任何关于钩子的内容，请突出说明它们是一个实验性的提议，并附上一个链接到** [**官方文档**](https://reactjs.org/hooks) **。我们会随时更新提案的任何变更。我们也花了相当多的努力使它变得全面，所以许多问题已经在那里得到了回答。**

当你和其他不像你这么兴奋的人说话时，请保持礼貌。如果你发现一个误解，如果对方愿意接受，你可以分享额外的信息。但是任何改变都是可怕的，作为一个社区，我们应该尽力帮助人们，而不是疏远他们。如果我(或者 React 团队中的其他人)没有遵循这个建议，请告诉我们！

### 下一步

查看 Hooks 提案的文档以了解更多信息:

*   [引入挂钩](https://reactjs.org/docs/hooks-intro.html)(动机)
*   [钩子一瞥](https://reactjs.org/docs/hooks-overview.html)(预排)
*   [编写自定义钩子](https://reactjs.org/docs/hooks-custom.html)
*   [Hooks FAQ](https://reactjs.org/docs/hooks-faq.html) (你的问题很可能在那里得到了回答！)

Hooks 仍处于早期阶段，但我们很高兴听到大家的反馈。你可以把它发送到 RFC，但我们也会尽最大努力跟上 Twitter 上的对话。

如果有什么不清楚的地方，请告诉我，我很乐意和你谈谈你的顾虑。感谢您的阅读！