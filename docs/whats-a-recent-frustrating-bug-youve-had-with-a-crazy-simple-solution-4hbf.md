# 你最近在一个疯狂简单的解决方案中遇到的令人沮丧的错误是什么？

> 原文：<https://dev.to/jsn1nj4/whats-a-recent-frustrating-bug-youve-had-with-a-crazy-simple-solution-4hbf>

今天我花了 1.5 - 2 个小时试图调试 PHPMailer 的一个问题——或者是我的`foreach`循环，或者是一个拼写错误的变量，或者是方法名应该以大写字母开头...？

开玩笑的。现在问题已经解决了，我*今天*至少从*那里学到了*新的东西。

但我相信你对这个问题循环很熟悉。当*被*损坏的东西显然不应该——或者应该——时，诸如此类的问题似乎总是在脑海中凝结在一起？在那一刻，谁知道呢？

今天的问题——**我以为**——是因为某个特定的代码库从 PHP 5.3.x 切换到 5.6.x 引起的(见下面的编辑)。

最终成为罪魁祸首的是在使用全局变量的函数的顶部缺少关键字`global`。这些变量(事实证明是其中的两个)没有在函数中设置。

### 今天吸取的教训

1.  不要自动信任你自己的代码。即使半最近有效，“半最近”也不是“现在”。

2.  更经常地关注语言升级。不同语言版本之间出现的罕见问题仍然会发生，即使它们*很少*。因为不是所有的项目都使用完全相同的特性集，所以寻找版本之间可能的突破性变化是非常重要的。

3.  挥之不去的困惑或许值得探究。在输入了所有这些之后，并且知道变量突然变得不可访问了——并且调查了一会儿——我仍然不能 100%确定是语言升级导致了这个问题；似乎在 PHP 4 中就有了`global`关键字(也许有更多经验的人知道)。

### 回到问题:

有哪些事情让你纠结了一段时间，却难以置信地容易解决？

* * *

附注:我知道从 5.3.x 升级到 5.6.x 是相当“落后于时代”的，但这恰好是我们使用的 VPS 的情况。不过，我确实看到这种情况在可预见的未来会发生变化。

另外，如果我写的东西到处都是，我道歉。虽然我很期待能够写更多的东西，但这只是一个简短的帖子(可能还不止这些😅).

谢谢你过来阅读！

* * *

编辑:关于上面的第三点，我现在肯定更加困惑了...一个从 5.2 开始使用 PHP 的本地开发者告诉我，如果没有`global`关键字，它总是会出错。