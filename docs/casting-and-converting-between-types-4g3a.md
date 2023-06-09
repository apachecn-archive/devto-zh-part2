# 类型之间的转换

> 原文：<https://dev.to/funkysi1701/casting-and-converting-between-types-4g3a>

最近有人问我如何将数字转换成字符串。让我们看看解决这个问题的几种方法。

c#中的大多数对象都有一个名为 ToString()的方法，它显示该对象的字符串表示。这是因为继承，所有的对象都继承自系统。定义 ToString()的对象。

Int32 是一个结构，所以它继承自 System。也从系统继承的 ValueType。目标

所以在代码中

int a = 9；

字符串 b = a . ToString()；

现在我们来看反面。然而，反向操作有抛出错误的风险，让我们看看为什么。

string b = " 9

字符串 c = " a

串 d = "二"；

所有都是有效字符串，但只有一个可以转换为数字。使用 TryParse 方法转换为数字。

int.TryParse(“9”, out int e);

如果转换失败，TryParse 不会抛出异常，如果成功，变量 e 将包含结果。注意 c#的早期版本要求您在使用 TryParse 之前定义 out 参数。

里面的 Parse 的存在是为了做同样的事情，但是如果转换不可能，它会抛出异常。如果使用 Convert，情况也是如此。ToInt32(“二”)；

#### 铸造

强制转换是一种明确告诉编译器一种类型实际上是另一种类型的方式，并且您知道会发生数据丢失。

双 x = 4.5

int y =(int)x；

但是，不可能将字符串转换为数字格式，因为字符串可以包含任何字符，而不仅仅是数字字符。

类型之间的转换的帖子[最早出现在](https://www.funkysi1701.com/2018/05/07/casting-and-converting-between-types/) [Funky Si 的技术讲座](https://www.funkysi1701.com)上。