# 性别代词荧光笔

> 原文：<https://dev.to/bengreenberg/the-gender-pronoun-highlighter-1g80>

当默认受众总是你时，很难意识到还有其他人没有被包括在默认受众中，即使这一点非常明显。作为一名男性，我经常看到一些假设我是读者的文章:“他是...“，”*他听着...*“，”*他表现出...*”等。几乎总是一个“他”。假定的读者几乎总是男性。

当非男性的人从来没有在他们周围的世界中看到自己的影子时，我没有深入研究所有可能对他们产生负面影响的方式，我认为创建一个简单的应用程序来突出这个问题可能会很有趣。

我利用 Next.js 和 React 构建了一个 Node.js 应用程序，以便在文本框中输入任何给定文本时动态突出显示性别代词。

**它是实时的，可以在访问[。](https://gender-pronoun-highlighter.herokuapp.com/)**

<figure>

[![Screenshot of Gendered Pronoun Highlighter](img/36f27054b6d70c68f3a2d49a4e6b18a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3Zu5sSDj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2fuylhnnajofrh4ccggt.png)

<figcaption>Screenshot of Gendered Pronoun Highlighter</figcaption>

</figure>

该应用程序由一个单独的页面`index.js`组成，该页面有几个组件，这些组件将功能分成更小的部分。当用户在`textarea`中输入时，文本被保存在本地状态。该状态被传递给一个`EvalText`组件，该组件在`<span></span>`标签之间呈现一些 CSS 样式。

在`EvalText`组件中，输入的每个单词都根据三个独立的数组进行评估:男性、女性和中性代词。如果在男性或女性代词数组中找到该单词，将对其应用特定的 CSS 样式。现在，性别中性代词还没有被使用，但是还有空间做一些其他的样式。

我已经把代码放在了一个公共的 Github 库中，希望大家能有所贡献。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [本格林伯格](https://github.com/bencgreenberg) / [代词 _ 荧光笔](https://github.com/bencgreenberg/pronoun_highlighter)

### 性别代词荧光笔。让你意识到你在文章中使用了哪些性别代词。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 性别代词荧光笔

### [https://gender-pronoun-highlighter.herokuapp.com](https://gender-pronoun-highlighter.herokuapp.com)

## 目的

这个项目的目的是在我们创作和阅读的文本中强调其中固有的性别偏见。我们有多少次假设默认的人是一个**他**或一个**她**？该项目采取任何文本输入到文本区，并自动突出显示每一个性别代词与刻板的“性别颜色”。(*注意:我不相信有“性别化的颜色”这种东西，但尽管如此，默认的女性代词用粉色，男性代词用蓝色，这让用户很容易就能在视觉上看出他们用的是哪种颜色。*)

这个项目的一个工作实例可以在[在线](https://gender-pronoun-highlighter.herokuapp.com)上找到。

## 装置

要开始使用该项目的本地开发版本，只需将其克隆到您的计算机上，安装并运行:

```
git clone https://github.com/benhayehudi/pronoun_highlighter.git
npm install
npm run dev 
```

## 测试

…

</article>

[View on GitHub](https://github.com/bencgreenberg/pronoun_highlighter)

对于我确定的项目，已经有一些未解决的问题，特别是:

1.  使性别代词列表更加全面
2.  响应式造型
3.  向页面添加计数器，以动态增加/减少每种代词的数量

此外，我欢迎新功能或其他改进。我在午休时写了这篇文章，所以我相信它能从一些爱和关注中受益！

上周，我写了一篇关于[在 5 分钟](https://dev.to/benhayehudi/getting-started-with-nextjs-in-5-minutes-19ah)内开始使用 Next.js 的博客，这是一次很好的体验，我使用这个框架构建了一个我已经有很长时间的想法。在这个世界上，我几乎总是默认的观众，做一些事情是有意义的，尽管是一件小事，但都是一样的，有助于引起人们对这个问题的关注。