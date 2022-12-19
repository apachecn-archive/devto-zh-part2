# 使用文本编辑器进行任务跟踪

> 原文：<https://dev.to/luke/using-a-text-editor-for-task-tracking-ckf>

我发现跟踪任务非常有用，不管是工作、生活管理还是购物，把它放在一个地方跟踪会有很大帮助。

最近我放弃了 iPhone，转而使用 Android，我失去了我最喜欢的待办事项应用程序 [Clear](https://itunes.apple.com/us/app/clear-todos/id493136154) 。直到我做出我的替代品(我的第一个安卓应用！)我一直在用我的文本编辑器凑合着让事情变得简单。

## 简单任务跟踪

首先做一个`log`目录保存任务文件，一个`notes`目录做项目笔记。

将所有与开发相关的东西放在同一个地方也有助于查找项目、笔记、日志等。快点。下面是一个目录结构的例子:

```
ls ~/dev
 log
 notes
 projects
 snippets 
```

Enter fullscreen mode Exit fullscreen mode

*   `log`:针对任务档案
*   `notes`:关于项目的笔记，关于任务的深度笔记等。
*   `projects`:用于个人项目或远程拉取 git repos 的代码
*   `snippets`:不属于项目的奇数位代码

### 创建任务文件

通过使用您的编辑器创建一个新文件，将其命名为`month-year.md`，例如`oct-2018.md`。

```
# Sunday 7th October 2018 
```

Enter fullscreen mode Exit fullscreen mode

虽然有点多余，但我发现按照任务完成(而不是创建)的日期来组织任务非常有用。重要的是，未完成的任务会留到第二天，给人一种包袱的感觉——一种尽快完成任务的动力。

约会结束后，写一点关于这一天的介绍就像是一个个人激励/抱怨部分，可以快速简洁地收集想法。提及总体目标，但不要结构化。保持简短！

```
# Sunday 7th October 2018

Feeling positive about getting this stuff done this 
evening. Hoping to share my task-tracking habits with 
others.
 - [ ] Write a blog post about task tracking
 - [ ] Take a look at a personal project 
```

Enter fullscreen mode Exit fullscreen mode

### 完成任务

我不确定 Markdown 是最好的格式，但是到目前为止，我发现它非常直观和简单，但是足够表达我的需求。当我想编写工具来简化这个过程时，它也是模糊的未来证明。

Markdown(或者至少是 GitHub 风格)允许的重要功能是将任务标记为已完成:

```
 - [x] Start writing a blog post
 - [ ] Publish blog post 
```

Enter fullscreen mode Exit fullscreen mode

### 基于时间的日志

最近，我也开始在我的日志中添加时间戳，这样我的想法就更加集中了。我认为这里的用处在于写作，而不是读回我的想法，但我偶尔会发现看到我昨天或上周所做事情的更详细的视图是有用的(特别是对于回顾 sprint 回顾中的痛苦/快乐点)。

```
 # Thursday 11th October 2018
 + 22:18 getting pretty sleepy, might try to finish 
         this blog before nodding off
 - [ ] Finish this blog post
 - [ ] Sleep 
```

Enter fullscreen mode Exit fullscreen mode

### 未完成任务

有时，分配给一项任务的时间被低估了，有必要计划以后再做这项任务。为此，我将任务从昨天转移到今天。

执行这种文本移动的物理行为不知何故建立了一种包袱感，我发现它阻止了我计划在一天中做太多事情。

### 计划任务

有时候，任务不能在今天完成，只能在未来完成。对于这些任务，只需在任务文件的底部堆积一个日期标题列表，准备在那一天到来时完成。如果一项任务有相关的时间(例如，安排在白天的会议)，我发现包含符号`@ 10:30`或类似的符号很有用。

```
 # Thursday 11th October 2018

## Log
 + 10:00 forgot to get milk yesterday!! Looking forward to
         banana bread on Saturday though

Kitchen
 - [ ] Buy milk

# Saturday 13th October 2018
 - [ ] Meet with friends @ 14:00
 - [ ] Get ingredients for banana bread 
```

Enter fullscreen mode Exit fullscreen mode

### 任务类别

我发现有用的一点是将标题放在相关任务组的上方。这有利于将家庭任务与工作任务分开，例如将“管理”任务与休闲任务分开。

```
Me
 - [ ] Netflix
 - [ ] Chocolate
Admin
 - [ ] Setup password manager 
```

Enter fullscreen mode Exit fullscreen mode

(“我”这个类别是我个人最喜欢的。记住给自己“我”的时间应该是你日常生活的一部分。)

## 前期工作

我会给你们留下一些我想看一看的前期工作。 [Org mode](https://orgmode.org/worg/org-tutorials/tracking-habits.html) 看起来是运行 emacs 的 hax0rs 的任务跟踪。

以前已经有人做过纯文本任务跟踪，我鼓励读者考虑他们自己的系统。

感谢阅读，关注我了解更多！🚀