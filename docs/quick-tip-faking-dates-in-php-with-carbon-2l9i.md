# 快速提示:用碳在 PHP 中伪造日期

> 原文：<https://dev.to/nikoheikkila/quick-tip-faking-dates-in-php-with-carbon-2l9i>

通常情况下，我们的代码依赖于当前时间。大多数编程语言都能很好地处理日期时间解析，但是对更简单、更人性化的日期库的需求已经出现。我用过的最好的数据库之一是 Brian Nesbitt 的 [Carbon](https://carbon.nesbot.com/) ，它可以被简单地描述为 PHP 对人类的`DateTime`。如果您正在使用 Laravel 开发任何复杂的东西，这是一个必须拥有的工具。

假设您的项目包含一个例程，它需要在数据库*中保存一个新行，但是*只在指定的工作日(没有周末，没有公共假日)。例如，通过调用`Carbon::today()`来实现日期验证是很简单的，它返回一个表示当天的新对象。

为了断言您的逻辑按预期工作，您应该像往常一样编写单元测试。但是，如果当天不是工作日，您可能会遇到单元测试失败的情况。接下来，假设您正在实践连续交付——就像您可能应该做的那样——并且由于一个失败的管道，您直到明天才能将一个关键的修复部署到生产中。你将如何向客户解释这一点？您可能会将这个测试标记为未完成，这样即使单个单元测试失败，整个管道也可以通过。

幸运的是，Carbon 为在单元测试中伪造日期提供了一个简单的解决方案。你只需要用 [`setTestNow()`](https://carbon.nesbot.com/docs/#api-testing) 的方法模拟当前日期到一个给定的碳实例，比如:

```
// Set test date to 12:00 on 18th June, 2018
$testDate = Carbon::create(2018, 6, 18, 12);
Carbon::setTestNow($testDate);

// Execute the logic
BusinessClass::doStuff();

// Check the results
$this->assertDatabaseHas('stuff', [
    // The data you need...
]);

// IMPORTANT: Reset the date to not affect the next test!
Carbon::setTestNow(); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，在测试中伪造日期非常容易。只是不要忘记重新设置日期，否则你可能会经历更多的失败测试。