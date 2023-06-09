# 你必须知道的软件测试的 7 个基本原则

> 原文：<https://dev.to/dohertykornelia/7-basic-principles-of-software-testing-you-must-know-523>

[https://www.youtube.com/embed/rFaWOw8bIMM](https://www.youtube.com/embed/rFaWOw8bIMM)

考虑一个场景，您将一个文件从文件夹 A 移动到文件夹 b，考虑所有可能的测试方法。除了通常的情况，您还可以测试以下条件:

*   试图在文件打开时移动它
*   您没有将文件粘贴到文件夹 B 的安全权限
*   文件夹 B 位于共享驱动器上，存储容量已满
*   文件夹 B 已经有一个同名的文件

其实不胜枚举。假设您有 15 个输入字段要测试，每个字段有 5 个可能的值，那么要测试的组合数将是:5 <sup>15=30517578125</sup>

如果您要测试所有可能的组合，项目执行时间和成本将呈指数增长。因此，测试原则之一规定 ***不可能进行*** 的详尽测试。相反，我们需要基于应用程序风险评估的最佳测试量。最大的问题是你如何确定这个风险。为了回答这个问题，让我们做一个练习。在你看来，哪个操作最有可能导致你的操作系统失败？
A .打开 Microsoft Word
B .打开 Internet Explorer
C .同时打开 10 个大型图形应用程序
你们大多数人可能会猜到同时打开 10 个大型图形应用程序。如果你正在测试这个操作系统，你会意识到缺陷很可能在一个多任务模块中被发现，并且需要被彻底地测试。

***缺陷聚类*** 表示少量模块包含了检测到的大部分缺陷。有了经验，您可以识别这种有风险的模块，但是这种方法有它自己的问题。如果相同的测试一遍又一遍地重复，最终相同的测试用例将不再发现新的 bug。这是另一种叫做 ***农药悖论*** 的检测原理。为了克服这一点，测试用例需要定期检查和修改，添加新的和不同的测试用例来帮助发现更多的缺陷。

但是，即使在所有这些汗水和努力工作和测试之后，你也不能声称你的软件是没有错误的。 ***测试显示缺陷的存在*** 例如，降低了软件中未发现缺陷的概率，但即使没有发现缺陷，也不能证明其正确性。

如果你非常努力地采取所有的预防措施，确保软件产品 99%没有错误，并且软件不能满足客户的需求，那该怎么办？ ***没有错误就是谬误*** 。如果系统构建不可用，并且不能满足用户的需求，那么查找和修复缺陷是没有帮助的。为了解决这个问题， ***早期测试*** 应该在软件开发生命周期中尽早开始，这样在需求或者设计阶段的任何缺陷都可以被捕获。

测试的最后一个原则是 ***测试是上下文相关的*** ，这基本上意味着你测试一个电子商务网站的方式将不同于你测试一个商业现成应用的方式。

以下是 7 个测试原则的快速回顾:

1.  测试表明存在缺陷
2.  彻底的测试是不可能的
3.  早期测试
4.  缺陷聚类
5.  农药悖论
6.  测试是上下文相关的
7.  没有错误是一种谬误

注:本视频由 [Guru99](https://www.youtube.com/channel/UC19i1XD6k88KqHlET8atqFQ) 供稿

原文出处:[testautomationresources.com](https://testautomationresources.com/videos/software-testing-principles-tutorial/)