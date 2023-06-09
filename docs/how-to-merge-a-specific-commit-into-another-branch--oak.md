# 如何将特定的提交合并到另一个分支中

> 原文：<https://dev.to/iamafro/how-to-merge-a-specific-commit-into-another-branch--oak>

我真的希望这个标题是不言自明的，因为这是我能想到的最好的。我知道我们都很忙，所以我就开门见山了。

所以上周，我一直在为一个项目开发这个新功能。像往常一样，我在开发部门工作，昨天我们发现了一个需要立即修复的小错误。

在修复了错误之后，我意识到我已经在上周工作的同一个分支上做了这个。我不能把这根树枝推给主人，因为一切都会折断。

我谷歌了一下，找到了一些有用的东西，但没有解决我的问题。以下是我的发现:

首先，签出您想要将提交合并到其中的分支

`git checkout <the branch>`。

然后

`git cherry-pick <the branch you've been working on>`。

这样做的是，[“它在工作分支的顶端应用提交引入的更改，并使用此更改创建一个新的提交”](https://git-scm.com/docs/git-cherry-pick#git-cherry-pick-codegitcherry-pickmastercode)。我遇到的问题是，我已经提交了几次，我需要主分支中的所有这些提交。那时我发现了它。

我发现了什么？你可能会问。是我发现的。如果你使用 VScode，你可能知道这个扩展。我安装它已经有一段时间了，但是我从来没有真正使用过它。

有了这个扩展，你所需要做的就是，在 gitlens 区域点击分支下拉菜单，然后点击你提交的分支的下拉菜单，然后右击一个提交并挑选它。别担心我在那里输入的那些胡言乱语。我附上了一张截图。(我很好，我知道)。

[![gitlens screenshot](img/f02d900a932f4fb3f00a9de4c527b05c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8qthykM6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/hackafro/image/upload/v1518728407/Screenshot_9_ip7xnw.png)

当您查看您的终端时，您将看到这个命令正在运行

`git cherry-pick -e <the commit hash>`

运行该命令后，您可以推送您的更改。你可以下载 gitlens 扩展来使用这个特性，或者如果你还没有 VScode，那么有一个终端命令你。每个人都有一个命令。

我答应长话短说，所以再见！我希望这有所帮助。

[![bye!!](img/14dd61394b87b2b73af6aae726457550.png)T2】](https://i.giphy.com/media/l49FqlUguNsGDNCGk/giphy.gif)