# 使用“class”关键字创建对象

> 原文：<https://dev.to/zmiles17/object-oriented-programming-in-javascript-141e>

过去一周，在我的编码训练营中，我学习了如何使用“类”和“构造函数”关键字来创建对象。使用这种方法，创建新的对象变得更简单，并使我们的代码更干净。为了演示，如果我们没有“class”和“constructor”关键字，下面是创建对象的样子。

[![](../Images/b4d012c621dc0ad80847825645e72e95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cbrURnt5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hgspairpvew7pwtlfcep.png)

在我的演示中，我通过复制以前的对象并替换值，手动创建了 students 数组中的所有对象。我希望你能理解这可能会变得乏味，特别是如果我为学校的每个学生创建一个对象。下面是使用上述“class”和“constructor”关键字的相同输出:

[![](../Images/9d452b06a4d34ada22a780a7d0a1ee0d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o5PGYaFk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gxtd7zaw8drtl9szh3tc.png)

最初，我们可以看到代码的行数减少了。我不必一遍又一遍地复制和粘贴同一个对象。只要我知道如何构造我的类对象，我就可以使用“new”关键字轻松快速地创建一个新对象。另一个需要注意的是，类声明是不悬挂的。如果我试图在声明“class Student”之前创建一个新的学生，那么它将抛出一个错误。

JavaScript 类的另一个很酷的地方是我们可以附加特定于每个对象的方法。

[![](../Images/f1a39226bf1fd68e978cf9b895dbd3f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8SDaI0YA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pi6kvq3f83jrhljw3iaw.png)

现在每个学生都有一个名为“enroll()”的方法，如果 active 的值当前为 false，该方法会将它更改为 true。如果 active 已经为真，那么什么都不会改变。类对于避免冗余和轻松编辑对象值特别有用。感谢阅读！