# For 循环与 forEach:距离太远了！

> 原文：<https://dev.to/basedenergy/for-loops-vs-foreach-a-far-reach-59j1>

当我第一次学习 Javascript 数组时，一个被反复重申的主要概念是，当我们需要一个函数来命中数组中的每个对象时，我们需要一个循环。

### 兄弟，我可以循环一个数组吗？

[![brother loops](img/297df4bf5bf0cfc20578f774ea3954d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y0ufR4U2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.kym-cdn.com/entries/icons/mobile/000/026/720/Screen_Shot_2018-07-18_at_2.21.03_PM.jpg)

因此，在我们设计一个需要遍历一系列小猫的应用程序的世界中(希望是我未来的职业)，循环的典型**如下所示:**

[![kitty loop](img/2ff08625b4f24a8fdd53863a7807c10c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TaxuVoe9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w53nbhoc8nvr20ykrf9s.png)

这很好，但是我们也可以选择使用一个漂亮的方法，我们的数组将从 Array.prototype 继承这个方法，称为 **forEach()** 。我们可以调用 forEach()来有效地完成我们的 for 循环所完成的事情，它看起来有点像这样:

[![kitty forEach](img/da7748df8c3573619dc83a56879482fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mWIoKXRI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zs3jfkww12vpavgyru0c.png)

我知道您在想什么，“为什么开发人员更喜欢使用其中一个而不是另一个？各有利弊吗？?"。男孩，我要给你一份礼物，因为绝对有。

### 为循环的情况

有一些真正的约瑟夫·高登·莱维特风格的循环程序会告诉你，for 循环比使用 forEach 方法要有效得多，而且它们有一些优点！

*   一些 Javascript 引擎和浏览器更适合 for 循环。
*   Break 可用于提前终止循环。
*   对循环条件给予更多的控制。

### 对 forEach()的反驳

另一方面，forEach()的支持者会很快告诉你“forEach()方法是第一！!"(我在街上经常遇到这种情况)。我理解 forEachers™️对他们最喜欢的阵列方法的热情。

*   forEach 可读性更好。当您将 for 循环中需要的临时变量考虑在内时，需要查看大量代码！forEach 看起来更干净，阅读时间更少。
*   代码中出现小错误的可能性更小，因为您不必指定条件语句。

### Dev 拿！

虽然我真的不想让自己卷入这场辩论，而且我通常避免谈论政治，但我不得不说，我确实有一个偏好...

我认为在大多数需要循环数组的情况下， **forEach()方法**是更好的做法！它看起来更好，使代码更可读和可维护。我唯一会使用 for 循环的时候是当我知道我需要尽早跳出循环的时候。否则我每次都去 forEach()。

#### 带上《为了回路者》的仇恨邮件。

[![loop earth](img/9d6c750d2d478bca9d9b934b37c29675.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Syca3oi9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/PLBYX4M.jpg)