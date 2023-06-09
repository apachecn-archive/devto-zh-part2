# 优先级反转和优先级继承

> 原文：<https://dev.to/hatsugai/prioriy-inversion-and-priority-inheritance-nl3>

# 优先级反转

参见[维基百科](https://en.wikipedia.org/wiki/Priority_inversion)。

我将按以下步骤处理这个问题:

1.  在 [SyncStitch](https://www.principia-m.com/syncstitch/) 上建模问题。
2.  通过模拟观察问题。
3.  针对该问题制定安全规范。
4.  实现优先级继承协议。
5.  检查安全性。

# 热身

使用 SyncStitch，您可以轻松实现并快速测试您的想法。在挑战优先级反转问题之前，先建立一个简单的模型作为热身。

一个简单的 RTOS 可以被建模为具有以下状态转换的过程。

[![sd1](img/edc10dbd469e36e8b340394bbe83d0a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y2Dn5Wwp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g39ut8moo30xx62egu50.png)

内核可以这样建模:(只是一个草图，这可能是世界上最小的 rtos？)

[![ker0](img/06fc3839e69aea8afe3f3f1cd86079ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bMkEtMZG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5wt1otk6o9mzagq39d6b.png)

然后，您可以对其执行模拟:

[![pe0](img/d1009aae98350b2c66ce4c01bfaa111c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CFfjOYqK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c54zyzaalj0c0t21bqfy.png)

您可以看到一个优先级较低的进程被一个优先级较高的进程抢占了。

# 实现锁定

像这样:

[![sd2](img/efce6fca8854e020cd366b04588ee263.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s3C0zfQD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iogd627axf3qkpqai5wl.png)

[![kernel](img/631ff27b6a1cde00e1b4ddbd9e572572.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VYAhDPTM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jmrtilbujyeri3zwwh62.png)

当然，进程有可能在锁中被抢占。
事实上，你可以观察到以下的顺序:

1.  进程 2 唤醒(数字越大，优先级越低)。
2.  进程 2 锁。
3.  进程 0 唤醒。
4.  进程 2 被抢占。
5.  进程 0 运行。尝试锁定，但失败，因为它已被进程 2 拥有。
6.  进程 1 唤醒。它可以想运行多久就运行多久，尽管更高的进程 0 正在等待锁。

[![pe2](img/9ead90b9ea3caa06ad0986a73c98161c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lBaGNzMc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cfsy7akwi9ab63unla0b.png)

这种现象就是所谓的优先级倒置问题。这可以显示为一个图表:

[![pi](img/73df82abd7c227137c2ed3911a3b2d36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m1-WV86K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2pdz245jk640ziolmb24.png)

# 安全规范

我们通过上面的模拟观察到了这个问题。接下来，我们制定一个规范，使工具能够自动检测问题或确保没有问题。

这种类型的规范被称为安全规范，它可以被描述为一种状态转换，只接受可能的后果，不包括任何有害的后果。

我们可以这样定义优先级反转协议的安全规范:

[![safety](img/e5c5fef921d72a9cf4975a6c897a07b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v067Q0nU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n4rbu1zdys0mhgdwqkvj.png)

SyncStitch 是他们所谓的精化检查器之一，它有能力比较给定的规范和给定的实现模型，并检查它们之间的正确性关系。

检查结果是这样的:发现一个违规:

[![assert1](img/670dff097f2f2415daf12d8145612275.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ulQ2QJiu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2xitjlvzwjhv57cjps1j.png)

然后，您可以使用 SyncStitch 提供的分析工具彻底调查违规情况。

您可以看到与我们在上面看到的违规相同的序列:

[![seq](img/9dd91312c0d37f7b75b9f4ef7dabaaa9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--97DlP3ry--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fqxps80yp1itxgcmd8xu.png)

# 优先级继承协议

优先级反转问题的解决方案之一是优先级继承协议。这个想法很简单:在调度进程时，选择阻塞最高优先级进程的进程。

[![pi](img/ca4c11eae4c2c54bd4472394a55b2f70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7_husPDA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7km3lw9pluj09budp424.png)

选择的功能如下:

[![sp](img/ad53bcfce9598c11dc0281d71fac6ed4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KsO9zZuH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xqv491i7k6nqo4slgmfe.png)

把它放到模型中，你会发现问题已经解决了。

[![assert2](img/4ad23ed98e70730dccc67c114dc807c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zZubEOrj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d3971xjfvl7aj3w9q6xu.png)