# Git 提交:有效的风格指南

> 原文：<https://dev.to/pavlosisaris/git-commits-an-effective-style-guide-2kkn>

通过 [xkcd](https://xkcd.com/) 提供的帖子封面图片

Git 提交是 Git 最被低估的特性之一。停下来想一想；

1.  你上一次不得不将代码恢复到更早的时间点是什么时候？

2.  你是否很难找到那个打乱了你代码的提交？

3.  那是因为不清楚的、混合的提交信息吗？

4.  当找到那个提交时，您是否也意识到在那个特定的提交中包含了几个文件更改，导致在尝试返回到那个提交时令人头痛？

**祝贺您**，您刚刚使 Git 最强大的功能之一失效:

[代码版本&历史！](https://git-scm.com/docs/git-reset)

## 保持整洁的重要性

作为一名开发人员，您可能会遇到的最困难的任务之一就是沟通。与其他开发人员、客户、经理沟通，也与**未来的你**沟通。

拥有一个干净的 git 提交消息历史意味着在将来的任何时候你都可以修改你在代码中修改了什么，删除了哪些功能部分，修复了哪些 bug。

通过运行`git log`,你将能够看到一个逻辑消息流，它单独解释了应用程序的整个代码历史。
你也可以运行`git log --pretty=oneline`来查看更密集版本的`git log`。

## 如何组织提交消息

如果你使用类似于[吉拉](https://www.atlassian.com/software/jira)、[阿萨纳](https://asana.com/)、[特雷罗](https://trello.com/)或其他的项目管理工具，并遵循类似于 [Scrum](https://en.wikipedia.org/wiki/Scrum_(software_development)) 或[看板](https://en.wikipedia.org/wiki/Kanban_(development))的任务分解项目管理流程，你会把所有的任务都放在一个地方，整齐有序。

您的目标应该是让每个提交描述一个相应的子任务。不再有`git commit -m "Added code"`、`git commit -m "Added CSS"`或`git commit -m "bugfix"`。
这些提交是无法追踪的，你将**永远**无法在未来使用它们。

那么，为什么首先要有它们呢？

当你已经修改了一堆类，准备提交代码的时候，花点时间思考一下；

1.  您在每个文件中更改了什么？哪些类与您工作的子任务功能相关？

2.  你的应用程序是如何受到影响的，你对代码做了哪些突破性的改变？

## 遵循简洁的信息风格

通过选择一种风格并坚持下去，你就把你的代码带到了另一个层次。

其他开发人员将能够像阅读小说一样阅读您的消息历史，他们可能永远也不需要查看实际的代码(这将节省他们很多时间)。

你还会强迫自己**去做重要的事情，而不是当下方便的事情**。

## 最后，风格指南

### 消息结构

一个干净的提交消息应该由三个不同的部分组成，用一个空行隔开:标题(描述代码更改的类型)，以及一个描述性的正文，其中包含关于任务跟踪或问题跟踪 id(页脚)的可选消息。

布局应该是这样的:

```
commit type

body (changes, links, attributions, etc)

issue tracker id (for reference) 
```

### 提交类型

它可以是提交内容的缩写。例如(摘自 [Udacity 的提交风格指南](http://udacity.github.io/git-styleguide/)):

1.  专长:一个新功能
2.  修复:错误修复
3.  文档:文档的更改
4.  样式:格式、缺少分号等；没有代码更改
5.  重构:重构生产代码
6.  测试:添加测试，重构测试；没有生产代码变更
7.  杂务:更新构建任务、包管理器配置等；没有生产代码变更

### 正文

提交对代码所做的任何更改。它可以是对添加的功能的描述，对完成的任务的描述，或者是 bug 重现和解决的步骤。

在构成正文的时候，尽量做到详细。我认为两年后你很有可能会读到这封信。你想和未来的自己交流什么？您可以添加要点、列表、指向 Stackoverflow 答案的链接以及指向您的问题跟踪器的链接。

## 结论

能够编写有意义的 Git 提交不仅会让你受到其他开发人员的喜爱，他们也会阅读你的提交。它还会让你成为一名更好的作家，更善于分析，更面向干净的代码。

想想你未来的自己。写更好的提交，今天！

参考资料:
[Udacity Git 提交消息风格指南](http://udacity.github.io/git-styleguide/)