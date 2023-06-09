# 组织模式标记

> 原文：<https://dev.to/thagomizer/org-mode-tagging-28hh>

今年我大约每周进行一次采访。这不是不可能完成的采访任务，但也不是无关紧要的。对我来说，面试最难的部分是提前准备问题。制定一个计划并不是绝对必要的，但是我发现它帮助我自信我达到了所有我需要达到的目标。事先准备还能让我根据候选人和角色量身定制问题。然而，我不想问每个候选人完全不同的问题，我也不想在面试前一个小时临时抱佛脚的时候想问题。在过去，我用以前的面试笔记来提出问题，但即使是 grep 也很乏味。幸运的是，`org-mode`提供了一个简洁的解决方案。

## 标注

Org-mode 支持标题上的任意标签。如果您使用 Org 来跟踪任务，那么您可以将项目标记为“工作”或“电话”。标签位于行尾，由冒号包围。您可以使用`C-c C-C`在迷你缓冲区中输入标签，但大多数时候我都是用冒号将标签括起来。

```
* Buy cat food :store:
* Water green beans :garden:
* Eat extra cookies :work:lunch: 
```

您也可以使用以下语法提供文件级标签:

```
#+FILETAGS: :work:supersecret:

* Come up with an awesome code name 
* Write awesome codes :computer: 
```

一开始我发现标签继承是不直观的。文件级标签适用于文件中的每个标题/项目符号。所以上例中的两个标题都有“工作”和“超级机密”标签，尽管它们不在那一行。第二个标题也有标签“计算机”

一旦开始使用标签，您就可以使用标签搜索。`C-c \`搜索当前缓冲区，并突出显示标签与您的搜索相匹配的标题。标签搜索支持基本的布尔搜索，用`&`表示 and，`|`表示 or，`+`和`-`表示必需的或排除的匹配。您可以组合和嵌套操作符，这让您可以做类似于`store|home-garden`的事情。该查询列出了所有标记为 store 或 home 的项目，但如果匹配 home 的项目也标记为 garden，则丢弃这些项目。

要搜索多个组织模式文件，您需要将它们包含在为[议程](https://www.gnu.org/software/emacs/manual/html_node/org/Agenda-Views.html)搜索的文件中。为此，您必须修改`org-agenda-files`变量。我发现这一部分令人困惑，因为对我来说，修改“议程”来搜索不包含任何任务的多个文件是没有意义的，但 org 有时很奇怪。一旦您配置了搜索路径，您就可以使用`C-c a m`在所有文件中搜索匹配的标题。整个标签系统非常强大，我在这里只介绍了最基础的部分。

## 使用标签进行面试

我在面试中有两种使用标签的方式。首先，我有一个`questions.org`文件，里面列出了我想问的所有问题。我把每个问题都标上了要评估的类别(技术、开发者关系、领导能力等)。)和潜在的二级标签，如用于应用程序开发的“appdev”或用于更直接问题的“warmup”。这是我的问题文件中的一小段。

```
* What interests you about developer relations? :devrel:
* What is your favorite language right now? What do you like about it? :general:warmup:
* Tell me about a time when you had to push through a contentious technical change. :leadership:
* What is serverless? :serverless:appdev:technical: 
```

当我准备面试时，我首先为面试创建一个新的 org 文件。然后，我使用“热身”或“开发工具”等查询搜索我的问题文件，以找到可能相关的问题。一旦我找到一个我想用的问题，我就把它复制到 org 文件中，用于这次面试。

面试结束后，我用几个文件级标签标记面试专用文件。通常是职位阶梯，一个基于我的等级推荐的大概等级，可能还有应聘者的特长。这个习惯给我留下了许多包含一系列问题和一组标题标签的文件，如下所示:

```
#+FILETAGS: :DA:L4:L5:language 
```

我使用文件级标签来查看我之前针对该角色提出的问题的确切列表。如果我面试一个语言 DA，我会搜索语言&DA。对于常见的职位，我使用这种快捷方式，因为它节省时间，并确保我问每个候选人大致相同的问题。我仍然会花一些时间根据候选人的简历或开源工作来调整问题，但我发现能够非常快速地获得准确的问题列表可以节省大量时间。

## 结论

我很确定这不是 org 标记的预期用途，但是它很适合我的目的。当我想到新的问题(或对现有问题进行扩展)时，我会将它们添加到我的问题库中，以备将来使用。把大量的准备工作转移到我的电脑上，让我可以轻松地参加面试，更专注于求职体验。另外，[我们正在招聘](https://careers.google.com/jobs#t=sq&q=j&li=20&l=false&jlo=en-US&jcoid=7c8c6665-81cf-4e11-8fc9-ec1d6a69120c&jc=DEVELOPER_RELATIONS&j=developer&)。