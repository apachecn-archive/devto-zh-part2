# Masonite 框架教程系列第 4 部分-视图

> 原文：<https://dev.to/masonite/masonite-framework-tutorial-series-part-4---views-163c>

# 简介

[Masonite](http://github.com/masoniteframework/masonite) 采用传统的 MVC(模型-视图-控制器)架构。虽然大多数框架都使用这种架构模式，但是每个框架都有不同的主观解释。我们已经在之前的一个教程中学习了 C(控制器),所以现在我们来谈谈 V(视图)。

如果您来自 Django 这样的框架，那么您可能习惯于使用 Python 代码的视图，如函数(基于函数的视图)或 Python 类(基于类的视图)，但在 Masonite 中，视图只是一个 HTML 模板。在整个文档和这些教程中，“模板”和“视图”这两个词可以互换使用。

由于视图是应用程序的主要部分之一，并且您将拥有许多视图，Masonite 使它们非常易于使用。基本上，视图非常简单，但是它们可以根据您的需要而变得复杂。让我们开始吧，尽可能详细地解释许多事情。

# 返回视图

后端视图最常见的用例是返回它们并向它们传递数据。如果你来自任何一个框架，那么这对你应该很有意义。我们已经在之前的教程中解释了如何返回视图，但是快速复习一下应该是这样的:

```
def show(self):
    return view('template', {'key': 'value'}) 
```

Enter fullscreen mode Exit fullscreen mode

所以这一点非常重要。注意这里我们使用了一个内置函数。这种结构将与`WelcomeController`一起开箱，所以乍一看这似乎很奇怪。

这些被称为[内置助手函数](https://docs.masoniteproject.com/the-basics/helper-functions)(因为它们利用了 Python 内置函数)，旨在让你快速构建原型，之后你可以进去重构或离开它们。我个人在一切正常后进行重构。如果你不喜欢在你的应用程序中有内置，并且你认为它太“神奇”，那么你可以从你的`PROVIDERS`列表中删除`HelpersProvider`。所有内置的助手函数都通过该提供程序添加到应用程序中。

如果你不喜欢内置的助手功能，那么你也可以通过控制器参数来解决:

```
def show(self, View):
    return View('template', {'key': 'value'}) 
```

Enter fullscreen mode Exit fullscreen mode

如果这里有些混乱，那么请务必阅读关于[服务容器](https://docs.masoniteproject.com/architectural-concepts/service-container)以及 IOC 和依赖解析器如何工作的文档。

这与使用上面的 helper 函数完全相同，但这是通过使用[服务容器](https://docs.masoniteproject.com/architectural-concepts/service-container)来解决的。如果你不熟悉[服务容器](https://docs.masoniteproject.com/architectural-concepts/service-container)如何解决问题，那么请务必阅读这里的文档。

解析的对象实际上只是视图类上的 render 方法。如果你想成为一个超级牛逼的 Python 爱好者，坚持输入你需要的东西，那么你可以通过使用 Python 注释来解决这个问题:

```
from masonite.view import View

def show(self, view: View):
    return view.render('template', {'key': 'value'}) 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们在这里使用了`view.render`方法。如前所述，`View`键只是视图类上这个 render 方法的别名。

如何回复意见完全取决于你和你的团队。做你认为合适的决定。我个人使用最后一个选项并导入我所有的类，但这完全取决于你。

# 模板

返回视图时使用的第一个参数总是模板。所有模板都位于`resources/templates`目录中。所以如果我们返回这样一个视图:

```
from masonite.view import View

def show(self, view: View):
    return view.render('index', {'key': 'value'}) 
```

Enter fullscreen mode Exit fullscreen mode

它将寻找`resources/templates/index.html`文件并呈现出来。

大多数模板都可以毫无问题地位于`resources/templates`目录中，你显然可以根据需要深入到任意多个目录，比如:
这样的模板

```
view.render('dashboard/users/settings/edit', ...) 
```

Enter fullscreen mode Exit fullscreen mode

这就要看`resources/templates/dashboard/users/settings/edit.html`文件了。

# 全局模板

有些模板可以从第三方软件包或其他目录中呈现。例如，我们可以做类似于`pip install invoices`的事情，然后可能需要返回发票视图:

```
view.render('/invoices/templates/show', ...) 
```

Enter fullscreen mode Exit fullscreen mode

请注意前面的正斜杠。这向 Masonite 发出信号，您应该开始在那个模块中寻找那个模板。这将查看`invoices`模块内部，然后呈现一个`templates/show`文件。

这不仅适用于第三方软件包，也适用于应用程序中的任何模块。如果我们把所有的模板放在 Masonite 附带的`storage`目录中，那么我们可以从这个模块中指定我们的模板:

```
view.render('/storage/templates/index', ...) 
```

Enter fullscreen mode Exit fullscreen mode

## 传入数据

注意上面我们有一个带有键值对的字典。这是我们的模板中将提供的信息。如果你来自任何一个框架，那么这对你来说都是常识。

我们将把从控制器生成的数据传入视图，然后由视图显示这些数据。例如，我们可能有这样的东西:

```
from masonite.view import View
from app.User import User

def show(self, view: View):
      user = User.find(1)
    return view.render('index', {'user': user}) 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`resources/templates/index.html`文件中，我们可以像这样做:

```
{{ user.email }} 
```

Enter fullscreen mode Exit fullscreen mode

如果花括号没有多大意义，也不用担心。Masonite 使用 Jinja2，我们稍后将对此进行详细介绍。如果你熟悉 Jinja2，那么你应该不错。如果没有，那么这就是我们在浏览器中显示数据的方式。Jinja2 将接受这些语法语义，然后用我们传递给它的数据解析它们。

# Jinja2 语

如果你熟悉 Jinja2，那么你真的可以跳过这一节。这里没什么特别的。如果你不是，那么你可以继续阅读。

在这一节中，我们将真正地回顾你需要知道的一切，以及所有与 [Jinja2](http://jinja.pocoo.org/docs/2.10/) 相关的额外琐事，你可以前往他们的文档页面。

## 显示数据

如前所述，我们可以将数据传递到视图中，并用双花括号显示出来。上面的代码会输出如下内容:

```
your-email@gmail.com 
```

Enter fullscreen mode Exit fullscreen mode

除了过滤之外，显示数据真的没什么，我们稍后会谈到过滤。

下面是 [Jinja2](http://jinja.pocoo.org/docs/2.10/) 模板中你需要知道的部分。

## If 语句

我们可以通过使用 if 语句在模板中使用一些逻辑:

```
{% if user.email == 'joe@email.com' %}
    Hello Joe
{% elif user.email == 'bill@email.com' %}
    Hello Bill
{% else %}
    I don't know you
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

## 为循环

```
{% for key in keys %}
    {{ key }}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

## 扩展视图

我们可以“扩展”我们的视图，这样我们就可以有一个基础模板，我们所有的逻辑都继承自这个模板。如果你以前使用过任何模板语言，那么这对你来说很有意义:

```
{% extends 'nav/base.html' %}

{% for key in keys %}
    {{ key }}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

这将在顶部注入`nav/base.html`模板，并在它下面注入代码。

## 模板块

我们可以使用模板块作为稍后将使用的各种信息的占位符。我们的基本模板可能如下所示:

```
<!-- nav/base.html -->
<html>

<head> 
  {% block css %}{% endblock %}
</head>

<body>
<h1> Hey! </h1>
{% block content %}{% endblock %}
<h2> Hope to see you again soon! </h2>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

然后我们的子模板可以是这样的:

```
<!-- dashboard/user.html -->
{% extends 'nav/base.html' %}

{% block css %}
  <link href="/static/style.css">
{% endblock %}

{% block content %}
Some awesome content
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

然后将最终的模板呈现给浏览器，看起来像:

```
<!-- nav/base.html -->
<html>

<head> 
  <link href="/static/style.css">
</head>

<body>
<h1> Hey! </h1>
Some awesome content
<h2> Hope to see you again soon! </h2>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

## 包括文件

我们还可以包括文件，所以这可能是类似于侧边栏的东西，其中所有的逻辑都可以保存在该侧边栏的单个模板中。这可能类似于:

```
<!-- nav/base.html -->
<html>

<head> 
  {% block css %}{% endblock %}
</head>

<body>
<div class="col-xs-4">
  {% include 'snippets/sidebar.html' %}
</div>
<div class="col-xs-8">
  {% block content %}{% endblock %}
</div>

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

您可以在任何地方包含模板。所有包含的模板都继承当前模板的所有变量。

如果您包含的模板需要的变量不会出现在包含它的模板中，那么可以考虑使用视图共享功能。

## 使用滤镜

Jinja2 附带了他们称之为“过滤器”的东西，它实际上只是接收它所附加的变量然后返回该值的函数。

您可以将过滤器应用于带有管道字符的变量。例如:

```
{{ user.email|striptags|title }} 
```

Enter fullscreen mode Exit fullscreen mode

同样，这些都是内置的，可以立即使用。可以在 Jinja2 文档站点上找到您可以使用的过滤器列表。这里举不胜举。

## 构建过滤器

对于特定于应用程序的更高级的东西，我们可以构建自己的过滤器。如果你不熟悉[服务提供商](https://docs.masoniteproject.com/architectural-concepts/service-providers)，那么你应该先了解一下。

为了给我们所有的模板添加过滤器，我们可以使用 View 类。放置这段代码的最佳位置是在一个[服务提供者](https://docs.masoniteproject.com/architectural-concepts/service-providers)内部，其`wsgi`属性被设置为`False`。这将确保过滤器不会被一遍又一遍地添加到我们的代码库中，从而降低我们的代码速度。如果`wsgi`是`False`，那么它只会在服务器第一次启动时被加载到视图类中。

你应该专门为你的所有过滤器创建一个[服务提供者](https://docs.masoniteproject.com/architectural-concepts/service-providers)，但是现在我们将只使用`UserModelProvider`并把代码放在那里。为了做到这一点，我们只需要向视图类添加一个函数。我们可以通过使用`filter`方法:
将过滤器绑定到视图类

```
from masonite.view import View

def split_string(variable):
    return variable.split(',')

class UserModelProvider(ServiceProvider):

    wsgi = False

    ...

    def boot(self, Request, view: View):
        view.filter('split', split_string) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们只需要将一个函数绑定到视图类。现在我们可以在我们的模板中使用它:

```
{{ user.email|split }} 
```

Enter fullscreen mode Exit fullscreen mode

# 助手功能

就像我们在后端代码中有一些助手函数一样，我们也有一些助手函数已经注入到我们的模板中。

## 请求

我们可以很容易地得到当前的请求对象:

```
{{ request().path }} 
```

Enter fullscreen mode Exit fullscreen mode

这只是一个注入到视图中的请求类。

## 当前用户

我们可以得到当前用户:

```
{{ user().email }} 
```

Enter fullscreen mode Exit fullscreen mode

## 会话

这包含会话类:

```
{{ session().get('key') }} 
```

Enter fullscreen mode Exit fullscreen mode

## 获取路线

我们可以得到任何命名的路线:

```
{{ route('route.name') }} 
```

Enter fullscreen mode Exit fullscreen mode

这将返回路由名称的 URL。如果您的路由 URL 类似于`/route/name/@id`，那么您必须将路由参数指定为第二个参数:

```
{{ route('route.name', {'id': 1}) }} 
```

Enter fullscreen mode Exit fullscreen mode

## 请求方法

Masonite 支持所有形式的请求方法，包括`PUT`、`PATCH`、`DELETE`等。问题是 HTML 表单只接受`GET`和`POST`。我们可以通过使用一个助手方法来模拟请求方法:

```
<form action="{{ route('route.name') }}">
    {{ request_method('PUT') }}
</form> 
```

Enter fullscreen mode Exit fullscreen mode

提交后，该表单将作为`PUT`提交。

## 要回去了

提交表单后，我们可能想重定向回来。这可能是因为失败的表单或不正确的验证或其他原因。

```
<form action="{{ route('route.name') }}">
    {{ back(request().path) }}
</form> 
```

Enter fullscreen mode Exit fullscreen mode

这将重定向回当前路由，因为我们指定了要返回的当前路径。我们还可以使用之前的 route helper 来指定一条路线:

```
<form action="{{ route('route.name') }}">
    {{ back(route('form.errors')) }}
</form> 
```

Enter fullscreen mode Exit fullscreen mode

其使用方式在控制器内部:

```
def show(self):
    if some error:
        request().back()

    do some other logic here 
```

Enter fullscreen mode Exit fullscreen mode

Masonite 将知道我们想回到哪里，因为使用`back`助手提交了`__back`输入。

# 其他帮手

## CSRF 保护。

所有提交的不是`GET`请求的表格都受到 CSRF 攻击的保护。我们总是需要在这些类型的请求上指定 CSRF 令牌:

```
<form action="{{ route('route.name') }}" method="POST">
    {{ csrf_field|safe }}
</form> 
```

Enter fullscreen mode Exit fullscreen mode

如果您忘记了一个，那么您将会收到一个异常消息，大意是 CSRF 令牌无效或丢失。

在模板中。我想在本教程开始时讨论一下助手函数

# 查看类

视图类本身是处理所有视图的基础。因此，任何与视图相关的东西，比如添加辅助函数、渲染、添加过滤器、环境等等。是视图类的一部分。

这个类用别名`ViewClass`加载到容器中。大多数对象到视图类的绑定应该在[服务提供者](https://docs.masoniteproject.com/architectural-concepts/service-providers)内部完成，其中`wsgi`属性为`False`，以避免应用程序变慢。

## 获取视图类。

如前所述，视图类使用别名`ViewClass`绑定到容器，所以我们可以像这样解析类:

```
 def boot(self, Request, ViewClass):
        ViewClass.share(..) 
```

Enter fullscreen mode Exit fullscreen mode

或者通过注释解析:

```
from masonite.view import View
...
    def boot(self, Request, view: View):
        view.share(..) 
```

Enter fullscreen mode Exit fullscreen mode

## 共享

如果你想和你所有的模板共享一个变量或函数，那么我们使用`share()`方法。

这需要一本字典。关键是模板中使用的内容和返回的值。

```
from masonite.view import View
...
    def boot(self, Request, view: View):
        framework = 'Masonite'
        view.share({'framework': 'Masonite'}) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在所有模板中使用它:

```
{{ framework }} <!-- "Masonite" --> 
```

Enter fullscreen mode Exit fullscreen mode

## 作曲

与共享略有不同，我们可以使用视图合成。这本质上是共享，但只是针对特定的模板。假设我们有一个这样的模板结构:

```
resources/
  templates/
    dashboard/
      show.html
      user.html
      base.html
    settings/
      show.html
      index.html 
```

Enter fullscreen mode Exit fullscreen mode

如果您只希望某个特定变量仅在仪表板`user.html`模板中可用，那么我们可以编写:

```
from masonite.view import View
...
    def boot(self, Request, view: View):
        framework = 'Masonite'
        view.compose('dashboard/user': {'framework': 'Masonite'}) 
```

Enter fullscreen mode Exit fullscreen mode

第一个参数是模板，第二个参数是您希望可用的键值对。这与视图共享的工作方式相同，但只适用于特定的模板。

您也可以指定模板的通配符:

```
from masonite.view import View
...
    def boot(self, Request, view: View):
        framework = 'Masonite'
        view.compose('dashboard/*': {'framework': 'Masonite'}) 
```

Enter fullscreen mode Exit fullscreen mode

这将使键值对在所有仪表板模板中可用，以包括`dashboard/show.html`、`dashboard/user.html`、`dashboard/base.html`。

## 环境

环境可以简单地认为是模板的位置。开箱即用，Masonite 只附带了一个模板环境(`resources/templates`)，它只需要基础目录就可以工作，但是您可以轻松地在:
中加载其他环境

```
from masonite.view import View
...
    def boot(self, Request, view: View):
        view.add_environment('invoices/templates') 
```

Enter fullscreen mode Exit fullscreen mode

这是如何工作的，它在`invoices`模块中寻找模板目录。这对于使用第三方包也很棒，因为模块不需要在你的文件结构中，但是可以在你的 Python 环境中。

以这个场景为例，您安装了一个名为 invoices 的包，并且需要向应用程序添加新的模板环境。

你可以这样做:

```
$ pip install masonite-invoices 
```

Enter fullscreen mode Exit fullscreen mode

按照说明将包添加到您的`PROVIDERS`列表，然后我们就可以访问所有的包视图:

```
{% extends 'invoices/base.html' %}

{% include 'my/application/snippet.html' %} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们现在正在组合多个模板环境，其中第一行来自`masonite-invoices`包，最后一行来自我们的应用程序。

* * *

感谢阅读！如果您有任何问题，请务必在 [Masonite GitHub Repo](http://github.com/masoniteframework/masonite) 页面上给一颗星，或者加入[mason ite 官方 Slack 频道](http://slack.masoniteproject.com)！