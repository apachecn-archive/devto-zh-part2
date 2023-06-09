# 博客文章:为什么以及如何写一篇好的改变日志

> 原文：<https://dev.to/jonasbn/why-and-how-should-you-write-a-good-change-log-4kp0>

在这篇博文中，我将概述我是如何看待变更日志的，以及它们应该如何和为什么被编写和结构化。请注意，这是基于我的经验和观点，而不是一套*最佳*实践，仅仅是我在使用软件和开源软件的工作中消费和产生变更日志的实践。

当你做一个软件项目时，无论大小，写一个变更日志是一个好主意。变更日志通常显示在伴随脚本、库或应用程序的单个文本文件中，这是软件开发或项目的中心。

当通过书面文本交流时，有几个重要的关键点必须牢记在心，以便成功地获得想要的信息。

*   谁是这篇文章的(预期)读者？
*   **为什么**观众在读课文？

这些都是很基本的问题，答案似乎很直接，但是当你写文章时，你记得问自己这两个重要的问题吗？尤其是您的变更日志。

因此，让我们尝试设置写入更改日志的设置。实际上，我们可以用一个*故事*的格式来描述这一点，将其描述为一个基本用例。

> 我的软件的一个用户正在阅读我的变更日志，以查看早先
> 报告的一个错误是否已经被解决

我们之所以要如此具体，是为了理解变更日志的作用及其重要性。

变更日志放置了一个特定的角色，这使它与其他文档区别开来。主要文档描述了软件的理想使用，可能有一些限制，可能是指待办事项列表、教程或其他内容，但软件的主要用例在其他地方有所描述，并且经常随软件一起更新，这意味着它不记录更改，而是记录当前状态，可能是从以前状态的迁移，或者它分别传达对现有功能和新功能的弃用或引入。变更日志扮演着一个单一的，但是非常重要的角色，向软件传达变更。

深入结构化文档的细节以及与此相关的交流任务超出了这篇博文的范围，也许应该以同样的方式来审视它，我确实有一些关于文档的经验和观点，但是让我们紧扣主题。

主要文档适用于以下用例:

*   找出软件是做什么的，甚至是它是如何做到的
*   它是如何安装、配置、使用甚至故障排除的
*   如何卸载它
*   也许如何获得支持或联系作者

但是，一切都与软件的当前版本密切相关，并且可能与迁移和弃用信息有关。

让我们检查另一个用例。

> 我的软件的一个用户发现了一个问题，这个问题指向我的软件。
> 不是倾倒超过 1000 行代码，而是浏览变更日志
> ,看看是否有任何列出的变更可能是问题的原因

这导致了另一个通信关键点，它可能与软件使用情况非常相关，但也可能与在紧急状态下使用的文本通信的其他部分相关。

*   **什么时候**观众在读课文？

有人正在努力工作，也许是加班，由于软件问题或类似的问题，他们处于压力之下，已经很晚了。他们会遇到与我们刚刚描述的用例类似的问题，并且必须尽快找出问题的原因。如果涉及您的软件的更新是他们问题的原因，他们必须决定从别处寻找，实施解决方案或回滚。

这时变更日志就派上用场了。

更改日志是对更改的简短描述，遵循某种标准布局以便于使用。

现在让我们进入你应该如何组织你的变更日志，以一种好的方式适应这些用例。

## 添加标题

文件名相当模糊，所以在文件顶部添加一个标题

`Change log for ApplicationX`

## 条目应组织成一个标题，并附有一个子条目列表

