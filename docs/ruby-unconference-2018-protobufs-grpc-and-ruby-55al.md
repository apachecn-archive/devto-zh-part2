# Ruby Unconference 2018 Protobufs、gRPC 和 Ruby

> 原文：<https://dev.to/dmgarland/ruby-unconference-2018-protobufs-grpc-and-ruby-55al>

我最喜欢的 Ruby 聚会之一是伦敦 Ruby Unconference，它发生在上周六，就在白金汉宫附近。

在会议上，观众大多是被动的，发言者代表知名科技公司，暗地里(或公开地)推销某些东西，而“无会议”则试图做相反的事情:鼓励与会者参与。

无政府状态的真实演示，会议日程安排在活动当天上午。任何人都可以提出场次，观众用脚投票。通过这种方式，即使是一个粗略的会议想法也可以进入议程。

我喜欢的是，我可能会想到一些我想在 Ruby 中探索的东西，将其作为一个会话，并花时间与志同道合的人一起解决它。

## Enter Protobuf

我最近在一个金融科技项目中负责学习 Google 的 protobuf 定义语言，用于描述 web 服务中的二进制格式。在高交易量的实时交易中，时间至关重要。算法交易依靠数据的微小变化来做决定，每一秒钟都是你的竞争优势。

我来自 Ruby 和 Rails 背景，我认为我们很久以前就已经消灭了 RPC / SOAP / XML 这条巨龙。HTTPS 和 JSON 格式在整个 web 上很流行，RESTful 约定使得 API 的设计变得简单且可预测。然而，当速度是关键时，protobufs 是非常值得一看的。我想知道工具和工作流程是否经得起 Rails、Padrino 和 Sinatra 设定的标准，或者它是否会回到我的 Enterprise JavaBean 时代。

尽管我曾在 FinTech 项目中与 Go 合作过，但 protobufs 的一个优点是，通过用一种标准语法定义 API 端点和它们传递的消息，可以用各种语言编写客户端和服务，并且它们之间仍然可以互操作。

因此，我决定在 Ruby 中推出一个关于 gRPC 和 protobufs 入门的会议，看看它与我习惯的 Rails RESTful 方法相比如何。

## 入门指南

因为我正在参加一个活动，所以我想我应该使用参加活动的例子来演示一个日历应用程序，使用 grpc 来处理后端代码。由于时间限制在 45 分钟左右，我想我可以演示一下如何将事件添加到日历中，也许还可以展示我们如何检索它们。

这个例子中的所有代码都在我的 GitHub 上

最初，我创建了一个 git repo 和一些文件夹来将我的原型定义与 Ruby 代码分开。

```

git init calendar_demo
cd calendar_demo
mkdir proto
mkdir lib

```

为了开始充实我们的日历 web 服务可以做什么，我从 protobuf 定义文件开始，这是一个单独的 API 语言定义，它定义了 API 将支持哪些端点，以及它们将相互传递哪些消息。这不同于 RESTful 方法，在 RESTful 方法中，我们可能从表示`Event`的资源开始，并期望 API 端点按照约定匹配。

protobuf 的第一稿看起来是这样的:

```
  syntax = 'proto3';

service Calendar {
  rpc AddEvent(Event) returns (EventAdded) {}
}

message Event {
  string name = 1;
  string date = 2;
}

message EventAdded {
  bool added = 1;
} 

```

这里，我们定义了一个名为`Calendar`的 web 服务，它代表了整个 API。在其中，我们可以有许多端点，它们执行不同的任务或服务。这有点类似于一个类和它的方法；只不过方法调用是由底层的 protobuf 二进制消息通过传输层触发的(稍后将详细介绍)。

我们的`AddEvent`端点需要知道我们正在添加的事件的信息。我们将此定义为*消息*，我将其命名为`Event`。每条消息由许多字段组成，每个字段都有基本的数据类型，如字符串、浮点、整数；可以序列化(转换成二进制)并在另一端反序列化的类型。为了简单起见，我只是把一个事件看作是存储一个名称(我们正在做的事情)和一个日期(我们正在做的事情)。

