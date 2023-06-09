# 用 Python 实现一个插件系统

> 原文：<https://dev.to/alysivji/implementing-a-plugin-system-in-python-19mp>

*这篇[帖子](https://alysivji.github.io/simple-plugin-system.html)最初发表于 [Siv 脚本](https://alysivji.github.io/)T5*

7 月，我发布了我的第一个开源项目。这是一个 [apispec](https://github.com/marshmallow-code/apispec) 插件，为 [Falcon web 应用](https://github.com/falconry/falcon)生成 [OpenAPI 规范](https://swagger.io/docs/specification/about/)(又名 Swagger docs)。

Apispec 的设计使得为特定用例扩展核心功能变得容易。我扩展了`apispec.BasePlugin`类，重写了几个方法，然后就完成了。我不得不深入 apispec 内部来弄清楚事物是如何连接在一起的，但是在已经存在的基础上进行构建是很容易的

这让我开始思考如何在我自己的应用程序中实现一个插件系统。创建插件架构？听起来很难。一个先进的计算机科学概念。

我仔细考虑了一些基于我以前使用的软件的不同实现。我意识到这不是一个难题。像编程中的其他事情一样，一旦我们将问题分解成更小的块，我们就可以清楚地推理实现细节。

我们认为事情比看起来更困难。对于我们以前没有见过的问题，更是如此。后退一步。深呼吸。把问题分解成更小的部分。你能行的。

[![Motivational You Got This poster with dog](img/1bed31e2248b025a508181017a0a4baf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CR7jO60Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alysivji.github.img/30-39/35_you_got_this.jpg)

在这个[快速点击](https://alysivji.github.io/category/quick-hits.html)中，我们将通过一个简单的插件系统实现。

* * *

## 背景

> 插件是一种软件组件，它为现有的计算机程序增加了特定的功能。当一个程序支持插件时，它就能实现定制化

使用插件框架构建应用有很多好处:

*   第三方开发者可以创建和扩展你的应用
*   新功能更容易开发
*   您的应用程序变得更小，更容易理解

### 样本申请流程

我们有一个程序，它启动，做一些事情，然后退出。

[![Program Flow](img/3538d41c92276f3636c4fc9a88941221.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uYZaVcuO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alysivji.github.img/30-39/35_program-workflow.png)

### 插件架构工作流程

我们将业务逻辑重构为一个插件框架，可以运行注册的插件。插件需要满足我们的框架定义的规范才能运行。

[![Program flow with Plugin](img/8c68f1ba3ae85025344ab657404822d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g2ELz94k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alysivji.github.img/30-39/35_program-workflow-plugin-system.png)

* * *

## 玩具举例

让我们实现一个玩具示例，其中我们有一个插件框架来打印到控制台。我们的项目将有以下结构:

```
. ├── internal.py  #  internal business logic
├── external.py  #  contains user-created plugins
└── main.py      #  initialize and run application 
```

Enter fullscreen mode Exit fullscreen mode

### 内部

这个模块包含应用程序类和一个内部插件。

```
# internal.py 
class InternalPrinter:
    """Internal business logic"""
    def process(self):
        print("Internal Hello")

class MyApplication:
    """First attempt at a plugin system"""
    def __init__(self, *, plugins: list=list()):
        self.internal_modules = [InternalPrinter()]
        self._plugins = plugins

    def run(self):
        print("Starting program")
        print("-" * 79)

        modules_to_execute = self.internal_modules + self._plugins
        for module in modules_to_execute:
            module.process()

        print("-" * 79)
        print("Program done")
        print() 
```

Enter fullscreen mode Exit fullscreen mode

### 外部

```
# external.py 
class HelloWorldPrinter:
    def process(self):
        print("Hello World")

class AlohaWorldPrinter:
    def process(self):
        print("Aloha World") 
```

Enter fullscreen mode Exit fullscreen mode

### 主

在这个模块中，我们用想要启用的外部插件运行我们的应用程序实例。

```
# main.py 
from internal import MyApplication
from external import HelloWorldPrinter, AlohaWorldPrinter

if __name__ == "__main__":
    # Run with one plugin
    app = MyApplication(plugins=[HelloWorldPrinter()])
    app.run()

    # Run with another plugin
    app = MyApplication(plugins=[AlohaWorldPrinter()])
    app.run()

    # Run with both plugins
    app = MyApplication(plugins=[HelloWorldPrinter(), AlohaWorldPrinter()])
    app.run() 
```

Enter fullscreen mode Exit fullscreen mode

### 讨论

该应用程序的插件框架适用于内部和外部插件。我们在应用程序代码中定义内部插件。外部插件在运行时被初始化并传递给应用程序。

每个插件都继承自基本 Python 对象，并有一个`process()`方法。没什么复杂的，想让这个例子尽量简单。

我们可以通过调用应用程序的`run()`方法来运行我们的插件。这个方法遍历所有插件，并调用每个实例的`process()`函数。正如我们从上面的输出中看到的，插件是按照与列表相同的顺序执行的。

### 跑步玩具应用

```
$  python main.py
Starting program
------------------------------------------------------------------------------------
Internal Hello
Hello World
------------------------------------------------------------------------------------
Program done

Starting program
------------------------------------------------------------------------------------
Internal Hello
Aloha World
------------------------------------------------------------------------------------
Program done

Starting program
------------------------------------------------------------------------------------
Internal Hello
Hello World
Aloha World
------------------------------------------------------------------------------------
Program done 
```

Enter fullscreen mode Exit fullscreen mode

### 真实世界的例子

该模式可以与[适配器模式](https://en.wikipedia.org/wiki/Adapter_pattern)结合使用，以简化应用程序开发。

假设我们有大量的外部客户需要与我们交互。每个 API 都是不同的，但是我们需要为每个客户端执行的任务是相同的。

一种可能的实现是围绕每个客户端 API 编写一个适配器，从而产生一个公共接口。接下来，我们可以利用插件框架来解决我们的业务问题，然后我们可以使用插件来使它为我们所有的客户端工作。

这是对解决方案的高度描述。我将实现作为一个练习留给读者。

* * *

#### 附加资源

*   一个简单的插件框架
*   插件库
    *   [插件](https://github.com/pytest-dev/pluggy/)
    *   [yapsy](https://github.com/tibonihoo/yapsy)
    *   [straight.plugin](https://github.com/ironfroggy/straight.plugin)