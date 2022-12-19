# Masonite 框架教程系列第 2 部分-路由

> 原文：<https://dev.to/masonite/masonite-framework-tutorial-series-part-2---routing-1ak3>

[![](img/f04e157e46e02aa875991a0c38a3633b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gl2kjney--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/90b7vhkjoxgu022q7vwd.png)

# 简介

现在我们已经完成了安装，我们的 [Masonite](http://github.com/masoniteframework/masonite) 应用程序正在运行，让我们为我们的应用程序添加一些路由。

同样，在本教程中，我们不会构建特定的应用程序。我们将只详细介绍路线的每一部分，并通过它与我们交谈，解释注意事项，显示不同的选项等。

在这个系列的最后，我将制作一个视频教程，使用这些基于文本的教程作为脚本。如果你不想错过这个或下一个教程部分，然后给一个跟随！

因此，一个简单的路由需要 3 个基本部分:请求方法、URI 和控制器。我们将在后面的系列教程中讨论控制器，但是让我们把重点放在深入挖掘具体的路线上，然后我们可以把这些知识与控制器联系起来。

# 创建我们的路线

所有路线都位于`routes/web.py`。所有应用程序都有一些不同的路由概念。我们可能有 API 路由、HTTP 路由、资源路由、内部路由。因此，Masonite 分解了所有正常的前端 HTTP 路由，并建议将其放在 routes 常量内的文件中。

在这个文件中，我们会看到一些类似于
的代码

```
from masonite.routes import Get, Post

ROUTES = [
    Get().route('/', 'WelcomeController@show').name('welcome')
] 
```

Enter fullscreen mode Exit fullscreen mode

最简单的形式是，您将使用它来创建路线。让我们再细分一下。

我们有一个`Get()`类告诉这个特定的路由它是一个..你猜对了..找到路线。这个类有两个参数，URI(以一个`/`开始)和一个字符串，这个字符串默认指向一个在`app.http.controllers.WelcomeController`中叫做`WelcomeController`的类。不是所有的控制器都需要在这个目录中，但这是默认的。我们将在后面的部分讨论如何改变这一点。

最后，我们有一个接受简单字符串的`name()`方法。这个字符串可以是你想要的任何东西，有些人可能会这样做:

```
..().name('welcome')
..().name('welcome.home')
..().name('welcome:message') 
```

Enter fullscreen mode Exit fullscreen mode

语法真的取决于你，以及什么对你和你的团队最有效。它只是一个字符串，如果我们计划获取它或重定向到它，它就用来标识该路由。

# HTTP 方法

我们不仅仅局限于获取和发布。我们可以使用任何请求方法:

```
from masonite.routes import Get, Post, Put, Patch, Delete

ROUTES = [
    Get().route('/', 'WelcomeController@show').name('welcome')
    Post().route('/create', 'WelcomeController@create').name('welcome.create')
    Put().route('/update', 'WelcomeController@update').name('welcome.update')
    Patch().route('/update', 'WelcomeController@update').name('welcome.patch')
    Delete().route('/delete', 'WelcomeController@delete').name('welcome.delete')
] 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在构建一个 API 端点，请随意使用其中的任何一个。如果你对用 Masonite 构建 API 感兴趣，你也可以看看 Masonite 入口包。

# 中间件

像任何框架一样，Masonite 有两个中间件概念。第一个概念是路由中间件，另一个是 HTTP 中间件。由于这是一个路由教程，我们将只讨论路由中间件，但将在中间件教程中进行更详细的讨论。

所有的中间件都在`config/middleware.py`中定义，并且可以在我们上面的路线中使用，就像这样:

```
..().name('welcome').middleware('auth', 'another', 'more') 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过简单地将它们作为参数传递来指定要连续运行的中间件。

# 列表

当 Masonite 将这些路线加载到容器中时，它会将它们展平。这增加了对在`ROUTES`列表中嵌套列表的支持，比如:

```
ROUTES = [
    Get('/', 'WelcomeController@show').name('welcome'),
    [
        Get.route('/another', 'AnotherController@show').name('another'),
    ]
] 
```

Enter fullscreen mode Exit fullscreen mode

这一点都不太有用，但是我们可以做的是用一个类方法来返回一个列表，就像这样:

```
# inside somefile.py 
class AwesomeRoutes:

    def routes(self):
        return [
            Get().route('/another', 'AnotherController@show').name('another'),
        ] 
```

Enter fullscreen mode Exit fullscreen mode

然后将其导入并在此路线列表中使用:

```
from somfile import AwesomeRoutes
ROUTES = [
    Get().route('/', 'WelcomeController@show').name('welcome'),
    AwesomeRoutes().routes(),
] 
```

Enter fullscreen mode Exit fullscreen mode

这对于将路线分成多个文件或创建需要将路线添加到应用程序的 Masonite 包非常有用。

# 路线助手

我个人不使用这些类，因为我不喜欢它的外观。相反，我使用路由助手，它是基于类的方法的简单简化，可以这样使用:

```
from masonite.helpers.routes import get, post

ROUTES = [
    get('/', 'WelcomeController@show').name('welcome')
] 
```

Enter fullscreen mode Exit fullscreen mode

只是一点速记，但我认为它要干净得多。如果你喜欢大写的基于类的方法，并且两者都想要一点，我们可以这样做:

```
from masonite.helpers.routes import get as Get

ROUTES = [
    Get('/', 'WelcomeController@show').name('welcome')
] 
```

Enter fullscreen mode Exit fullscreen mode

## 路线组

我们还可以指定路由组，这只是一个可以使用的路由列表..分组。如果你有一堆相似的路线，并且想让你的路线更干燥，这是很有用的。

举这个例子:

```
from masonite.helpers.routes import get

ROUTES = [
    get('/dashboard/user', ...)
    get('/dashboard/user/edit', ...)
    get('/dashboard/user/create', ...)
    get('/dashboard/profile', ...)
] 
```

Enter fullscreen mode Exit fullscreen mode

注意这里所有的路线看起来都非常相似。在这种情况下，我们应该创建一个路由组:

```
from masonite.helpers.routes import get, group

ROUTES = [
    group('/dashboard', [
        get('/user', ...),
        get('/user/edit'),
        get('/user/create'),
        get('/user/profile'),
    ])
] 
```

Enter fullscreen mode Exit fullscreen mode

# 参数

如果我们不能动态获取 URI 的某些部分，这个路径功能就没有用了。为了做到这一点，我们只需要在后面要用到的变量名旁边加上一个`@`符号。一个很好的例子是:

```
from masonite.helpers.routes import get

ROUTES = [
    get('dashboard/user/@id/edit', ...),
] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在我们的路径列表中有一个`@id`。我们现在将能够使用`id`键来获取该值。我们将在下一个教程讨论控制器时讨论如何获取这个值。

这将匹配几乎任何你扔在那里，包括数字，字母和字母数字。在这种情况下，我们可能只想匹配整数，所以让我们指定:

```
from masonite.helpers.routes import get

ROUTES = [
    get('dashboard/user/@id:int/edit', ...),
] 
```

Enter fullscreen mode Exit fullscreen mode

如果`id`不是整数，这个`@id:int`将告诉 Masonite 不要匹配该路线。这将匹配`/dashboard/user/1/edit`但不匹配`/dashboard/user/joe/edit`

我们可以对字母数字串做同样的事情:

```
from masonite.helpers.routes import get

ROUTES = [
    get('dashboard/user/@id:string/edit', ...),
] 
```

Enter fullscreen mode Exit fullscreen mode

这将匹配`/dashboard/user/joe/edit`但不匹配`/dashboard/user/1/edit`。

# 更深模块控制器

控制器基本上就是一个包含一些方法的类。如果你来自 Django，那么 Masonite 控制器就是一个简单的基于函数视图的类。并非所有的控制器都位于`app.http.controllers`中，所以你可以将它们移动到几个地方。大多数人做的是把它们放入他们自己的类别，你有一个这样的结构:

```
app/
  http/
    controllers/
      Dashboard/
        SomeController.py
      Admin/
        SomeController.py
      Leagues/
        SomeController.py
      BaseController.py 
```

Enter fullscreen mode Exit fullscreen mode

这是完全没问题的，可能是应该走的路。在这种情况下，我们可以简单地在控制器结构中指定一个更深的模块:

```
from masonite.helpers.routes import get

ROUTES = [
    get(..., 'Dashboard.SomeController'),
] 
```

Enter fullscreen mode Exit fullscreen mode

注意这里的`.`符号。这告诉 Masonite 检查`app.http.controller`目录，但也检查`Dashboard`模块内部，而不是那里的基目录。

# 全局控制器

有两种方法可以全局指定控制器。如果你想一起离开`app.http.controller`目录，而是想把你的控制器放入这样的结构中:

```
app/
  http/
    controllers/
      BaseController.py
controllers
  Dashboard/
    SomeController.py
  Admin/
    SomeController.py
  Leagues/
    SomeController.py 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以指定一个“全局”控制器，它基本上只是寻找控制器，就像你正在导入模块一样:

```
from masonite.helpers.routes import get

ROUTES = [
    get(..., '/controllers.Dashboard.SomeController'),
] 
```

Enter fullscreen mode Exit fullscreen mode

注意简单的`/`前缀。

# 基于类的控制器

如果你想完全摆脱基于字符串的控制器的世界，尤其是当我开发包的时候，我们可以简单地导入类，并且指定它不带字符串:

```
from masonite.helpers.routes import get
from app.http.controllers.WelcomeController import WelcomeController

ROUTES = [
    get(..., WelcomeController.show),
] 
```

Enter fullscreen mode Exit fullscreen mode

注意我们没有实例化任何东西。Masonite 会自己找到类、方法和模块。我个人更喜欢这种方法，因为它更加明确。我们不必担心字符串能够隐式地获取控制器的位置。

# 子域

子域对任何应用程序都非常有用，但我们实际上默认关闭了它们。这是因为一些像 Heroku 这样的部署服务部署到像`meadow-fever-18273.herokuapp.com`这样的东西上。这将在应用程序上设置一个子域，在设置自定义域名之前，您将无法显示任何路线。

## 激活子域

为了激活子域，我们只需要打开任何一个具有`wsgi=False`类属性的服务提供者，我们可以在引导方法中抛出这个属性:

```
def boot(self, Request):
    Request.activate_subdomains() 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们激活子域，我们现在可以使用它们了！

## 使用子域

我们现在可以简单地为每条路由设置域，但是使用`domain()`方法:

```
from masonite.helpers.routes import get

ROUTES = [
    get('/', 'WelcomeController@show').name('welcome'),
    get('/home', 'SomeController@show').domain('masonite').name('home'),
] 
```

Enter fullscreen mode Exit fullscreen mode

现在只有`/home`航线可以到达`masonite.website.com`而不是`website.com`。

更常见的用例是在所有域上指定一个路由，然后在代码库中获取该域。为此，我们可以使用星号通配符来捕捉所有子域:

```
from masonite.helpers.routes import get

ROUTES = [
    get('/', 'WelcomeController@show').name('welcome'),
    get('/home', 'SomeController@show').domain('*').name('home'),
] 
```

Enter fullscreen mode Exit fullscreen mode

这将捕获每个子域，但不会捕获`website.com`上的任何内容。

最后，我们可以指定一个我们不想连接的具体子域列表:

```
from masonite.helpers.routes import get

ROUTES = [
    get('/', 'WelcomeController@show').name('welcome'),
    get('/home', 'SomeController@show').domain(['api', 'slack']).name('home'),
] 
```

Enter fullscreen mode Exit fullscreen mode

这将在`api.website.com`和`slack.website.com`上找到，但在任何其他子域或主域上都找不到。在下一部分中，我们将解释如何在控制器中获取这些值。

* * *

路线到此为止！接下来，我们将讨论控制器的精彩世界，以及 Masonite 是如何从其他 Python web 框架中脱颖而出的。

一定要关注，这样你就不会错过，一定要点击查看 [Masonite！下次见！](http://github.com/masoniteframework/masonite)