请注意，邮件中的每个字段都按升序编号。这是为了保持与使用我们服务的其他客户端/服务的向后兼容性，但可能没有最新的代码库，而不是改变消息的格式。通过这种方式，我们可以添加编号更大的新字段，或者更改它们的类型，而不会混淆其他客户端和服务。

作为 RESTful 开发人员，我们可能期望这样一个端点简单地确认事件被成功添加，可能带有 HTTP 状态代码 200 或 201。使用 protobufs，我们也需要显式地定义响应，所以我在这里定义了另一个消息`EventAdded`，它封装了一个布尔值，指示事件是否被添加。实际上，我们可能希望在这里包含更多的信息，例如事件是立即添加的还是排队等待以后处理的。

因此，有了这个相当简洁的定义，我们已经为一个端点定义了一个请求/响应，该端点被设计为向我们的日历添加一个事件。

## 生成客户端和服务器

虽然 protobuf 的定义简短易读，但在我们付诸实践之前，它不会为我们做太多事情。这意味着我们需要创建实现这个定义的代码，负责序列化/去序列化、发送/接收消息以及路由到正确的端点。幸运的是，使用 grpc 工具集已经为我们完成了大部分艰苦的工作，让我们只关注我们的业务逻辑，并根据事件和日历进行思考。

使用`grpc-tools` gem，我们可以避开`protoc`编译器和插件的设置和安装，从一个预制的二进制文件开始。我把它和`grpc`依赖项一起添加到我的`Gemfile`中。

```
  source "https://www.rubygems.org"

gem 'grpc'
gem 'grpc-tools' 

```

然后，我可以用几个命令生成代码:

```
  bundle
grpc_tools_ruby_protoc -Iproto calendar.proto --ruby_out=lib --grpc_out=lib 

```

第二个命令要求 bundler 管理的`protoc`编译器从我们的 API 定义中生成客户机和服务器存根，在 proto 文件夹中查找 protobuf 文件，并在 lib 文件夹中输出结果代码。

如果您仔细查看生成的代码，您会发现它生成了:

*   `calendar_services.pb`，包含一个以我们的服务命名的模块，注册我们的`AddEvent`端点
*   `calendar_pb.rb`，记录我们已经定义的事件。

## 定义服务器

最终，我们不能让 grpc 工具集为我们定义一切；否则我们就失业了！我们需要使用生成的服务作为模板，定义当服务接收到`AddEvent`消息时会发生什么。我在`lib/calendar_server.rb`中存储了以下代码:

```
  require 'grpc'
require 'calendar_services_pb'
require 'calendar_pb'

module Calendar
  class Server < Service
    def add_event(event, _call)
      # TODO - Business logic!
    end
  end
end 

```

我使用了良好的老式继承来子类化`Service`，继承了工具集生成的所有方法和功能。然后我们需要为我们定义的每个端点提供方法，所以我写了一个`add_event`方法。该方法被传递了一个封装了我们的`name`和`date`字段的`Event`对象，以及一个封装了请求的`_call`。

虽然这是一个很好的开始，但是我们还没有任何东西来实现我们的服务，或者任何东西来定义*传输层*——也就是说，服务如何发送和接收消息。下面的片段是从 grpc 教程中推荐的，作为一种入门方式；实际上，这可能在其他地方被调用，但是现在我将把它添加到服务器类中。

```
  require 'grpc'
require 'calendar_services_pb'
require 'calendar_pb'

module Calendar
  class Server < Service
    def add_event(event, _call)
      # TODO - Business logic!
    end
  end
end

addr = "0.0.0.0:8080"
s = GRPC::RpcServer.new
s.add_http2_port(addr, :this_port_is_insecure)
puts("... running insecurely on #{addr}")
s.handle(Calendar::Server.new)
s.run_till_terminated 

```

这只是足够运行服务器的代码，但它不会做太多；任何连接的客户端都会永远挂起，因为我们的`add_event`方法不返回任何东西。让我们添加一点业务逻辑，天真地将我们的`Event`存储在一个数组中:

