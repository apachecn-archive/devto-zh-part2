# 如何做一个最小可行的功能切换，包括一个简单的代码示例

> 原文：<https://dev.to/geshan/how-to-do-a-minimum-viable-feature-switch-includes-a-simple-code-example-561d>

功能切换是一种在运行时关闭或打开工作软件功能的方式。我们可以使用配置或条件来实现这一点。功能切换也称为功能标志、功能切换和条件功能。在这篇文章中，我将揭示为什么要做[特性切换](https://en.wikipedia.org/wiki/Feature_toggle)以及如何用一个简单的 if 条件来做。

[![Minimum viable feature switch](../Images/56e42b7cac3da3e48dfcf74be907421e.png "Minimum viable feature switch")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z6uV-00U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geshan.com.np/images/feature-switch/feature-switch.jpg)

## 为什么要做一个特性开关/标志？

我第一次使用功能标志是在我们不得不部署`PayPal`作为支付方式的时候。我们希望确保它在生产中不会出现问题。我们当时是怎么做的，有一个[特征标志](https://launchdarkly.com/featureflags.html)，条件是用户电子邮件以`@your-company.com`结尾。

您可以出于各种原因进行功能切换，其中包括:

1.  一个关键特性需要投入生产，你需要在生产中测试它。除非像新的支付方式一样一切都好，否则不能发布给所有人。
2.  您希望分步骤部署一个重要的特性，直到最后一步完成，这个特性才算完整。
3.  您希望将您的更改发布到生产环境中，并与主分支合并。但是新的变化还没有公开。
4.  你不想花几个小时来解决合并冲突。您已经工作了几天，但是您的代码还没有合并到主分支中。
5.  如果您想在将来安排一个功能，您可以根据日期或时间切换功能。

## 如何做功能切换？

有许多方法来做这个帖子的功能切换，例如，我将添加一个新的支付方法到一个结帐。对于这个任务，我将使用一个简单的`if`条件。进行特征切换的其他方式可以是用一个`cookie`或者甚至一些`settings in the database`。

### 结账场景

简单结账已经有两种支付方式`Cash`和`CreditCard`。我们将把`PayPal`加进去。当前的概念验证实施仅显示订单总额。对于现金，它显示比信用卡多 5 英镑，因为它增加了`Cash On Delivery (COD)`费用。

这段代码是对我之前关于 Laravel 单元测试的文章的补充。如果你还没有读过，我强烈推荐你去读。这个例子也有[测试](https://github.com/geshan/laravel-unit-test-example/pull/4/files#diff-b3678da71dcc0bd1aa883f9f930c1ca5R34)。

### PayPal 结账示例

让我们以一个简单的功能切换为例，添加一种新的支付方式 Paypal。只有当您的电子邮件以`@gmail.com`结尾时，才能访问它。你应该使用`your-company.com`电子邮件，但作为一个例子，我会使用`gmail.com`。

### 示例代码

有一个简单的`Checkout`服务有一个`calculateTotal`方法。它根据付款方式和现在的电子邮件来计算总数。

特征标志/开关代码在第 21-23 行。它会检查付款方式是否为`PayPal`以及电子邮件是否不以`@gmail.com`结尾。Laravel [helpers](https://laravel.com/docs/5.3/helpers#method-ends-with) 中的
`endsWith`方法可以方便地做到这一点。所以只有当你的电子邮件以`@gmail.com`结尾时`PayPal`才可用。您可以检查此[拉动请求](https://github.com/geshan/laravel-unit-test-example/pull/4/files)的全部更改和相关测试。

### 关机

所以让我们假设，您在您的生产环境中对`PayPal`进行了密集测试。当您满意时，您只需移除条件并修复相关测试，就大功告成了。再次部署，进行最后一轮测试，然后`PayPal`投入生产，之后所有客户都可以使用它。

## 结论

停止做需要几周才能完成的大任务/单。应用功能切换，部署小东西。在没有人注意的情况下在生产中测试它们。

总是以一种隐蔽的方式测试生产中的关键事物，比如一种新的支付方式。练习功能切换，让您的代码通过您的工作流程进入生产环境。

> 希望您将受益于功能切换，并建议它来解决复杂的问题。

* * *

最初发表于[Geshan.com.np](https://geshan.com.np)，你可以在那里读到更多的东西。