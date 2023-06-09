# NMandelbrot:在客户端运行任意代码

> 原文：<https://dev.to/craignicol/nmandelbrot--running-arbitrary-code-on-client-29l4>

作为我的 JavaScriptmap-reduce 和零安装分布式计算的宏伟计划的一部分，我必须考虑如何在我们[不信任服务器](https://craignicol.wordpress.com/2014/05/27/the-three-rules-of-network-security/)的安全环境中获得用户的信任。我想不出一个好的答案。

从那时起，我们已经看到了[恶意 JavaScript](https://craignicol.wordpress.com/2015/05/25/nmandelbrot-clients-gaming-the-system/) [安装](https://craignicol.wordpress.com/2015/03/17/is-your-cpu-time-there-to-be-stolen/)到[矿](http://www.forbes.com/sites/kashmirhill/2013/11/19/brilliant-but-evil-gaming-company-turned-players-computers-into-unwitting-bitcoin-mining-slaves/) [加密货币](https://cloudblogs.microsoft.com/microsoftsecure/2018/03/13/invisible-resource-thieves-the-increasing-threat-of-cryptocurrency-miners/)的故事，我们知道 [JavaScript 可以被利用来读取客户端](https://isc.sans.edu/forums/diary/Meltdown+and+Spectre+clearing+up+the+confusion/23197/)上的内核内存，包括密码，我怀疑我们会看到对 JavaScript 允许做什么的更多限制——尽管 Spectre 利用从根本上来说是一个数组读取，这将是一个多层次的复杂修复。

我有关于如何沙盒客户端 JavaScript 的想法(我正在查看 Python 的 virtualenv 和 Docker 容器以隔离代码，并将其锁定到已经具有非常有限的 API 的服务工作者)，但这依赖于浏览器和操作系统保持分离，如果虚拟机不能[以其隔离级别保持](https://www.techrepublic.com/article/10-new-vm-escape-vulnerabilities-discovered-in-virtualbox/) [分离](https://threatpost.com/vmware-patches-pwn2own-vm-escape-vulnerabilities/124629/)，这对浏览器开发人员或任何在他们的平台上运行的人来说都不是一件容易的工作。

我还想知道客户端是否应该用一种转换成 JavaScript 的函数式语言来编写，以便在语言层面上执行不变性和安全检查。当然，因为[一个函数式风格和 API](https://medium.com/dailyjs/functional-js-with-es6-recursive-patterns-b7d0813ef9e3) 通过避免任何隐含的共享上下文，提供了一个更简单的上下文来推理 map-reduce。

[你允许别人的 JavaScript 出现在你的网站](https://www.zdnet.com/article/uk-government-websites-ico-hijacked-by-cryptocurrency-mining-malware/)上吗，不管是库、跟踪脚本，还是来自俄罗斯、韩国、僵尸网络和脚本小子的随机广告？您如何保护客户的安全？如何将您信任的流程与处理外部世界和用户的流程隔离开来？JavaScript 在未来会更安全，研究很有趣( [JavaScript Zero:真正的 JavaScript，零旁道攻击](https://blog.acolyer.org/2018/03/13/javascript-zero-real-javascript-and-zero-side-channel-attacks/))但是你能等得起吗？

Meltdown 和 Spectre 不应该改变这一切。但现在是考虑这个问题的好时机。让 2018 年成为你对用户、第三方和其他威胁变得偏执的一年。这一年还很早，但功勋却越积越多。