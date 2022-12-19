# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-1p0o>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

[@dan_abramov](https://dev.to/dan_abramov) 跳进了 **[为什么 React 社区遗漏了关于 Web 组件的要点](https://dev.to/ben/why-the-react-community-is-missing-the-point-about-web-components-1ic3)** 来提供他的观点。原始帖子以及完整的讨论非常值得您花时间去阅读:

[![dan_abramov profile image](img/7e33ef2d061d9d6f8a6b04e8fad121ee.png) ](/dan_abramov) [ Dan Abramov ](/dan_abramov) • [<time datetime="2018-11-07T20:09:32Z" class="date-short-year"> Nov 7 '18 </time> • Edited on <time datetime="2018-11-07T20:31:42Z" class="hidden m:inline-block date-no-year">Nov 7</time>](https://dev.to/dan_abramov/comment/6kdc) 

从我的角度讲几点。(我在 React 上工作。)

1.  我们不反对更好地支持 web 组件。问题是没有单一的“web 组件社区”。有多个子社区。你在文章中提到了“web 组件库”。这些库不同意单一的标准，所以 React 需要在诸如“服务器渲染如何与 web 组件一起工作”的争论中选择一方。无论我们选择什么，都会有很大的下游影响，我们不愿意支持更多与对语义的小心谨慎有关，而不是与 web 组件本身有什么冲突。

2.  正如我在上一点中提到的(你在帖子中也提到了)，有多个“web 组件库”。据我所知，对 React 的许多批评也适用于这样的库。我不认为抵消“多姆·FUD”的方法是引入“FUD 图书馆”。如果您使用一个库来声明性地定义和更新您的 web 组件，您并没有遵循与使用 React 在概念上不同的方法。

3.  说“你可以用 WCs 做任何你能在 React 中做的事情”是一把双刃剑。是的，当然，你可以做任何事情——因为我们实际上还没有就任何约束达成一致。如果约束是“你不能在上面使用类似 React 的库”，我想你会发现有很多事情很难用像 vanilla WC APIs 这样的命令式抽象来完成。我们已经讨论过使用 React 作为统一抽象让我们做什么(比如非阻塞渲染，或者在不降低用户体验的情况下动态加载 UI 代码)。你可能想看看他们([youtube.com/watch?v=nLF0n9SACd4](https://www.youtube.com/watch?v=nLF0n9SACd4)，[youtube.com/watch?v=ByBPyMBTzM0](https://www.youtube.com/watch?v=ByBPyMBTzM0))。当然，如果你在 WCs 上使用 React 这样的库，你*可以*做这些事情。但是这否定了你不需要类似 React 的库来做这件事的论点。

总而言之:我们很乐意在 React 中更好地支持 WCs。我们不想仓促行事，希望确保这种支持是经过深思熟虑的。此外，我们相信有很多东西是原始的命令式 WC APIs 不能给你的——对它们来说，像 React 这样的东西甚至在 WC 世界中也是合适的。最后，有一个流传的神话，一旦你写了 React 代码，你就不能把它作为 web 组件重用。不是这样的——正如你从文档中看到的，只有几行代码:[reactjs.org/docs/web-components.ht...](https://reactjs.org/docs/web-components.html#using-react-in-your-web-components)

希望有意义，并提供一些额外的背景！

> [1]:例如，如果我没弄错的话，Preact 选择的语义使得向 DOM base 元素引入一个新的标准属性成为 web 的一个突破性变化。如果可能的话，我们会尽量避免这样的问题——正是因为 React *和*从 MooTools 中学到了东西，我们不想重蹈`Array.prototype.flatten()`的覆辙。

[@ben](https://dev.to/ben) 本周在阿里的 **[每日编码谜题-11 月 4 日-11 月 9 日](https://dev.to/aspittel/daily-coding-puzzles---nov-4th---nov-9th-5bfa)** 线程中以他的答案获得了简洁奖。"你的目标是实现一个差分函数，从一个列表中减去另一个列表，并返回结果."：

[![ben profile image](img/aafbcbcc2891483db855a7b6ec39b85c.png) ](/ben) [ Ben Halpern ](/ben) • [<time datetime="2018-11-09T18:35:48Z" class="date-short-year"> Nov 9 '18 </time>](https://dev.to/ben/comment/6lk2) 

红宝石

```
a - b 
```

Enter fullscreen mode Exit fullscreen mode

在一次以抽出有意义的时间为中心的谈话中，如果今天是周六，你要到周一才会再次编码，你如何让自己的注意力从当前的工作中转移出来？——[@ benny powers](https://dev.to/bennypowers)带着很棒的回答跳了进来:

[![bennypowers profile image](img/55f19a4b509f7ff68f6b63a38a756013.png) ](/bennypowers) [ Benny Powers 🇮🇱🇨🇦 ](/bennypowers) • [<time datetime="2018-11-10T15:33:13Z" class="date-short-year"> Nov 10 '18 </time>](https://dev.to/bennypowers/comment/6m4g) 

作为一名虔诚的犹太人，一旦我们在周五晚上点燃蜡烛，我们就不会碰我们的手机或电脑，直到 havdallah 周六晚上(除非紧急情况，比如我在周六凌晨 4 点接生了我们最小的孩子，但我跑题了)。

我们不是社交媒体，而是社交。我们不吃饲料，而是大吃大喝。我们讨论的不是合并冲突，而是犹太学术中巨大的 *makhlokos* (法律和哲学上的意见分歧)。

简而言之，这是*成为*而不是*做*的一天。

整个 **[没人告诉我的关于成为一名软件工程师的事情](https://dev.to/anaulin/things-nobody-told-me-about-being-a-software-engineer-3pa5)** 线程是一个很好的起点，也是关于成为一名软件工程师的大量非显而易见的现实的讨论。 [@shiling](https://dev.to/shiling) 拔得头筹:

[![shiling profile image](img/7b501dcbbd64fdabff80827f5b8c105e.png) ](/shiling) [ Shi Ling ](/shiling) • [<time datetime="2018-11-09T07:37:51Z" class="date-short-year"> Nov 9 '18 </time>](https://dev.to/shiling/comment/6lcg) 

> CSS 是最复杂的现代编程语言

如此真实。

有时候有点像伏都教。我可以理解为什么很多人宁愿做后端。我从 StackOverflow 中学到的关于 CSS 的知识比从书上学到的多得多，好在现在有了 MDN。当大三学生问我“我如何学习 CSS”时，我不知道从哪里开始。

**[确定自己不需要费心学习的东西就好](https://dev.to/ben/its-good-to-identify-what-you-dont-need-to-bother-learning-1nm6)** 。 [@shalvah](https://dev.to/shalvah) 用这句简洁的话抓住了这条线索的精神:

[![shalvah profile image](img/f00ada0a47fcaef46f1b796b3df65275.png) ](/shalvah) [ Shalvah ](/shalvah) • [<time datetime="2018-11-08T22:32:32Z" class="date-short-year"> Nov 8 '18 </time>](https://dev.to/shalvah/comment/6l87) 

FOMO 是焦点的敌人。😞

下周见，更多精彩评论，✌