# Org 和 Jekyll:一项调查

> 原文：<https://dev.to/gonsie/org-and-jekyll-a-survey-17jl>

我越来越喜欢 Org 模式，并开始在我的博客过程中使用它。我和 Jekyll 一起在 GitHub 页面上主持我的博客。我想把我的帖子保留在 markdown 中，而不是把 HTML 直接推给`_site`。起初，我认为我需要为 Jekyll 构建自己的 org exporter，但是我意识到已经有了许多解决方案(以及关于这些解决方案的博客帖子)。

这篇文章是对 Org 和 Jekyll 现有的博客方法的调查。

## 我当前的进程

[![Blogging Process](../Images/0ffbfdc8ab3727c45ebf6116de097141.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q4msq454--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://gonsie.com/blorg/images/blorg-process.png)

我目前的流程包括几个我想消除的非最佳“修复”步骤。理想情况下，Emacs publish 转换会完全按照我想要的那样产生 markdown。额外的好处是可以利用 Org 发布工作流(而不仅仅是导出文档)。然后，我将能够直接在 Org draft 上进行任何复制编辑，并且发布/部署过程可以在一个按钮的推动下发生。

## 相关工作的调查

在整理我的工作流程之前，我调查了其他人的工作。我发现了一些博客帖子，讨论了每个作者使用 Org mode 和 Jekyll 写博客的经验和工作流程。

有三类方法:

1.  通过开发 Jekyll 插件/转换器来支持以 Jekyll 为中心的工作流。
2.  通过构建组织发布功能，支持以组织和 Emacs 为中心的工作流。这种方法将 Org 内容直接转换成 HTML。
3.  通过完全脱离 Jekyll(即使用不同的平台)来支持 Org-publish 工作流。

### 以哲基尔为中心的方法

以 Jekyll 为中心的方法经常试图让 Org 语法成为 Jekyll 的一等公民。然而，这些工具都没有被 Jekyll 社区采用，因此仍然在 Jekyll 本身之外。这意味着基于 org 的博客不能利用 GitHub 页面的自动发布。

#### 1。 [*一个组织模式的杰基尔插件*由乔尔的日志](https://joelmccracken.github.io/entries/org-mode-jekyll-plugin/)

这篇文章采用了一种*有文化的编程*方法来开发 Jekyll 插件以理解 org 文档。这篇文章很好地回顾了幕后发生的事情(包括 I/O 流的细节，这可能很复杂)。

#### 2。GitHub 上的[*egg caker/Jekyll-org*](https://github.com/eggcaker/jekyll-org)

又一个 Jekyll 插件。也许我忽略了为什么这种方法如此有吸引力。

#### 3。[*tjaartvdwalt/Jekyll-org-mode-converter*在 GitHub 上](https://github.com/tjaartvdwalt/jekyll-org-mode-converter)

这个项目中的自述文件相当于博客文章。它涵盖了一些其他现有的项目，并再次使用 Jekyll 转换器/插件方法。

### 组织发布方式

有许多“黑客”将 Jekyll 和 Org 混为一谈。不幸的是，似乎 Jekyll 和 Org 都没有得到充分利用。每一个工作流都包含一系列新的 elisp，用于让一切正常工作。

#### 1。“官方”指导: [*使用 org 与杰基尔*在 Worg 上写博客](https://orgmode.org/worg/org-tutorials/org-jekyll.html)

Worg 为使用 org 写博客提供了一些指导。这是许多 Org 博客的非正式起点，描述了几个以 Org 为中心的工作流。

#### 2。 [*使用 org-mode 与杰基尔一起写博客*作者马丁·富特](https://www.mfoot.com/blog/2015/11/17/using-org-mode-to-write-jekyll-blogs/)

这篇文章详述了为 Jekyll 生成 HTML 的 Worg 指南。它使用 HTML 块来导出 Jekyll front matter。它包括 elisp 代码片段，用于设置正确的项目发布。因此，这是新博客的一个很好的起点。

#### 3。 [*使用 org-mode 与莱夫拉兹*](https://levlaz.org/using-org-mode-with-jekyll/)的杰基尔

这篇文章又是从 Worg 指南开始的(和一次使用 Google Docs 的糟糕经历)。他加入了 elisp `org-publish`片段，将草稿从`_org`移至`_posts`。

#### 4。 [*Org-jekyll* 作者胡安·雷耶罗](http://juanreyero.com/open/org-jekyll/)

帖子的第一行提到作者不再使用这种方法。我真的很感激能提前得到这些信息！

post 使用 org-publish 将单个 org 文档中的子树导出为单个帖子。

### 其他平台方法

重要的是要认识到还有其他一些方法。一些用户已经完全脱离了 Jekyll，而新的平台似乎可以写博客了。

#### 1。 [*用 Org 模式写博客*用 Superloopy.io](https://www.superloopy.io/articles/2017/blogging-with-org-mode.html)

方法:

*   最初尝试上游一个 Jekyll 插件。
*   完全远离 Jekyll，现在使用 org-publish

Superloopy 的帖子有很多很棒的想法和对任务的讨论。

首先，我完全同意建立 Jekyll 技术栈是一个巨大的痛苦。GitHub 会自动用 Jekyll 为您发布它们，这在一定程度上缓解了这一问题。同样，令人失望的是 gem/jekyll 插件被忽略了。

这篇文章还描述了一种通过捕获模板降低博客门槛的方法(看起来很棒)。这篇文章还包括直接从 org 发布和将现有的 jekyll-markdown 文章转换回 org 的代码。

#### 2。[T3【吉成野村/org-octo press】GitHub 上的](https://github.com/yoshinari-nomura/org-octopress)

org-octopress 包包含 octopress 和 jekyll 的导出器。这些出口商有一些我想要的功能:

*   支持自动添加 yaml frontmatter
*   在 frontmatter 中设置布局(使用默认值)
*   一些文件名和日期功能

这个包仍然输出到 HTML，而不是我想要的 markdown。但是，如果我可以很容易地将它融入我的工作流程，使用它可能比开发一个新工具更有意义。

## 未来的工作

在调查开始时，我认为已经有了一个`ox-jekyll`解决方案。相反，我还没有找到一个构建在 markdown 和原生 Jekyll 解析之上的。

我将不得不发展我自己的。这个导出器将不得不完全适应现有的工作流(如果我想让它对其他人有用的话)。关于项目和组织文件的设置，我有一些假设，我必须小心谨慎或清楚地记录下来。

## 结论

我从这篇评论中学到了很多，包括一些对我写博客有用的东西:

*   我应该确保我的博客有一个标题。我一直离线写这篇文章，相关网页已经打开。如果我在我自己的博客上看一篇文章，我在页面上将没有上下文/元信息。
*   在文章开始时更新内容是对读者的一种礼遇。我必须为我自己的职位这样做。

有一点很重要:有很多人使用 Emacs 和 Org 来写博客(写作和工作)。有文化的编程的影响意味着许多人会写关于他们工作流程的博客文章。由于这个项目，我已经在我的 RSS 订阅中添加了大约 5 个博客。