# 高级调试技巧

> 原文：<https://dev.to/tiptopgs/advanced-debugging-trick-4183>

在 Xcode 中调试所有这些崩溃问题并不总是有趣的，尤其是当您在控制台日志中没有收到非常有用的信息时。为了更好地帮助我们自己，诀窍是启用异常断点。这可以在 Xcode 左下角的+上找到。

[![](../Images/a3ce19dda64522acf2b2f344cb62834e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5tu1QeLi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/438/1%2AQBk8YoXfSR84U0Q-VHPXsA.png) 

<figcaption>异常断点</figcaption>

允许异常断点在所有 Objective-C 异常上中断。这足以捕捉来自苹果基础类(UIKit 等)的所有元素。)当它们发生时。我以前曾多次使用这种方法在我的应用程序崩溃之前捕获代码行，但从未意识到我可以让 Xcode 在程序完全停止之前打印出实际的异常错误描述。请注意打印错误消息 po $arg1 的附加操作。这就是我们所需要的。

[![](../Images/7db211af22cf0eb6359b4f972df4c60a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7m-OngMC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/932/1%2AHU_Afe6uoOZyfQ52q3TCPQ.png) 

<figcaption>添加动作打印错误信息</figcaption>

要进一步使这个异常断点设置对所有 Xcode 项目可用，您可以将断点移到 User。

[![](../Images/074dc3755391d858839387790e12741d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LEsBsheb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/940/1%2AkNSP8ZX-6JrwwZfD-dgctg.png)

试试这个，让我知道这个高级技巧帮你解决了什么异常。

我在 https://www.hackingwithswift.com/从保罗·哈德森那里学到了这个新技巧