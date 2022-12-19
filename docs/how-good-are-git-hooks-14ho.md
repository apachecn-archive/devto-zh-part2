# Git 钩子有多好？

> 原文：<https://dev.to/hector6872/how-good-are-git-hooks-14ho>

## 我试过了[答案会让你震惊](https://venngage.com/blog/7-reasons-why-clicking-this-title-will-prove-why-you-clicked-this-title/)

作为开发人员，我们每天使用 Git(或者至少我们应该)无数次
,因为[我们并不是所有人都是机器](https://media.giphy.com/media/TgHosMP8OADYO6onsC/giphy.gif),我们有时会因为匆忙而犯错误(例如，当它应该去开发时，将提交推给 master)。为了减少这种失误，我们有**Git Hooks**的帮助。

### 什么是 Git 钩子？

Git 钩子是**无扩展的可执行脚本**，你可以把它们放在你的库的钩子文件夹中(默认为*)。git/hooks* )在 git 执行的某些点触发动作。换句话说:我们可以在使用`git`命令之前/之后**自动化**我们一直想要发生的事情。

[![](../Images/78263756fe99b10818f924b4ce225b19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jQbLWwUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/hrules6872/Writings/master/1-git-hooks/art/1.jpeg)

有一个巨大的[钩子类型列表](https://github.com/git/git/blob/master/Documentation/githooks.txt#L42) ⁴你可以 attach⁵脚本，但这里我们将只看到几个。