# 什么代表了过去、现在和未来

> 原文：<https://dev.to/taowen/what-represents-the-past-present-and-future-963>

### 什么代表过去、现在和未来:未来(1)

[![](../Images/e7588232045e3735f01974abb3b41cf8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p3cuW0L7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AO97GbaErKzttHXOM.png)

我们的软件为智能代理人建立了一个相互交流的网络空间。使用机器学习算法来构建智能代理是一个热门话题。但是大部分代码仍然是关于构建虚拟环境本身的。代码和运行时描述了在这个关于“未来”的环境中会发生什么。为了解决模糊性，代码和运行时必须解决“当前”真正发生的事情。代码和运行时还需要忠实地记录“过去”发生的事情。要保持完整性并打下坚实的基础，这三件事都应该做得恰当，否则，我们会将异常感知为“一个 bug”。正如文章封面所示，最突出的网络空间是在线多人游戏。

随着商业兴趣迅速转移到构建更智能的代理，网络空间构建技术成为一种商品，一些好的古老智慧可能会丢失和被遗忘。目前的主流方法是成熟且有效的，但远非完美。这一系列的三篇文章试图总结我对“过去”、“现在”和“未来”的可能表征的观察。其中一些值得更多关注。

什么代表“未来”？这似乎是一个相当明显的问题。所谓商业逻辑，有很多很多的表达方式。我们将从“未来表征”的角度来看一些非常初级的概念。那么我们可以看到它实际上并不像我们想象的那么简单。

### 源代码

“未来”存在于文档、源代码和运行时中。我们要用的语言是 lua 5.3 和 es2017 (ECMAScript 2017)。所有代码都可以在[https://tio.run/](https://tio.run/)在线试用。

### 简单的过程

我们要描述一个非常琐碎的过程:result = (a+b)*3。“某 _ 重要 _ 业务 _ 流程”描述了取 a、b 并计算结果的过程。

### 功能

描述“可能会发生什么”的最简单的方法是使用一个函数。

lua 版本:【https://tio.run/】T2

[![](../Images/10065ee8b23e92038b82343fb5ce37db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CWsg2xqC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/476/0%2ABunIN7l1MyvZ43hA.png)

es2017 版本:[https://tio.run/](https://tio.run/##hY6xDsIwDET3fMWNLRQqYGLgW6o0BBSU2lXsdEF8e6igqsTELed7sq172MmKS2HUHfHVl3LL5DQwQXjwXRhGTmpJuz5LIC/SjYnd7JVt0Nd4GsyKXpG85Ki4wGKLfsVuJqdPWheWYQO3cM2JFmpexvx8@1fj0OBYG8ckHP0@8r36ntZoW5xLeQM)

[![](../Images/4c5aff3e3a481040c0b28e345897414e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B7f4a-6_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/479/0%2Aq2_WvsomoXtWL6ba.png)

### 部分功能

在前一个函数中，我们将 a 和 b 都作为输入。然而，我们可能从一开始就不知道所有的事情。要循序渐进地讲故事，我们先拿“a”，再拿“b”。

lua 版本:[https://tio.run/](https://tio.run/##hZDBbsMgDIbvPIWPsCaRtp162GFPEhlKKzRiEJjnT0lJMm5FQlj@/duf8QXX9V7IsAsEERM79PI@wDRNSkA9Phj0oEOh2296ZPgBRu3tFNH8ybMqWS6J4Oj0L/SibM5CL@/ZUnXTLN3Edk@kHBY7uyWGxEg865Id2ZznmIKpr8QBdA@abC6eKyXCBXQnmJr73ln3kj34ANPv0LIviubcRtXq43PeEA3wqcQ4NpfLlcMEYkcFt33EOXzT5ZcSMTli2dIKqvG6rk8)

[![](../Images/6b114c3a20f359227217865ccea4341d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--43N21qRe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/521/0%2Ad_swNOp2pKadKGHx.png)

es2017 版本:[https://tio.run/](https://tio.run/##hZC/UsMwDMZ3P4VGB4J7wMTA0CfJKa5bzLlWzpJZuD57UP40l054sSX9PumTv/EH2Zc4yEumUxjHc81eImUYsEjEZM8tOOd6qvl0LBdu4NeAnhKklgwbbhXCXX3POI8pWfmK/NhrjmbRLLmZm9n6MV1DF68DFcEsXV855sDcDYW83hZb6O@zUhCdxTUJfALCM/Rb2mvmfTW8AuvjCfx@kSWrDibVNETJ@xf846WF18YcDosqsjrwlCXmitMm5sHdxNi3xijBlIJLdLFLsQFt8TGOfw)

[![](../Images/a67b213789555f652e1d8d9418f669f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ir5uxzU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/520/0%2AzH8mH9OPjM9Hdmuf.png)

变量“proc”将“a”捕获到自身中。它是一个延续，执行可以从它继续。

### 目标

有一个初始输入和一个最终输出不足以描述将要发生的事情。未来的完整表示必须描述在未来给定更多输入的情况下会发生什么。为了表示这一点，通常的选择是使用一个对象来存储流程，并在故事展开时调用一些方法。

lua 版本:[https://tio.run/](https://tio.run/##nZHdasMwDIXv/RS6TLY20PRqgz2LcVwVDI4cbJkOSp89808oZXQzm2@MrHP0yZKNal2t08pCcDNKMy/OsyKWUwyGMAS5eKfTDR9wvYmGaJDS0Ak/k7ihFOIcSbNx1FIOhJdO9QLS2TpFe84E5BlZsZosdtfbrlWolsjmQSW7KqFHjp7Kq0A6/aGtwLgcumzcwfRQ22OIlkt/hfQK03fSpvkHcCzAStOJcfyJu0UvoH@F14FmQntlZRGHXuz31WACKNCO2FBU@Qfi3kLOv9cBjb1YvCHuarKHZD8@UY7dE@Hbun4B)

[![](../Images/5b20b894c7e013420ca0df1ef2db9cab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RnqLTKoZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/613/0%2AIq8tvnXBt_TI95cT.png)

es2017 版本:[https://tio.run/](https://tio.run/##hZDBbsIwDIbveYoc000UAacdeJbKDRYEhbiKne0w8eyd01YIgQY@xLL1@Xf8n@Eb2OcwyCrRAccxolimC3bhMlAWSNL1hUNC5m7I5DXbvfURNP8aq@EpseTihbKDZmnWkFPgFpSGqXWdXhYcNq5/4jJyiaLwMvVp@xuQUUpO99yD4Nbd69UTvCrtXm1Yqg/r36@5mipZr9fphD/vDHKbxqzX80BgC9UiCamABEqT1u0vlWlnT7aNqVZSxDbS0c1IY1VoZ555Pfk//Gsc/wA)

[![](../Images/c7a1554e6f383d072485efd807bbb521.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M3zxFLEm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/617/0%2A2gfsAqD5tPwnh_Vf.png)

这里的变量“proc”与前面的“proc”相同。它是一个延续，执行可以从它继续。

### 状态机

然而，使用 object 并不完全代表这个过程。因为步骤 1 和步骤 2 是相等的。没有地方定义步骤 1 应该发生在步骤 2 之前。更准确的表示是状态机:

lua 版本:[https://tio.run/](https://tio.run/##rZLRasMgFIbvfQrvmmyt0PRqgz7JGGLMKRPMMeiRFUqfPVMTtox1CxvzRsz5z/9/OWqjGkfrtLI8uB6k6QfnSSHJNgaDEIIcvNNp50d@ubIVkZDSYAfnJF5RMnaKqMk4XFMKhNdK1YynNZOCPeUEoB5IkWotVJfrds1ossjNQqV29XFEOJMMBEP6vMn7flOKHih6LBoG2P2CuZhUuXHL20WwhxAtFfiCcc/bHzCaLxizwR9omkKzHKNOMYfv0ObTHde3@dDY/2HLpvOghBBLvnS5L66bgZ4@Ezwvwyfh0qQgTC45Z/05lke2r9luNzWYwBXXDslgVPk/2Pt4cv2xUDc1G7xBqqZazVP34Zbwhu5hHN8A)

[![](../Images/115bf5f3510bcc814502e133b1356a2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zKz70ia2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/618/0%2AiFEPjY7vjABwyQg0.png)

es2017 版本:[https://tio.run/](https://tio.run/##jZJNboMwEIX3nMI7TH8chay6yEmqCg3OKHFlbGSP20pVzk49QKOENk29YMTw5ntPHl7hDaIOpqdH53c4DBZJRN9hY7reBwJHTZuicRhj0wevcxVboS3k@lmIfLR3kULS5IOEam7yoYOJCrIaLlsOP6iJhH3@VHJdl6PgOD7Hhmx/gALGZCmPzNh70d7A1uVJEJBScOeghWMtzw35EnTGbP6KML/dCX09h0vW/i8ET0mlFIR9XEbpkA5@N/s@X5q8LPGTWGmwVrL0QXxTZ7djwUzeJQfE91vrluuqWK2mARMF8MLJuARkvBtZp3thzRhN1lXB/4W3qKzfy0lRiczZFL/Ir6qfhuEL)

[![](../Images/d3ee93567f240bf4af7a8a21bc48f8f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HXmujFjC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/626/0%2AzAalUgdrhIQnyyC9.png)

“next_step”是一个游标，用于跟踪执行状态。在 x86–64 CPU 中，有一个称为“RIP”的指令指针寄存器，它类似于这里的“next_step”概念。

### 协同程序

协程像状态机一样工作，但看起来像一个函数。表达同样的想法需要更少的代码。协程有两种风格，一种是“yield”(也称为“generator”)，一种是“async/await”。我们先来看看“yield”是如何工作的。

lua 版本:[https://tio.run/](https://tio.run/##hZBLDsIgEIb3nGKWoNZEu3LhWRpKx4SEQsNj4elxaKutRi0bIPP9DzBJ5nxLVkXtLATXY6P7wfkobWzaFLTFEJrBO0U7l4IBLeOUNNDCFZTzLkWCjneNpuPruceQTCRIwh7acfCJT8hapIivx/tLPh92oP56oO0Ym0xK3bdyyqOMyDeeJ1hVTVodqLRylsRJlp9hi1fJ65EX7gAnMUc2h6XwD/Ys2OC1jc/GQGk12xR@UV1yfgA)

[![](../Images/2f341dc2bb88ab97133853df57a2730f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p9Ug2x1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/633/0%2A2YWa0WAsue2z0Ygo.png)

es2017 版本:[https://tio.run/](https://tio.run/##hY/RDoIgFIbveYpzKbZ05VUXPYtDPDUagpODq7WenUCdrdUmN8B/vo/93MQonBxUT3tjWwzh4o0kZU0OznZYq663AwlDdeOdMuhc3Q9Wxj0THJ4M4tJI0MAZHgp1uyYDOq8pxgJ20EzxBCyDlZMRqabbaiyHHOSv9mIsWalEJLc6HjgryxlWLjaR1pAyXqQfshQXBu@UcfbV@DM4chYVZzUW2l6zmShGoT1yiC9X7I@04ZxCeAM)

[![](../Images/d4a1446942b97d51f895a8a1b3559b6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B8k7MJvW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/632/0%2AtiTU_KLed24o1klO.png)

产量一举两得。它向调用者返回值并获得输入。从视觉上看，它是这样的:

[![](../Images/036cf39d1f1ecaebc6f361dc4f27724c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dj0Cv4w8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/463/0%2ASmYYhp2mBrbg-njt.png)

### 协程对象 I

协程总是以相同的函数名继续，如“next”或“resume”。它不如“第一步”或“第二步”有表现力。我们可以将协程包装成一个对象，使代码看起来更好。

lua 版本:[https://tio.run/](https://tio.run/##lZLBbsMgDIbvPAVHsqWRmp02ac@CgLgSEoEIjLap6rNnENJE7Tol5QLCn//f2JgoxtE4JQwNrgeu@8F5FBa5jEFbCIEP3qm00096vpANqOFc2w6@E7xBEnKKVqF2dotsLHwxURGa1lwpmFN2AOwBBQppgJ0v9ZZQkcjJTb5JCsp5FzFBjfIgENi1JlbY1VLe0D8aTPeH8RCiwQQK@krlErxPK9h9ci7nbblbpObDC1W79MB25bBCOdwDW55d4h4wejs1g6SkJ6YREIbjJFdTeSP2v@dEPm/TTgo7PWaD0s15vnt@1rGac3i9tj0DH@WlbUUGry1e@0wPhzSoh3DLHrDv4/gL)

[![](../Images/e08ef2c7923c309ee64b34f1c143f7a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--df_KbVXm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/632/0%2AwvCAy95sGUEca2Ke.png)

es2017 版本:[https://tio.run/](https://tio.run/##lZDBbsMgDIbP4yl8JJ1K1fS0w54lItTbmChE2FSrpj175rAm2pRDNC4G@/8/bL/bqyWX/cD7mM44ji8lOvYp7oDSBTt/GVJmG7nrC/mIRN2Qk5OobQOfCuQEZOjhGW4ew3nJZKQSWNIWHqGv6Sq4FxadE8mpvhbH/bIDt7Z9qY2@jEROfBvQEONwFN48k@6nlh8ycskR@M2TifjBkv43tv2NnRexAk9cNQFEvbXOY6MOB6hiT7I0lyL7WOz0Q2XMyD/LrYU6p24bJR5KAU1Ir/pHYq42FGxA0Ce1drV6w/Q0jt8)

[![](../Images/c92cf15d1cc3215afdb479a5eb6e77a6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GN-QLyN3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/638/0%2Aje87lK5U-diJAEUa.png)

### 协程对象 II

虽然我们已经添加了方法“步骤 1”和“步骤 2”，但是它们并不强制按那个顺序被调用。另外，如果我们希望“第一步”有两个选项呢？比如说，它可以是“第一步添加”或“第一步订阅”。让我们将协程对象升级到版本 2。

lua 版本:[https://tio.run/](https://tio.run/##jVPbjoIwEH3nK/om3UUS9Wkf/JamwKgkpSW9qBvj/jo7tVhUJEhi0Jkz55y5KBzvOqFKLohRDbC6aZW2XFpWOFNLMIa1WpX4JltyuSYzoJyxWlZwRvDOydLWSqYGxC4jkjdAE4JPUDsiRPPTHuwIUO8waw8gb7/8o8E6LcnxFgBZJQ/BqMP13tBY4klzH0Id/4qJIN@APagKc172VadUWjmLjeUajGvg5jD3LWZ9IY1O/Ce5e5ibYS7hlPLHMXhmdGHAIjG3vBCQXq7ZHFGgiLaQYfBcauAW0jgXOtX6UPJbg6hGQN@8sDGIa0nvtVuyMBbaFeNVtaDPywqD9KV@9OR7WEVeRBAIA28JjStmCJeThE8VoLXS6cJJOLdQWkCfA7g/oXtbr5MIapT89bbW7yx9KhCG6Xe0SUbt9F@@SBlzE2aSQBs0pi@UPv43fDRcaH8ln9zniiYsGxz6VB53cym26ytZLtGBExUpgMQ7QI1a2jg7xGzeE63x0kbYn677Bw)

[![](../Images/07c605ab4b3b3d929a07a0991432fd53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fUD-dvCi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/638/0%2AdHmec8p3Nsfu-ZjX.png)

es2017 版本:[https://tio.run/](https://tio.run/##jVNNc4IwEL3nV2xPBKs46s2O/6H3TocJYdW0mDBJ8GMcfjtNAFGhreYCyb59u/vy8sX2zHAtcjuRKsWqWheSW6EkGLXDWOxypS2TNk4KIyQaE@dacfelLIQzAbcytOAPYQWX5BG9BC8AjabIbHf0sUO7VekYmN6YT5d5EpilXdgchOVb2oBuqfzizCAExmI@i1maBsu7qF9NMcfK4LWuECUDTKKRff/Ja4rkf97J87wprplLG9LZrVYHCAqJxxy5xTToIOVVKcl22NOpr6ZYA/UweFmtmgnmQV@1p4r5m/L3uCCDkdufEfAuNuilpGH99W6IXCHb7jXaQkuQeIB3rY4n6gHjmwY3aJedefyOWjcw2jH8boGW8JJBvTp9zA2uIWs6undeeJdS9iQpwzdSEnJj8EePYhaS6bSZv74G9w4OIsta9fHIMfcNk@uTcKQdvPYdPSdLmJchOCKuCqdwgtC5nXAljcowytSGNgzRnmUF1vjJjAxZXRMPshZV9QM)

[![](../Images/7d6e2353dce1193119e33ca3d0ee636b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2pR4XznX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/638/0%2AZPis7MPmj9v4S76a.png)

我们可以看到协程和 object 非常相似，协程更强大。但是如果没有直接的语法支持，协程的编写方法是很麻烦的。

第一章到此结束。我们已经使用这些方法实现了一个非常简单的过程

*   功能
*   对象= >状态机
*   协程= >协程对象

函数在实际使用中是不够的。所以我们要么选择对象，要么选择协程来表示未来。对象对其期望是明确的，但对其状态是隐含的。协程对它的状态是明确的，但对它的期望是隐含的。我们已经尝试将对象和协程合并为一个，但成效有限。

此外，我们还学习了许多形式的延续

*   部分功能
*   对象实例
*   协同例程实例

在接下来的章节中，延续将构成复杂调度的基础。

另请参见:

[什么使得代码不可读](https://medium.com/software-engineering-problems/what-makes-code-unreadable-to-human-45fa30544386?source=collection_detail----1d0a7ad41f0e-----1---------------------)

[什么造就了优秀的软件架构](https://medium.com/software-engineering-problems/what-makes-up-the-software-20f607da9155?source=collection_detail----1d0a7ad41f0e-----2---------------------)

[编程语言的 3 个角色](https://medium.com/software-engineering-problems/4-major-problems-of-programming-language-c6974c24e083?source=collection_detail----1d0a7ad41f0e-----3---------------------)

* * *