# Java 中的构造函数链接及示例

> 原文：<https://dev.to/skptricks/constructor-chaining-in-java-with-examples-4ppp>

帖子链接:[Java 中的构造函数链接及实例](https://www.skptricks.com/2018/09/constructor-chaining-in-java-with.html) 

从同一个类的另一个构造函数调用一个构造函数被称为[构造函数链](https://www.skptricks.com/2018/09/constructor-chaining-in-java-with.html)。构造函数链接的真正目的是你可以通过一堆不同的构造函数传递参数，但是只在一个地方进行初始化。这允许您从单个位置维护初始化，同时向用户提供多个构造函数。如果我们不链接，并且两个不同的构造函数需要一个特定的参数，你将不得不初始化那个参数两次，并且当初始化改变时，你将不得不在每个构造函数中改变它，而不仅仅是一个。

[![](../Images/93260d51599a07f7eba27013dd464058.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m2fMiUZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3.bp.blogspot.com/-SPb95R2-aSA/W4oFNjBK8rI/AAAAAAAAB20/KdcUB9Z6F-AjRscsk7UsO1Na6LwFhEDegCLcBGAs/s400/cc.jpg)

[点击查看示例](https://www.skptricks.com/2018/09/constructor-chaining-in-java-with.html)