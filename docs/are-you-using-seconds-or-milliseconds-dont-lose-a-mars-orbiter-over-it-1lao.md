# 你用秒还是毫秒？不要因此失去火星轨道飞行器！

> 原文：<https://dev.to/aniketsmk/are-you-using-seconds-or-milliseconds-dont-lose-a-mars-orbiter-over-it-1lao>

如果你需要一个包含时间的对象，你可能需要转换它。单位转换是许多错误的原因，有些比其他的更具灾难性。

设计软件时，总是让正确的事情容易，让错误的事情难。

<figure>[![](img/69e015be5989c563d1c782c27a591a9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WSRM2yLk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AK1vx1GuRFEqin70QYDyAcQ.jpeg)</figure>

如果你从来不需要担心它是哪一个，并且无论你需要几毫秒还是几小时，你都能 100%正确的得到它，那会怎么样？

下面来说说。

让我们假设你在测量锻炼持续时间。