```

require 'grpc'
require 'calendar_services_pb'
require 'calendar_pb'

module Calendar
  class Server < Service
    def initialize
      @events = []
    end

    def add_event(event, _call)
      @events << event

      return EventAdded.new(added: true)
    end
  end
end

addr = "0.0.0.0:8080"
s = GRPC::RpcServer.new
s.add_http2_port(addr, :this_port_is_insecure)
puts("... running insecurely on #{addr}")
s.handle(CalendarServer.new)
s.run_till_terminated

```

现在我们已经有了服务器的第一个草案，您可能会考虑将您的终端分成两半，并使用以下代码运行:

T2`ruby -Ilib lib/calendar_server.rb`

您应该看到服务正在运行的警告，尽管不安全。它坐在一个无限循环中等待你的请求到达！

## 发送客户端请求

从开发人员的角度来看，protobufs 的一个缺点是，作为一个低级的二进制协议，我们不能使用类似于`curl`的东西来测试我们的 API。相反，我们需要使用提供的客户端存根来创建一个 Ruby 客户端。在实践中，这些代码也可能被嵌入到一个更大的应用程序中，但是现在用下面的代码在`lib`文件夹中创建一个`calendar_client.rb`就足够了:

```
  require 'grpc'
require 'calendar_services_pb'
require 'calendar_pb'

stub = Calendar::Stub.new('localhost:8080', :this_channel_is_insecure)
event = Event.new(name: 'Ruby Unconference', date: '2018-10-06')
response = stub.add_event(event)
puts response.added 

```

这里，我们正在实例化一个合适的客户端，它知道如何与我们的端点“对话”,并向它传递消息。客户端处理响应，并给我们一个对象，该对象为`added`字段提供一个 getter，因此我们可以看到事件是否被添加。

```
ruby -Ilib lib/calendar_client.rb 
```

## 流式事件

protobufs 的一个很好的特性是它支持流式 API，这对于逐步处理大量数据非常好，而不是构建巨大的响应并让客户端等待。

为了说明这一点，我演示了如何在名为`ShowEvents`的新 API 端点中实现每月重复事件。这个端点将一次流处理一个事件，如果需要，从初始事件生成重复事件。

我首先用一个新的端点扩展了我们的 protobuf 定义。要使这个端点成为一个流 API，我们需要做的就是在返回值之前添加`stream`指令。我添加了一条`EventRange`消息，它指定了一个日期范围供选择，我还更新了`Event`消息，以包含一个循环字段。

```

syntax = 'proto3';

service Calendar {
  rpc AddEvent(Event) returns (EventAdded) {}
  rpc ShowEvents(EventRange) returns (stream Event) {}
}

message Event {
  string name = 1;
  string date = 2;
  bool recurring = 3;
}

message EventAdded {
  bool added = 1;
}

message EventRange {
  string from = 1;
  string to = 2;
}

```

由于 API 定义已经改变，我们需要像以前一样更新生成的代码。这引起了一个重要的考虑，记录在生成的代码中，我们不能手工编辑生成的文件；否则，这些更改将在我们(或我们的队友)下一次更新 protobuf 定义文件时被覆盖。

运行生成器之后，我们只需要向我们的服务器(`lib/calendar_server.rb`)添加一个新方法来支持新的端点(为了简洁起见，我将省略文件的其余部分)。Ruby grpc 实现很好地利用了这里的`Enumerator`类，允许我们将流定义为一个`Enumerator`——也就是说，我们定义了序列，底层代码将重复调用`each`，直到它用完。因此，我们可以在一个流中轻松地返回数组中的每个事件:

```

def show_events(search, _call)
  @events.each
end

```

使用内置的 select 方法，我们可以使用 EventSearch 消息中提供的日期轻松地添加日期范围过滤:

```

def show_events(range, _call)
  from = Date.parse(range.from)
  to = Date.parse(range.to)

  @events.select {|event| Date.parse(event.date) <= to && Date.parse(event.date) >= from }.each
end

```

由于 Ruby 是弱类型的，在`String`和`Date`之间有相当多的类型转换，这使得代码有点重复。尽管 Ruby 对这些考虑很矛盾，但当涉及到 API 设计时，我们会更多地使用强类型代码。