条目应该以所描述的版本的指示开始，这个例子使用语义版本化( [semver](https://semver.org/) )，我将回到那个，然后是每个变更的列表项。

```
1.0.1
- Fixed bug in parser 
```

Enter fullscreen mode Exit fullscreen mode

## 按逆年表排序条目

常规的用例是发现最近的更改，因此随着时间的推移，历史更改变得越来越不重要，向文档的末尾漂移。

```
1.0.1
- Fixed bug in parser

1.0.0
- Initial release 
```

Enter fullscreen mode Exit fullscreen mode

## 构造一个发布条目

发布条目应该以统一的方式构建。

它至少应包括:

*   版本号
*   时间戳

是否使用语义版本取决于您。但是需要一个可识别的版本化方案，它能够唯一地识别每个版本。

```
1.0.1 2018-09-27
- Fixed bug in parser

1.0.0 2018-09-27
- Initial release 
```

Enter fullscreen mode Exit fullscreen mode

我看到过带有时区的时间戳和小时、分钟和秒的分辨率，这可能是不必要的，因为发布可能不会像部署它的实际操作一样产生相同的影响。所以这个额外的信息有点过分了。

与日期格式一致，推荐使用 [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) 日期格式。

我发现一些非常有益的额外元数据是一个版本类型指示:

*   功能发布
*   维护版本
*   Bug 修复发布

这些并不直接映射到语义版本化方案，但是它们提供了一些关于如何发布的信息。

```
1.0.2 2018-09-27 Maintenance release
- gitignore updated with tmp directory

1.0.1 2018-09-27 Bug fix release
- Fixed bug in parser

1.0.0 2018-09-02 Feature release
- Initial release 
```

Enter fullscreen mode Exit fullscreen mode

用户可以查看发布说明，判断功能发布中包含的功能是否对他们有用，或者是否可以暂时跳过。

如果它只是一个维护版本，解决了元数据或发布信息中的小问题(相信我，这些年来我已经做了很多)，那么现在可以跳过它。

最后，错误修复版本表明，没有添加任何新内容，向后兼容性完好无损，错误已得到解决，对于希望了解其问题是否已解决的观众来说，此信息很有价值。

一些 bug 修复可能是重要的，因此提示用户对该版本的立场是很重要的。

我用这两个:

*   建议更新
*   不需要更新

```
1.0.2 2018-09-27 Maintenance release, Update not required
- gitignore updated with tmp directory

1.0.1 2018-09-27 Bug fix release, Update recommended
- Fixed bug in parser

1.0.0 2018-09-02 Feature release
- Initial release 
```

Enter fullscreen mode Exit fullscreen mode

## 结构变更项条目

现在我们已经有了我们的发布条目结构，现在让我们看看变更条目。

发布中的单个项目应该被描述以供人们使用，并且每个变更集应该用单个条目来描述。

我看到了条目的分组:

*   新增功能
*   错误修复

是的，这应该是整个变更条目结构的一部分，但是因为我的目标是保持我的版本小，或者被分类为 bug 修正或者特性版本，我试着不把它们混合在一个版本中。

如果您制作了包含几个变更的较大版本，例如主要版本，这种分离会非常有用，例如，如果您运行常规的预定版本..

```
2.0.0 2018-09-27 Feature release, Update recommended

Feature additions:
- Added color selector
- Added color profile exporter

Bug fixes:
- Addressed issue with start up time 
```

Enter fullscreen mode Exit fullscreen mode

为了不必重写变更日志中的所有内容，添加链接和引用。

```
1.09 2018-05-30 Bug fix release, update not required

- Based on issue #21 several issues with the test suite was spotted
  and corrected, at the same time there was created issues for
  implementation of adapters for SK and NZ. An issue with ES was also
  created since this distribution seems to rely on Date::Holidays,
  which does not seem to make sense.

  Ref: https://github.com/jonasbn/Date-Holidays/issues/21 
```

Enter fullscreen mode Exit fullscreen mode

请用被动语态，不要用主动语态。我已经见过很多次了，人们直接从待办事项列表或问题跟踪器中复制。这也可以在提交日志中经常观察到——如果你问我的话，这只是简单的*草率的*。

示例:

在您的问题跟踪器中，您有以下任务:

*“添加颜色选择器”*

这表明有些事情必须完成。

在您的变更日志中，您可以添加:

*“增加颜色选择器”*

这表明某事已经完成。

这些是我对基本变更日志的建议，现在让我接触一些相关信息。

## 自动生成变更日志

我见过几次从提交日志或类似的日志自动生成变更日志的尝试。我不推荐这样做。

变更日志扮演了一个重要的角色，它是一个交流的媒介，而不仅仅是一个变更列表。如果你在哪里这样做，我认为应该考虑以下标准。

*   您提交的消息足够吗
*   您是压缩合并还是将变更集作为事务来处理
*   你能从你的版本控制系统( [VCS](https://en.wikipedia.org/wiki/Version_control) )中提取元数据吗

这个*可以完成，上面的两个标准对正常的提交纪律来说都是明智的建议，但是我从来没有见过/做过一个足够成熟或有纪律的项目来做到这一点——不是说我不努力去做，而是说这很难。*

其中一个缺陷是，我不认为变更日志人工制品的生成应该决定你应该工作或处理谁。但是，如果您处于可以自动生成变更日志的情况下，请告诉我，我很乐意听到这个消息。

## 奖励提示:写入减价

这是相当多的一口，写了比预期更长，但我不得不挤在这个奖金信息。

考虑在 **Markdown** 中写下你的变更日志。

*   这使得链接变得更短，这些链接呈现为超文本而不是文本，是的，超链接很酷——谢谢伯纳斯-李先生。
*   如果需要，您可以使用粗体、斜体和代码栏

最后，您可以使用不同的标题组织您的发布条目，次要发布可以包含在主要发布中。

```
## 2.0.0 2018-09-27 Feature release, Update recommended
- Added colour selector

### 1.0.1 2018-09-03 Bug fix release, Update recommended
- Fixed bug in parser

## 1.0.0 2018-09-02 Feature release
- Initial release 
```

Enter fullscreen mode Exit fullscreen mode

感谢您阅读到最后，请评论您的想法/问题等。