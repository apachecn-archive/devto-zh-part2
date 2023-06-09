# 死代码是如何在开发过程中出现的

> 原文：<https://dev.to/sadick/how-dead-code-spring-up-during-development-5e4i>

[![](img/be90b4bac3ca545ded418a1cc441dade.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jqAFm2sC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://refactoring.guimg/refactoring/content/smells/dead-code-01.png)

死代码是永远不会运行的代码，永远无法到达。
**死代码的一些表现形式包括:**

*   从未被调用的函数
*   已写入但从未读取的变量
*   传递给从未使用过的内部方法的参数
*   从未使用过的枚举、结构、类或接口
*   从应用程序的用户界面中删除了一些特性，但保留了后端支持代码。
*   向导生成的用户界面代码插入了经常不使用的挂钩。

**拿走**

*   尽可能删除死代码。它会妨碍你，让你慢下来。
*   删除将来可能需要的代码是安全的。您总是可以从版本控制中取回它。
*   代码清理应该总是在单独的功能变更提交中进行。
*   即使是最好的代码库也会出现死代码。告诉你的代码，要么生活，要么迷失。