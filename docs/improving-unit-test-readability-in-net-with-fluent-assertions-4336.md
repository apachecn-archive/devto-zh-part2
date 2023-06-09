# 中提高单元测试的可读性。Net 与流畅的断言

> 原文：<https://dev.to/matheusrodrigues/improving-unit-test-readability-in-net-with-fluent-assertions-4336>

单元测试中的断言是我们验证测试结果是否如我们所料的阶段。这个阶段很简单，通常只有一行。但是可读性不是很好，因为它看起来是这样的:`Assert.Equal(“ExpectedResult”, “ActualResult”)`。

预期结果和实际结果参数没有逻辑顺序，这总是让我困惑。可读性的另一个方面是它不是人类可读的。如果你试着大声读出这样的代码，听起来就不那么好了。

为了解决这个问题，人们创建了一些框架来提高断言的可读性，比如,[Fluent Assertions](https://fluentassertions.com/)framework for . net。顾名思义，这个框架使用一个 Fluent 接口来创建可读的断言。

在这篇文章中，我将展示流畅的断言如何提高单元测试的可读性。

[继续阅读...](https://www.matheus.ro) [提高单元测试的可读性。Net 加上流畅的断言](https://www.matheus.ro/2018/04/16/improving-unit-test-readability-in-net-with-fluent-assertions/)