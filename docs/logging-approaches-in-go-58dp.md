# Go 中的日志记录方法

> 原文：<https://dev.to/chuck_ha/logging-approaches-in-go-58dp>

在一个清理周期中，我发现了 [glog](https://github.com/golang/glog) 库的一个简洁的特性，这让我想到了各种日志记录的方法。

这面旗帜引发了我的思考:

```
 -log_backtrace_at value
        when logging hits line file:N, emit a stack trace 
```

Enter fullscreen mode Exit fullscreen mode

我真的需要这个特性来处理我正在处理的票据，但是它不是我在其他日志库中见过的特性。`glog`认为日志记录应该是全球性的。您可以导入`glog`库，并在任何地方记录您需要的任何行。你不用代码来配置`glog`，而是在运行时用标志来配置它。这就是为什么 glog 要求你在使用之前运行`flag.Parse()`的原因。`glog`作为一名全球伐木工，能迅速完成工作。

另一方面，您可以将日志记录作为一种适当的依赖。记录器是函数的参数或存在于结构中。无论哪种方式，都可以通过代码进行配置，这使得编写单元测试更加容易。您不必求助于 [Go 的可测试示例](https://blog.golang.org/examples)来确保您的应用程序正确登录。当您依赖于特定方式的输出或者依赖于日志而不仅仅是调试输出时，这种方法是很好的。查看 [logrus](https://github.com/sirupsen/logrus) 获得一个很好的库，它很容易被包装成一个适当的日志依赖项。

几乎每个技术问题的答案都是，选择适合手头项目的方法。很可能你会在中间的某个地方结束，并使用两种类型的日志记录，这没问题。唯一重要的事情是像对待日志类型一样对待它。因此，继续前进，跳过全局记录器的单元测试，不要忘记通过注入日志依赖来为一个经过良好测试的库构建额外的时间。