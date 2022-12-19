# 演示者和视图符合界面分离原则

> 原文：<https://dev.to/hector6872/presenters-and-views-meet-the-interface-segregation-principle-11ia>

## 亦称“第四个字母在固体中的缩写”

[![](../Images/23db36584ef67abe3fd1187de699eb60.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--FuRE-UQ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/hrules6872/Writings/master/2-presenters-views-interfaces/art/1.jpeg)  [超人大战穆罕默德阿里漫画](https://en.wikipedia.org/wiki/Superman_vs._Muhammad_Ali)

有几篇文章是关于 **MVP** 模式(或者一些作者用来称呼它的任何花哨名字`:wink:`)的，甚至更多的文章解释了**坚实的**代表什么，所以我们将集中在:

1.  演示者和观众之间的契约
2.  代码重用

> 说到 MVP 和合同，看在上帝的份上，一些开发人员应该读一下这个！

#### DRY³

一次又一次，我们被告知必须**避免重复和冗余**,因为“维护的代码越少越好”,但是我们发现自己一遍又一遍地无数次地输入几乎相同的接口。