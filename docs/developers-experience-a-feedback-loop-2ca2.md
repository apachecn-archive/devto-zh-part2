# 开发者体验:反馈循环

> 原文：<https://dev.to/stereobooster/developers-experience-a-feedback-loop-2ca2>

这是一个非常重要的指标。改变后你浪费了多少时间等待回应。例如，您更改了代码，您需要等待多长时间来查看是否有类型错误，是否有 lint 错误(linter)，是否测试失败，等待浏览器重新加载，等待服务器重新加载，等待 UI 重绘，等待编译，等等。

可以有不同类型的速度(不是研究数据，如果你对这个主题有研究，请给我指出正确的方向):

*   毫秒-理想的反应。你不会注意到的。例如，OCaml 重新编译。
*   秒——慢到足以注意，慢到不足以分心。你最终会生气的
*   几分钟-慢到让人分心。你很可能会将上下文切换到其他事情上，比如阅读博客或与同事交谈，当它结束很长时间后，你会回到它。

如果反馈循环，例如，运行大型应用程序测试套件需要 30 分钟(😱是的，这可能发生)这意味着你每天只有 16 个循环。你知道这个关于 10x 开发人员的讨论，这可能是因为他们知道如何最小化反馈循环。请看贾斯汀·希尔斯的演讲“[如何停止讨厌你的测试](https://www.youtube.com/watch?v=aiDikM9g4x8)”，他解释得很好。destroyall 软件的幕后人员 Gary Bernhardt 也一直在谈论反馈回路的[重要性](https://twitter.com/garybernhardt/status/1047971335069032448)。

正如你所猜测的，这是一个困难的问题，或者可能并不困难，但优先级最低(如果开发人员遵循[“让它工作，让它正确，让它快速”](http://wiki.c2.com/?MakeItWorkMakeItRightMakeItFast)原则)。

### (模块热)重新加载

**热(模块)重新加载**——一种流行的解决方法是保持系统运行并只重新加载部分系统，这种方法的缺点是一些状态可能会堆叠，直到完全重新启动(缓存失效是一个棘手的问题)，你才能从中脱身。他们在 Rails 中为这个问题奋斗了很长时间，至少有三次尝试来解决它:[斯波克](https://github.com/sporkrb/spork)、[宙斯](https://github.com/burke/zeus)、[弹簧](https://github.com/rails/spring)。据我所知，仍有一些细微的错误时有发生。 [react-hot-loader](https://github.com/gaearon/react-hot-loader) 和[流服务器](https://flow.org)也存在这些问题。只有当你不得不处理易变的事情时，这个问题才是困难的，否则这个可解决的任务，例如在 Erlang 中你可以在生产中重新加载系统。

### 保存状态

除了热重新加载之外，你还可以通过提取状态并保持其他代码尽可能“纯净”来在重新加载之间保留状态。这就是他们在 Redux 中所做的事情(其中一个例子)，所以当应用程序重新加载时(假设除了 store 之外的东西被重新加载)，它将处于与以前相同的状态，所以您可以恢复与系统的交互，而不需要重新创建状态。

### 观看模式

相关但不一定相同的技巧是**监视模式**，例如每次磁盘上的文件改变时重新运行命令。这节省了几毫秒。这种技术可以和第一种结合起来。

### 通知

如果除了运行长命令别无选择，可以在操作结束时发送通知。参见 [lmk](https://github.com/fgrehm/lmk) 和 [undistract-me](https://github.com/jml/undistract-me) 。

### TDD 特定接收方

*   比起集成测试，更喜欢单元测试
*   模拟 IO 操作，比如网络调用
*   确保测试是独立的，这样你可以并行运行它

### 立即响应

在窗口或终端标签之间切换也有助于反馈回路。在 IDE 中显示测试或类型检查的结果很好，不需要切换到不同的窗口，也不需要显式运行一些命令。例如，在 [vscode-jest](https://github.com/jest-community/vscode-jest) 中，它们内联显示每个测试的状态

[![](../Images/fc353d784e087d1457833b2e24abde1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0ytQE-nj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2xh5hp6e8h77yn09nj25.jpg)

例如，Chrome Developer tools 会在您按 enter 键之前计算表达式

[![](../Images/dfc6ba731fab18ca574a5128215ced23.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DTm8Ggmq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dhuh7e8ici2ujhuh2rib.png)

*奥利弗·黑尔在 Unsplash 上拍摄的照片*