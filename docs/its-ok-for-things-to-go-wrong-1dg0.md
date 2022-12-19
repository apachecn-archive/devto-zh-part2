# 事情出错是正常的

> 原文：<https://dev.to/matmooredev/its-ok-for-things-to-go-wrong-1dg0>

直到最近，我一直在英国政府数字服务部门的 GOV.UK 团队工作。这个团队真正擅长的一件事是对事件做出反应，并从中吸取教训。

我最近一直在思考这个问题，因为我的新团队仍然在正式确定如何应对事件，因此当事情真的出错时，会变得更加混乱。但不一定要这样。

我认为大多数软件团队将受益于将事件视为偶尔发生的正常事情，而不是一些可怕和糟糕的事情，不惜一切代价避免。

[![Dog drinking coffee in burning house: "this is fine"](../Images/02dfd058bfbe845fc7fe95041e6b0bfc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5jhSKKnd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vvfd60qo94wpoaxc15cv.png)

## 发生事故时，你应该怎么做？

事件可以是对人们关心的服务的任何意外中断。例如:

*   该服务完全不可用
*   有些东西慢得令人无法接受
*   功能被破坏的方式阻止了人们使用它
*   您发现了一个安全漏洞或漏洞

在事故中，你不需要总是了解根本原因。目标是尽快恢复正常服务，并向受影响的人员(和管理层)传达正在发生的事情。

然而，你希望以后能够理解根本原因，所以你应该记录你采取的行动和你观察到的事情。没有这一点，就不可能理解发生了什么，特别是如果你采取的一些行动实际上使事情变得更糟(这是可能发生的)。所以把事情写下来。

## 不要惊慌

一个人很容易忽略事件中的重要细节。理想情况下，应该有不止一个人回答，这样你们就可以讨论下一步该做什么。另一方面，让多个人挤在一张桌子周围(或发送一个空闲频道)会增加回应者的压力，而实际上不会帮助他们。如果发生这种情况，指定一个人来领导该事件，并让他们在需要时请求帮助。

通信部分的重要性取决于对用户的影响。如果 GOV.UK 网站瘫痪，这就阻止了对政府信息和服务的访问，人们会注意到。所以他们总是指派一个人来更新[状态页面](https://status.publishing.service.gov.uk/)，并回答那些没有积极解决事件的人的问题。

## 事发后进行尸检

事件发生后，你应该让房间里的每个人谈论发生了什么以及为什么会这样。这是事后分析(又名事件回顾)。

以下是尸检的大致日程:

1.  提醒大家验尸是如何进行的
2.  让某人读出事件发生的时间表(如果你在事件发生时没有时间表，在会议前写下来，这样人们可以提前回顾)
3.  读出关键细节:影响是什么，恢复服务需要多长时间，以及是否需要任何进一步的调查或修复
4.  讨论原因和做出的关键决定，并写下你的任何建议
5.  决定哪些建议值得做，以及谁应该实现它们

你可以边走边写一份验尸报告。如果你捕捉到了事件发生时的时间线，你所要做的就是写出你的结论和建议。

## 无可指责的验尸是有效的验尸

在这些会议中，抓住一个根本原因并专注于此是非常诱人的，但这过于简单化了。一个事件通常涉及复杂的软件和人员系统，并且有许多因素会影响它的发展。

我们在会议室的墙上贴了一个叫做[回顾主要指令](http://retrospectivewiki.org/index.php?title=The_Prime_Directive)的东西，上面写着:

> 不管我们发现了什么，我们理解并真诚地相信每个人都尽了最大努力，考虑到他们当时所知道的、他们的技能和能力、可用的资源以及手头的情况。

有些人对此不屑一顾，因为这描绘了一幅每个人都一直完美的美好画面，但这并不是重点。事后分析的目的是从发生的事情中吸取教训，避免同样的问题再次发生。指责是一种非常无效的方式，因为它鼓励人们隐藏他们的错误，而不是解决它们。

回顾一件事并假设结果是显而易见的也是很容易的，而实际上当时并不是这样(这被称为事后诸葛亮)。因此，假设人们以合理的方式做出反应，并思考他们在做决定时可以获得哪些信息和工具，会有所帮助。

我参加过一些邀请高级利益相关者参加的事件审查。在我看来，这是一个非常糟糕的主意。当存在权力差异时，很难让会议按计划进行。团队成员很难承认错误，利益相关者的试探性问题可以很容易地将谈话引回到近因。如果利益相关者对某个特定的领域负责，他们会专注于此，而让团队考虑更大的图景会更加困难。

## 验尸时问的好问题

良好的会议卫生非常重要。你应该总是指定一个人做笔记，另一个人确保会议顺利进行。

其中一部分是鼓励人们使用精确的语言，并区分观察和事后的解释。

这里有一些提示，你可以用来探究到底发生了什么以及为什么。这些来自西德尼·德克尔的《理解人为错误的实地指南》(也有 T2 的印刷版本)。

### 暗示

*   你关注的是什么？
*   你看到了什么？
*   你期待会发生什么？

### 释义

*   如果你必须向你的同事描述当时的情况，你会告诉他们什么？

### 错误

*   在这一点上可能会出现什么错误(例如在翻译中)？

### 以前的知识/经验

*   你想起以前的经历了吗？
*   这种情况符合标准场景吗？
*   你受过处理这种情况的训练吗？
*   这里有什么明确适用的规则吗？
*   有没有其他知识来源建议该怎么做？

### 目标

*   你想达到什么目的？
*   同时有多个目标吗？
*   你能做的事情有时间压力或其他限制吗？

### 采取行动

*   你如何判断你可以影响事件的进程？
*   你是否讨论过或者在脑海中想象过一些选择，或者你是否立刻知道该怎么做？

### 胜负

*   结果符合你的期望吗？
*   你必须更新你对形势的评估吗？

### 通讯

*   你更喜欢使用什么沟通媒介？(电话、聊天、电子邮件、视频会议等。？)
*   你是否同时利用了一个以上的沟通渠道？

### 帮助

*   你向任何人求助了吗？
*   是什么信号让你寻求支持或帮助？
*   你能联系到你需要联系的人吗？

## 示例模板

[更新时间:2020 年 9 月]

这是我和另一个团队用来在 github 存储库中记录事件和事后细节的模板。这是基于贝齐·拜尔、克里斯·琼斯、詹妮弗·佩托夫和尼尔·理查德·墨菲的[站点可靠性工程中的一个例子。](https://landing.google.com/sre/book/chapters/postmortem.html) 

```
# Title (incident #)

### Linked tickets
Include here any tickets/pull requests related to the incident or its follow up actions.

### Summary
Explain what happened in one or two sentences.

### Impact
How did this affect the users of the service? Quantify the impact if possible.

### Detection
How did the problem get noticed?

If it was raised through a ticket, what level was it reported as (low/medium/high impact)?

### Resolution
How did we restore service?

### Contributing factors
Discuss this in an incident review meeting. The goal is to identify systemic problems, so you can then generate action items which will make the system safer.

Do not stop at a single root case. When things go wrong there are usually multiple points where an intervation could have averted or mitigated the incident.

## Action Items

- To be agreed

## Lessons Learned

### What went well

- Something good

### What went wrong
- Something bad

### Where we got lucky
- Something fortunate

## Timeline
All times are assumed to be local time unless specified otherwise.

| Time | Event |
| -----|-------|
| YYYY-mm-dd HH:MM:SS | Something happened | 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

每个人都接受某种程度的风险，以便将软件推出市场，所以当事故真的发生时，我们不应该恐慌。当事情出错时，我们能做的最好的事情就是从中吸取教训，我认为无可指责的事后分析是我们改进做事方式的最好工具之一。如果没有这种持续的反馈和改进，GOV.UK 就不会像现在这样有弹性。

我认为你也可以从阅读其他人的事件报告中学到很多东西(见本[尸检集](https://github.com/danluu/post-mortems)中一些有趣的)。

你如何处理工作中的突发事件？