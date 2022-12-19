# JavaScript 中的对象是如何工作的？

> 原文：<https://dev.to/flippedcoding/how-do-objects-work-in-javascript-34mj>

物体有古怪的名声，但是它们真的没有那么糟糕。他们在 JavaScript 中有自己的做事方式，你可以控制他们如何做。您可以为对象赋值，并从中获取结果。它们基本上就像超级变量，可以保存你赋予它们的任何东西。

这里的尝试有助于使对象的概念更容易理解。

想想你的手机。它有一定的高度、宽度和厚度，它有不同的应用程序来发送文本或上网。现在想想你能想到的所有其他手机。就像你的一样，它们有一定的维度和功能。

现在用同样的方式思考物体。一个物体就像一部普通的电话。你可以赋予它定义对象的属性，你可以拥有对象可以使用的方法，就像你可以得到一部有特定维度和应用程序的手机一样。

[![javascript object phone comparison](../Images/601426b725b2405cfaba6f4c1cae658e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PRDLZabb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o0psoothfx1eey8gntlc.JPG)

对象之所以复杂，是因为它们可以保存不同类型的多个值。对象是如此开放，以至于您可以将函数赋值。这也是物体如此强大的原因。例如，如果您正在为一个网站创建多个用户，对象会很有帮助。他们会让你使用相同的代码从不同的用户那里获得相同的信息。

下面是一个 JavaScript 对象的代码示例:

```
var user = {
    username: "flpdcdg"
    password: "890iop"
    email: "flpdcdg@flpdcdg.com"
    isActive: function() {
        return (user.password === true ? "Y" : "N");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您想获得一个用户的用户名，您可以使用 user.username，您将获得您需要的值。或者如果你需要创建一个新用户，你可以像这样使用这个对象:

```
var user353 = new User();
user353.username = "djno";
user353.email = "djno@aol.com"; 
```

Enter fullscreen mode Exit fullscreen mode

你看，对象的存在是为了让你更容易重用代码并保持代码的一致性。对象所做的就是取名字并给它们赋值。这就是 user.username 会返回值“flpdcdg”的原因。

这也是为什么你能够在你的字符串变量上使用 substring 这样的方法。字符串是对象的一个常见例子。它们允许您使用字符串属性和方法，如 length 或 split。感觉 JavaScript 里的一切都是对象(因为确实是)！

关于对象，您需要记住的主要事情是，它们只不过是键值对。它们的名字都有赋值，这就是它们如此有用的原因。你不必记住索引或者一堆变量名。你只需要记住你想要的属性或方法的名字，它的值已经准备好了。

我希望这对您理解 JavaScript 对象有意义。这是另一个更容易理解而不是解释的概念。但是如果你有任何问题，请在评论中提问。

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)