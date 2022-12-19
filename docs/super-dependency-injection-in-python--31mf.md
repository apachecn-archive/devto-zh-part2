# Python 中的超级依赖注入

> 原文：<https://dev.to/incognitjoe/super-dependency-injection-in-python--31mf>

*最初发布于[我的博客](https://incognitjoe.github.io/)。*

这不是一篇关于我喜欢的一些伟大的依赖注入框架的博文。相反，它是关于使用`super()`方法来改变我们的类在不同环境中的行为。如果您熟悉 super 在单继承语言中的工作方式，这听起来可能有点疯狂——根据我在其他地方的经验，super 调用父类，所以如果您有 B 类扩展 A 类，并且 B 在任何地方调用 super，那么 A 类的方法就会被调用。然而，在 Python 中却不是这样——对于使用多重继承构造的类，Python 的方法解析顺序，也就是众所周知的 MRO，会动态变化以决定如何调用 super。实际上——如果你在你的类中使用 super，那么你的一个孩子调用 super，这个孩子的父母(你的类只有一个！)确定调用哪个方法。

迷茫？让我们来看一个例子，希望能澄清一些问题。

因此，很简单的情况是，我们正在构建某种类型的服务，该服务对其他服务进行 HTTP 调用。我们将使用[请求](http://docs.python-requests.org/en/master/)库，而不是编写我们自己的所有会话处理等等，但是我们确实想把一些样板文件，比如自定义标题、日志和错误处理放在一个地方，所以我们将子类化`Session` :

```
import requests

class CustomSession(requests.Session):
    def __init__(self):
        super().__init__()
        self.headers.update({'User-agent': 'CustomSession', 'Accept': 'application/json'})

    def get(self, url, **kwargs):
        print('Performing GET on {}'.format(url))
        resp = super().get(url=url, **kwargs)
        resp.raise_for_status()
        print('{} returned status: {}'.format(url, resp.status_code))
        return resp.json() 
```

Enter fullscreen mode Exit fullscreen mode

这样一来，我们将使用我们的 CustomSession 作为我们客户端的基础:

```
class MyAppClient(CustomSession):
    URL = 'https://myapp.corpdomain/'

    def get_stuff(self):
        resp = super().get(url='{}stuff'.format(self.URL))
        return resp 
```

Enter fullscreen mode Exit fullscreen mode

希望你实际的 API 看起来不会太差，但这不是这篇文章的重点。总之，完成之后，`MyAppClient().get_stuff()`向`https://myapp.corpdomain/stuff`发出 GET 请求，如果状态不是 2xx/3xx，抛出异常，并返回 JSON 响应。干净利落。然而，我们真的不希望我们的测试不断地进行实时 HTTP 调用，所以这可能会有点问题。让我们子类化 CustomSession，并创建一个 TestSession 来覆盖我们的`get()`，现在只返回一个空字典:

```
class TestSession(CustomSession):
    def get(self, url, **kwargs):
        print('TEST: app tried to GET {}'.format(url))
        return {} 
```

Enter fullscreen mode Exit fullscreen mode

然后，让我们执行创建一个测试类的复杂任务，该测试类包含 MyAppClient 中的所有方法，但是用对 TestSession:
的调用替换从 CustomSession 继承的`get()`调用

```
class TestMyAppClient(MyAppClient, TestSession):
    pass 
```

Enter fullscreen mode Exit fullscreen mode

现在当我们运行`TestMyAppClient().get_stuff()`时，我们看到:

```
TEST: app tried to GET https://myapp.corpdomain/stuff 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这里发生了什么？基本上 MRO *调用下一个方法，而不是我们类中的父方法*。如果我们检查 TestMyAppClient 的`help()`，我们可以看到解析顺序:

```
 |  Method resolution order:
 |      TestMyAppClient
 |      MyAppClient
 |      TestSession
 |      CustomSession
 |      requests.sessions.Session
 |      requests.sessions.SessionRedirectMixin
 |      builtins.object
 |  
 |  Methods inherited from MyAppClient:
 |  
 |  get_stuff(self) 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们有一个从 MyAppClient 继承的`super().get()`调用，*，但是* TestSession 是我们解析顺序中的下一个，所以调用的是`get()`而不是 CustomSession 的，尽管 MyAppClient 对 TestSession 一无所知。

当然，这不仅仅在测试中有用——您可以扩展您的 CustomSession 来获取和解析`application/xml`响应，而不是 JSON，或者用一个`DebugSession`来添加大量的日志记录来代替我们的小 print 语句——一旦您对确定 super 将如何解析感到满意，它就打开了各种可能性。