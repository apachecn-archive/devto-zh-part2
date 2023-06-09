# 在 Github 上寻找问题的技巧和窍门

> 原文：<https://dev.to/ignoreintuition/tips-and-tricks-to-finding-issues-to-contribute-to-on-github-2lde>

想开始为开源做贡献吗？这里有一个关于如何在 GitHub 中发现问题的快速入门。

*   前往 [Github 问题页面](https://github.com/issues)。默认情况下，这将向您显示您的未决问题。查看搜索栏，您会看到所有默认的限定符。我的长这样:`is:open is:issue author:ignoreintuition archived:false`

*   创建一组筛选器，将结果限制在您可能感兴趣的项目中。根据您选择的语言搜索未分配、最近更新的问题:`is:open is:issue is:public no:assignee language:javascript archived:false updated:>=2018-06-01`

*   接下来，我们可以将它限制到某些标签或关键字。标签对于寻找适合初学者的问题或者作者积极寻求帮助的地方很有用:`is:open is:issue is:public no:assignee language:javascript archived:false label:"help wanted" updated:>=2018-06-01`

*   最后，我们要对结果进行排序。您可以根据交互次数或创作时间进行排序:`is:open is:issue is:public no:assignee language:javascript archived:false label:"help wanted" updated:>=2018-06-01 sort:interactions-desc`

所有限定符的文档可以在[这里](https://help.github.com/articles/searching-issues-and-pull-requests/)找到。希望这能帮助你找到可以解决的问题，并让你开始着手一些很酷的新开源项目。