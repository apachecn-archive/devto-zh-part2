# 使用 Rails 秘密武器:ActiveSupport::Notifications

> 原文：<https://dev.to/hugodias/using-rails-secret-weapon-activesupportnotifications-12d3>

[最初发布于我的博客](https://blog.hdias.dev/ruby-on-rails-active-support-notifications)

Ruby on Rails 有一个强大的武器，但并不是所有开发人员都知道它的存在。Rails 框架使用 active support Instrumentations 来处理应用程序运行时发生的事件。

Active Support 提供的工具 API 允许开发者提供其他开发者可以使用的钩子。在 Rails 框架中有几个这样的例子。有了这个 API，开发人员可以选择在他们的应用程序或另一段 Ruby 代码中发生某些事件时得到通知。

幸运的是我们可以随意使用这个工具。

### 工作原理

它看起来像一个`PUB/SUB`图案。

在软件架构中,“发布-订阅”是一种消息传递模式，消息的发送者(称为发布者)不将消息直接发送给特定的接收者(称为订阅者)。相反，发布的消息被分成几类，而不知道可能有哪些订阅者。类似地，订户表达对一个或多个类别的兴趣，并且仅接收感兴趣的消息，而不知道有什么发布者(如果有的话)。 - [维基百科](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)

订阅活动非常简单:

```
# config/initializers/events.rb
ActiveSupport::Notifications.subscribe "my_custom.event" do |*args|
  event = ActiveSupport::Notifications::Event.new(*args)
end 
```

Enter fullscreen mode Exit fullscreen mode

事件变量将包含类似这样的内容

```
=> #<ActiveSupport::Notifications::Event:0x0000559258433df8
 @children=[],
 @duration=nil,
 @end=2018-07-03 12:47:46 +0000,
 @name="my_custom.event",
 @payload={:foo=>"bar"},
 @time=2018-07-03 12:47:46 +0000,
 @transaction_id="37bb01f75132e0c0505publisher6"> 
```

Enter fullscreen mode Exit fullscreen mode

注意到`@payload`了吗？我们可以在事件中发送附加数据。

### 仪表化事件

有一个名为`instrument`的方法发布事件。

可以这样用:

```
ActiveSupport::Notifications.instrument "my_custom.event", { foo: "bar" } 
```

Enter fullscreen mode Exit fullscreen mode

简单对吗？

### 真实世界的例子

假设您有一个处理应用程序指标的外部服务，比如 Segment。木卫一，基恩木卫一，还有很多其他的。

您可以使用此模式将事件发送到其中一个服务。

```
# config/initializers/events.rb
# We can use regex to subscribe!
ActiveSupport::Notifications.subscribe /metrics/ do |*args|
  event = ActiveSupport::Notifications::Event.new(*args)
  MyMetricsService.send(event.name, event.payload)
end

# app/controllers/my_controller.rb
def create
  # some code
  ActiveSupport::Notifications.instrument "metrics.item_purchased", { uid: item.id }
end 
```

Enter fullscreen mode Exit fullscreen mode

您可以在此阅读更多关于[动态支持工具的信息](http://guides.rubyonrails.org/active_support_instrumentation.html)

希望有帮助:)

* * *

Unsplash 上由 Ciprian Boiciuc 拍摄的照片