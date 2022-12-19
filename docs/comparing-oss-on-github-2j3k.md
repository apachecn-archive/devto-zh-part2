# 比较 GitHub 上的操作系统

> 原文：<https://dev.to/uetchy/comparing-oss-on-github-2j3k>

您正在决定为新开发的应用程序采用哪个开源项目。

这次对你来说有点困难，因为从功能的角度来看，候选人看起来几乎是一样的。

所以让我们从一个不同的角度来深入研究这个问题:贡献者和用户活动。

*   更多的星星、叉子和观察者是一个有活力的项目的良好的重要标志，这表明许多用户参与到项目中来。
*   更多的问题代表好的和坏的迹象，但基本上它表明他们的活跃。
*   在大多数情况下，组织拥有的项目比用户拥有的项目更加稳定和健壮。
*   存储库的大小有复杂的含义，但在实践中，如果两者都试图实现相同的目标，简单的代码比大量的代码更好。

我做了一个简单的工具，让你了解上面的指导方针。

# 比较-github

[![screencast](../Images/3cc6e74fae31635ddf811db373c83606.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b9U3JW_6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/uetchy/compare-github/gh-pages/screencast.gif)

[compare-github](https://github.com/uetchy/compare-github) 是一个简单的终端应用程序，用于探索您的候选人，并将结果汇总到一份漂亮的报告中。

```
npx @compare/github facebook/react vuejs/vue riot/riot 
```

Enter fullscreen mode Exit fullscreen mode

或者使用纱线:

```
yarn global add @compare/github
compare-github facebook/react vuejs/vue riot/riot 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/af13ee69e2b58dcab729419f178b2b38.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1svFu6mV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1xfd1gcrfntpft5bbu5s.png)

您将立即看到每个候选人的 GitHub 活动。它可以帮助你决定采用哪一个库！

热烈欢迎任何意见/想法来改进`compare-github`！