我发现避免这种转换的一个好方法是打开提供的`Event`类，并添加一个 getter 来提供底层数据的 Ruby `Date`等价物。这允许我们继续使用底层序列化的字符串，但在我们的逻辑中提供使用日期

```
  class Event
  def actual_date
    @actual_date ||= Date.parse date
  end
end

def show_events(range, _call)
  from = Date.parse(range.from)
  to = Date.parse(range.to)

  @events.select {|event| event.actual_date <= to && event.actual_date >= from }.each
end 

```

使用`Enumerator`类的一个有点晦涩但非常强大的特性，我们还可以将一个块传递给`Enumerator`类的`initialize`方法，并定义每次迭代产生的值。当我们想要定义自己的自定义序列时，这是很有用的。

使用这种方法，我测试了一个事件是否重复发生，如果是，将会比原来的日期提前一个月生成新的日期。将这段代码放在一个`while`循环中意味着我可以在我的流中生成任意数量的事件:

```

def show_events(range, _call)
  from = Date.parse(range.from)
  to = Date.parse(range.to)

  Enumerator.new do |y|
    @events.select {|event| event.actual_date <= to && event.actual_date >= from }.each do |event|
      y << event
      while event.recurring && event.actual_date < to
        # assume monthly its only a demo :)
        event = Event.new(name: event.name, date: (event.actual_date >> 1).to_s, recurring: true)
        y << event
      end
    end
  end
end

```

此外，客户端代码以 Ruby `Enumerable`的形式提供给流，这使得迭代流响应变得很简单:

```

range = EventRange.new(from: '2018-01-1', to: '2018-12-31')
events = stub.show_events(range)
events.each do |event|
  puts "#{event.name} is happening on #{event.date}"
end

```

## 有多快？

我们可以看到，使用 grpc 方法非常简单，但是它有多快呢？与 XML/SOAP web 服务相比，我们的标准 HTTP/JSON API 的部分问题与 HTTP/JSON 本身提出要解决的问题相同:序列化时间。无论是在网络方面，还是在序列化和反序列化数据的计算能力方面，额外的带宽量，无论是 HTTP 头还是 JSON 结构，都是设计 API 时必须考虑的开销。

虽然 JSON 比 XML 更简单、更快，但我做了一个[快速基准测试](https://github.com/dmgarland/calendar-grpc-demo/tree/benchmark)来看看我们的日历应用程序与用 Sinatra 编写的 API 相比如何。注意，两者都使用 HTTP 传输，所以这实际上只是比较两个系统的 JSON 开销和内部结构。下面是发送和接收 1，000，000 个向我们的 Ruby 数组添加事件的请求的结果:

#### 处理 100 万个请求的时间(秒)

|用户|系统|总计|实际|需求/秒|
| 113.786016 | 27.136001 | 140.922017 |(342.784571)| 7092 |
| 300.176772 | 119.946387 | 420.123159 |(1050.995664)| 238 |

使用 protobufs，速度提高了 3 倍！grpc 以每秒超过 7，000 个请求的速度快速处理 JSON 序列化并获奖。

## 我学到了什么

*   GRPC 不是我们在 90 年代经历的 WSDL，肥皂地狱的翻版
*   使用和定义新服务非常容易
*   它是可互操作的，并且是将 Rails 应用分解成用其他后端编写的微服务的好方法，比如 Go
*   它比 JSON API 快 3 倍
*   在 hackdays 认识新朋友很有趣！:)

### 谢谢

这篇文章中的大部分信息都是受 grpc.io 上的[优秀教程的启发，该教程在 protobufs 上也有很好的链接和文档参考。](https://grpc.io/docs/tutorials/basic/ruby.html)

如果不是因为伦敦 Ruby Unconference，我可能不会花一个小时左右的时间来做这个演示，所以感谢海罗和 CodeScrum 举办这个活动。

如果这些对你来说没有任何意义，你需要看看我的 [Ruby 课程！](https://www.dangarland.co.uk/courses/essential-aspects-of-ruby.html)