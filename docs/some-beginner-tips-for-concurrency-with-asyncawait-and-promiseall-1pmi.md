# Async/Await 和 Promise.all 并发的一些初学者技巧

> 原文：<https://dev.to/puemos/some-beginner-tips-for-concurrency-with-asyncawait-and-promiseall-1pmi>

#### 如何利用好并发来减少执行时间。

因此，您正在使用 **Async/Await** 并享受它的所有好处:

1.  干净的代码
2.  更好的错误处理
3.  容易分支(if/else)
4.  排除故障
5.  多得多…

然而，不要让漂亮整洁的语法将您的注意力从并发性以及如何从并发运行任务中获益上转移开，这是一个好主意。

[![](../Images/662982999c619370ae5a33dda52e3c01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NAZrML4C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AWep4GsSxXmvbToTx60c5ug.gif) 
*多任务*

> **并发计算**是[计算](https://en.wikipedia.org/wiki/Computing)的一种形式，其中几个[计算](https://en.wikipedia.org/wiki/Computation)在重叠的时间段内执行——并发[——而不是顺序执行(一个在下一个开始之前完成)。](https://en.wikipedia.org/wiki/Concurrency_(computer_science))

我将通过展示两个**并发性大放异彩的案例来展示其优势。**

### 案例一:任务列表

假设您有一个需要在外部服务上发布的帖子列表。在发布之前，您希望验证一些帖子字段，以及它们是否可以发布。

#### **时序* *解** 😴**

 **您可以遍历列表并检查每个帖子的有效性，如果帖子有效，就继续发布它。**