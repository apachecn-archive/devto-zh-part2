# Masonite 框架教程系列第 3 部分-控制器

> 原文：<https://dev.to/masonite/masonite-framework-tutorial-series-part-3---controllers-14pn>

[![](img/32a756bb35faaeb4c1fa1c9d7d480cd0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uhbGxGCp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wxl1ooxdmuik6ocof1ot.png)

# 简介

这是 Masonite 教程系列的第 3 部分。在本系列中，我们将在更个人化的层面上进行比文档更深入的探讨。我会尽可能详细地解释每一部分的每一部分，同时尽量不离题。

Masonite 的业务逻辑与 Flask 或 Django 有所不同。Masonite 坚持一个真正的 MVC 框架的惯例，而 Flask 或 Django 稍微曲解了一下。

Masonite 中的控制器是简单的类。它们实际上非常简单，不从任何类继承(这在 Masonite 2.1 中可能会改变)。控制器只是类和方法。该类通常可以看作是“组”，控制器方法包含特定视图的所有业务逻辑。我们稍后会详细解释这种关系。

现在让我们从初级概念开始，转到更高级的主题

# 创建控制器

控制器可以通过运行 craft 命令来创建:

```
$ craft controller Name 
```

Enter fullscreen mode Exit fullscreen mode

## 控制器名称

您可以随意命名您的控制器，但是按照惯例，这些控制器会附加有`Controller`。例如，上面的命令将创建一个名为`NameController`而不是`Name`的控制器。

这也将把类放在它自己的文件中:`app/http/controllers/NameController.py`。按照惯例，也许是固执己见，Masonite 中的每个类都在它自己的文件中。随着越来越多的文本编辑器和 ide 允许在文件和热键之间快速切换，在整个类之间切换变得非常快(因为它们是它们的文件名)，我们已经看到了这一点。

## 不追加“控制器”

需要注意的重要一点是，并不是所有的控制器都需要附加`Controller`，所以我们可以“精确地”创建一个我们在终端中指定的控制器。

如果你像我一样，那么你不喜欢那些“含糊其辞”的东西。换句话说，像`app/http/controllers/NameController.py`这样的结构对你来说可能很奇怪，因为对你来说很明显它是一个控制器，因为它在控制器目录中。

所以我们也可以像这样创建控制器:

```
$ craft controller Name -e 
```

Enter fullscreen mode Exit fullscreen mode

这个简单的`-e`标志将创建没有附加的`Controller`部分的控制器。

# 结构

让我们浏览一下控制器结构，以便熟悉它。写完命令后的控制器看起来像:

```
''' A Module Description '''

class NameController:
    ''' Class Docstring Description '''

    def show(self):
        pass 
```

Enter fullscreen mode Exit fullscreen mode

您将会看到我们有一个简单的类，它可以被认为是“一组业务逻辑”和一个控制器方法，它是每个视图的单独业务逻辑。

在 Django 应用程序中，控制器方法可以被视为“基于功能的视图”,并且包含类似的逻辑。唯一真正的区别是，您可以使用其他方法来完成和分解一些逻辑。例如，对于这个类，我们现在可以做这样的事情:

```
''' A Module Description '''

class NameController:
    ''' Class Docstring Description '''

    def show(self):
        ...
        self.get_names()

    def another(self):
        self.get_names()
        ...

    def get_names(self):
        return ['Mike', 'John', 'Bob'] 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在 Django 中通过创建函数来做类似的事情，但是它并没有你需要的所有逻辑。这是一个简单的例子，但是你可以想象你的业务逻辑会变得多么复杂。

# 容器解析

如果你还不知道 Masonite 的服务容器是如何解析对象的，那么我建议你在阅读本节之前先阅读它。如果您理解容器和自动解析依赖注入是如何工作的，那么请继续:)

## 控制器方法

如果一个类中的所有方法是由 route 指定的，那么它们都由容器解析。自动解析部分是路由逻辑的一部分。例如，我们可能有这样一条路线:

```
ROUTES = [
    get('/hello/@name', 'NameController@show')
] 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，当我们渲染这条路线时，`show`方法将被解析:

```
from masonite.request import Request

class NameController:

    def show(self, request: Request):
        request # <class masonite.request.Request> 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到我们使用 Python 注释来访问这个类。这对于包含可测试性有几个积极的影响，我们将在后面讨论。

## 控制器构造器

您可以想象，当几个方法开始需要相同的类时，我们会有一点多余的代码。例如，我们可能有两个方法需要请求类:

```
from masonite.request import Request

class NameController:

    def show(self, request: Request):
        request # <class masonite.request.Request> 
    def store(self, request: Request):
        request # <class masonite.request.Request> 
```

Enter fullscreen mode Exit fullscreen mode

这是重复的，所以我们可以在类构造函数中抛出这些类:

```
from masonite.request import Request

class NameController:

    def __init__(self, request: Request):
        self.request = request

    def show(self):
        self.request # <class masonite.request.Request> 
    def store(self):
        self.request # <class masonite.request.Request> 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到，这里我们清理了请求类，以便只解析一次。这使得我们的代码更加枯燥。

# 归还东西

我们可以在控制器方法中返回一些东西。最明显要返回的是一个视图。

在 Django 中，视图是一个函数，更类似于上面的控制器方法。在 Masonite 中，视图是实际的 HTML 模板。我们将在以后的系列教程中讨论视图。

由于我们会经常在 Masonite 中返回视图，所以我们添加了一个“内置”帮助函数。该功能是通过名为`HelpersProvider`的服务提供商添加的，并且已经添加到了`PROVIDERS`列表中。如果您不喜欢这些“神奇”的功能，那么您可以简单地删除该服务提供者，它将从框架中删除所有的助手功能。

## 返回一个视图

所有视图都在`resources/templates`目录中。我们可以用两种方式渲染视图:

我们可以使用内置的助手函数:

```
 class NameController:

    def show(self):
        return view('index') 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们在这里没有导入任何内容。这些被称为内置助手函数(因为它们利用了 Python 3 的内置功能)。

有些人不喜欢这样，所以我们可以更明确地定义我们的视图类:

```
from masonite.view import View

class NameController:

    def show(self, view: View):
        return view.render('index') 
```

Enter fullscreen mode Exit fullscreen mode

helper 函数只是指向视图类上的这个 render 方法，因此它们的执行完全相同。

大多数人喜欢助手功能，因为它允许他们快速模拟功能。一旦一切正常，您就可以返回并重构更显式的导入。

## 返回字典

控制器方法的另一个方便之处是你可以返回一个字典，它会自动返回一个 JSON 响应:

```
from masonite.view import View

class NameController:

    def show(self):
        return {'name': 'Joe'} 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一个 JSON 响应:

```
{  "Name":  "Joe"  } 
```

Enter fullscreen mode Exit fullscreen mode

这便于构建快速 API 端点。

# 获取路线参数

在之前的教程中，我们解释了我们可以拥有像`id`或`name`这样的动态属性的路线，它们看起来像这样:

```
ROUTES = [
    get('/dashboard/user/@id', 'NameController@dashboard'),
    get('/hello/@name', 'NameController@hello')
] 
```

Enter fullscreen mode Exit fullscreen mode

我们这里有这些`@id`和`@name`字段。我们可以用请求类:
在控制器中获取这些

```
from masonite.request import Request

class NameController:

    def dashboard(self, request: Request):
        request.param('id')

    def hello(self, request: Request):
        request.param('name') 
```

Enter fullscreen mode Exit fullscreen mode

# 输入数据

我们还可以获得输入参数，比如一个`POST`请求上的表单数据或者一个`GET`请求上的部分查询字符串。梅森奈特很聪明，知道是哪一个。我从来不明白为什么我应该明确地指定我在哪个请求中，以便得到特定的输入数据，这样我们就可以用`.input()`方法:
得到任何一个

```
from masonite.request import Request

class NameController:

    # GET /dashboard/route?firstname=Joe
    def show(self, request: Request):
        request.input('firstname') # returns Joe 
```

Enter fullscreen mode Exit fullscreen mode

# 测试

如果我没有向你推销 Masonite 控制器的有用性，那么也许解释这些控制器的可测试性是值得的。我在这里只讨论一点点细节，因为这一部分可以是一整篇文章，所以让我们稍微梳理一下。

请注意，我们只是将请求和视图作为参数传入。所以我们现在可以嘲笑他们。

假设我们有这样一个类:

```
from masonite.request import Request

class NameController:

    def show(self, request: Request):
        return request.input('name') # returns Mike 
```

Enter fullscreen mode Exit fullscreen mode

我们如何测试这个？我将在另一个系列中更详细地介绍测试，但是我们的测试可能看起来类似于:

```
from app.http.controllers.NameController import NameController

class MockRequest:
    def input(self, name):
        return 'Mike'

class TestNameController:

    def test_name_controller(self):
        assert NameController().show(MockRequest()) == 'Mike' 
```

Enter fullscreen mode Exit fullscreen mode

有很多很棒的测试技巧，我会在这里详细介绍。让我们继续关注控制器的基础知识。

* * *

这就是本教程中控制器的基础。如果你喜欢你所看到的，一定要关注或在下面留下评论。您也可以在这里找到[mason ite](http://github.com/masoniteframework/masonite)