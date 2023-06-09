# Rails 五年，重新评估

> 原文：<https://dev.to/epigene/five-years-with-rails-a-re-evaluation-fc5>

在接近我从事 Rails 工作五周年之际，在我职业生涯的一个关键点上，我想花一点时间来思考一下我喜欢 Rails 的地方，以及我希望我的工作在未来会朝着什么方向发展。

#### TL；速度三角形定位法(dead reckoning)

*   我将超越普通的 Rails MVC 结构进行设计，编写更简单的 Ruby。
*   我将在已经建立的领域中尝试替代方案(用`Sequel`代替`ActiveRecord`，`hamlit`代替`slim`)。
*   我将成为视图单元测试思维转变的一部分。

### 一切后端

我的主要职责是开发应用程序的后端。在我看来，这就是从数据传入开始(web 表单点击控制器动作)，接着是数据存储和操作(数据库架构、业务逻辑)，最后是准备响应数据。

在这方面，Rails 的控制器和模型对我很有帮助，尤其是 ORM (ActiveRecord)和回调。

然而，最近，为了与时俱进，我一直在用普通的 Ruby 类补充 Rails 提供的核心 MVC 结构。这些包括服务、交互器、验证器、表单对象、序列化器等。这样做可以隔离行为域，并形式化复杂的应用程序应该如何构造。我认为这种趋势将会继续，我们将会看到独立团队和整个行业的实验。我想到的一个行业规模的例子是[开拓者](http://trailblazer.to/)。

除此之外，我很喜欢与 Sidekiq 一起工作，特别是在 ActiveJob 上下文中，以实现进程异步，我认为我们应该停止使用`whenever`和 cron 进行调度，转而使用像 [`clockwork`](https://github.com/adamwiggins/clockwork) 这样的无依赖性解决方案。

### 有点做梦

有时我会打开 Ruby 工具箱网站,幻想如果我从头开始一个项目，我会使用什么样的堆栈。

如今，它自然会是 Ruby 2.5。*和 Rails 5.2。*，享受甜蜜，甜蜜的活动存储，但还有什么？

多年来，我一直意识到 ActiveRecord 是有缺陷的，但我的 SQL-fu 非常弱。我需要一张表格。
在 [orm 标签下](https://www.ruby-toolbox.com/categories/orm)有几个选择，我听说`Sequel`不错，但是我不愿意重新学习一个 orm。我想知道，有什么好处和权衡...

我最近开始研究的另一组宝石是[模板引擎](https://www.ruby-toolbox.com/categories/template_engines)。
我曾和 ERB、斯利姆以及最近的 HAML 合作过。我讨厌 ERB 的混乱，但是我知道它是最快的，尤其是使用`erubis`实现的时候。我喜欢斯利姆和 HAML 的简洁和基于缩进的模板，稍微倾向于 HAML 的多行 Ruby 块支持，但我知道 HAML 非常慢。然后我听说还有`hamlit`，它是 HAML 的一个非常快的变种，不支持一些更高级的功能。我应该以埃鲁比斯·ERB、斯利姆和哈姆莱特·HAML 为基准...

### 房间里的大象

这是一个大的。在我 5 年的经验中，我从未见过视图或 javascript 函数的单元测试。这太疯狂了。视图基本上是非常复杂的方法，我还没有为它们编写一个测试。我昨天才听说针对 Rails 的 [javascript 测试](https://www.ruby-toolbox.com/categories/javascript_testing)。
诚然，编写这些测试是前端开发人员的责任，但问题就在这里——前端开发人员忙于处理 JS、CSS、HTML 和资产，几乎没有时间和精力来为 RSpec 中的视图编写规范。

这必须改变。我认为有一个支点是可能的。我们实际上可以通过多写(或者开始写)来节省时间！)查看测试和 JS 单元测试以及远不如水豚的特性测试。基本上强烈倾向于单元测试而不是集成测试，这是 J.B. Rainsberger [在这个视频](https://vimeo.com/80533536#t=1355s)中极力提倡的观点。
事实上我认为水豚特性测试应该**只**用于测试 JS 驱动的复杂用户端行为，也许有比水豚更好的工具来编写 JS 集成测试。

我希望业界就此多谈一谈。

### 总之

令我兴奋的是，随着 Ruby 和 Rails 生态系统的成熟，开发人员有更多的时间关注他们代码的质量，并重新评估已经存在多年但可能不再适用的实践。

我可能会看看 ActiveRecord 的 ORM 替代品`Sequel`，并对三个流行的模板引擎进行基准测试。

最后，我将推动视图模板的覆盖率达到 90%以上，就像项目中的任何其他 ruby 代码一样。

除了 Rails 的 MVC，你们还在使用什么实践？除了普通的库，我还应该查看哪些其他的库？你是如何测试 Rails 项目中的 JS 和模板的？我想听听你的经历！