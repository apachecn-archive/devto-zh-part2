# 面向开发者的 Changelog？

> 原文：<https://dev.to/scottharrisondev/changelog-for-developers-8ce>

正如你可能已经看到的，我最近写了一个帖子，询问如何增加一个项目的总线因子。

[![scottharrisondev image](img/4bbe52a8d9cc0c04ba4217533e87fd26.png)](/scottharrisondev) [## 如何增加你的公交系数？

### 斯科特哈里森 19 年 7 月 18 日 11 分钟阅读

#discuss](/scottharrisondev/how-to-increase-your-bus-factor-3dpg)There were some great responses and it prompted my team to adopt a process where when submitting a code review you select all the other developers as reviewers (not every developer has to approve the request though as this could get tiresome). This should help with knowledge sharing across projects and developers within the team.

在一起工作于一个项目的开发团队中，一个常见的相关问题是，当项目中发生变化时，无论是代码、设计还是与 devops 相关的变化，都要让每个人都参与进来。

我想到的一个可能的解决方案是一个变更日志，但是是针对开发者而不是最终用户的。想法是在每个项目的根中有一个 changelog.md 文件，每当一个特性被合并到主分支时，这个文件就会被更新。消息应该简单，但传达任何可能与未来开发人员理解相关的内容。

例如，最近一名开发人员对我们的 docker 设置进行了更改，并更新了 readme.md 以反映这一更改，但是在选择项目添加新功能时，我没有检查 readme.md，我的环境无法工作。与其查看提交历史或打扰另一个开发人员，我可以简单地查看 changelog.md 文件并看到以下内容:

```
31/07/2018
- Now use docker-sync for handling storage in project, details of what you need to change in README.md 
```

Enter fullscreen mode Exit fullscreen mode

这个简单的信息告诉我，在我再次拿起项目并参与之前，我需要知道的一切。这可能被视为提交消息的重复，但我经常发现提交消息提供了关于已更改内容的相当技术性的细节，当您刚刚跳回到项目中时，这并不总是重要的，尤其是如果您没有触及那些提交中的部分，但是用一句话概括一下更高层次的已更改内容会很方便。

一个同事给我看了这个，看起来这和我想的一模一样:[https://keepachangelog.com/en/1.0.0/](https://keepachangelog.com/en/1.0.0/)

你怎么想呢?