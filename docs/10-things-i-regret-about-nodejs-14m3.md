# Node.js 让我后悔的 10 件事

> 原文：<https://dev.to/nickytonline/10-things-i-regret-about-nodejs-14m3>

照片由 flickr 用户 [Trygve Lie](https://www.flickr.com/photos/trygve_lie/5831902960/in/photolist-9Tm2z3-9TkXYG-9Snmm2-9TiaVg-986fu7-91Lys4-af3D6G-91PELy-91PFdw-91LxWD-ir76Ci-8vTR8o-91PEMA-guo8Zw-guot1K-8vQPsx-9Skm3e-91LydX-8vQP9D-8izGEj-guo94j-UD21ff-27wa2Xi-9Skmgc-9SqeLo-9SwjVQ-VAVtVQ-Kc6jW6-9Swk7s-8vTQMJ-9Sobs3-aCccoi-8vQP4a-gunPQK-9SkkHP-9SkjYa-8vQNvP-gunfvN-fhpKFt-a31Gwp-8vTRij-9Skmpr-8vQNt4-fhpJRe-ir6Gd4-SyeqvE-9Sqdf1-9Swkk3-keuT4b-UqDk2W) 提供。

Node JS 的创始人 [Ryan Dahl](http://tinyclouds.org) 最近在 JSConf EU 2018 上发表了题为“Node.js 我后悔的 10 件事”的演讲

[https://www.youtube.com/embed/M3BM9TB-8yA](https://www.youtube.com/embed/M3BM9TB-8yA)

这是一个关于 Node 的精彩演讲，他还详细介绍了他的新项目 [Deno](https://github.com/ry/deno) ，一个基于 V8 的安全类型脚本运行时。

以下是他后悔的一些事情:

*   动态语言对于某些事情来说很棒。但是在服务器中，您希望事物是静态类型的。(大概就是他在 TypeScript 和 Go 里写 Deno 的原因吧)
*   设计错误:
    *   没有遵守 Node 中的承诺(在开始时)，遵守这些承诺可以加速 async/await 开发。
    *   安全
    *   构建系统(GYP)
    *   package.json

就连布伦丹·艾希也附和道

> ![BrendanEich profile image](img/c27772ea4c4766f007962adbd8fdd98a.png)布伦丹内克@布伦丹内克![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)Ryan Dahl，也是自 1995 年以来的我:“我逐渐认识到，无论何时当你在设计一个程序时，有一些你认为可能很可爱的东西加入其中，你总是会后悔...不要做他们！”
> 
> 2018 年 06 月 18 日下午 12 点[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1004425861410402304)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1004425861410402304)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1004425861410402304)

给它一只手表。很好奇大家的想法是什么。