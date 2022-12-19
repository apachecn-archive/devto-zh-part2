# 使用 Masonite 框架说“你好，世界”

> 原文：<https://dev.to/nioperas06/saying-hello-world-using-masonite-framework-261l>

在本帖中，我们将使用 Masonite 框架构建一个简单的 Hello World 应用程序。

# 什么是 [Masonite 框架](https://github.com/MasoniteFramework/masonite)？

根据官方[文档](https://docs.masoniteproject.com/)，Masonite 被描述为一个 Python web 框架，它致力于成为一个真正的包含电池的开发者工具，具有许多开箱即用的功能和极其可扩展的架构。Masonite 非常适合初学 web 应用的开发人员以及有经验的开发人员。

Masonite 利用 Python 的强大功能为开发人员提供了一系列强大的功能，例如简单而富于表现力的路由引擎、强大的命令行助手、简单的迁移系统、出色的活动记录式 ORM、用于导航和扩展项目的出色的文件系统架构等等。

# 我们开始吧

为了使用 [Masonite 框架](https://github.com/MasoniteFramework/masonite)，您需要:

*   Python 3.4+版本
*   Pip3 或 Pipenv

您现在可以使用 pip:
安装 Masonite

```
$ pip install masonite-cli 
```

Enter fullscreen mode Exit fullscreen mode

下一步是使用`craft`命令行工具:
创建 Masonite 项目

```
$ craft new hello_world 
```

Enter fullscreen mode Exit fullscreen mode

这将在名为 hello_world 的文件夹中获得最新的 Masonite 项目模板。现在运行以下命令:

```
$ cd hello_world
$ craft install
$ craft serve 
```

Enter fullscreen mode Exit fullscreen mode

打开浏览器，访问以下地址:

```
http://localhost:8000/ 
```

Enter fullscreen mode Exit fullscreen mode

给你！
[![Masonite Framework](img/61baf6370e9c785325a571a2fab1744b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NEZ6M1cq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/905962zllph6suneyehr.png)

Masonite 是一个真正的 MVC 框架。定义哪个控制器的哪个动作的所有路线都位于`routes/web.py`中。

现在，在`ROUTES`列表中添加下面一行:

```
Get().route('/home', 'HomeController@home') 
```

Enter fullscreen mode Exit fullscreen mode

在 Masonite 中，您可以为路线定义控制器方法。让我们来创造`HomeController`。运行此命令:

```
$ craft controller Home 
```

Enter fullscreen mode Exit fullscreen mode

所有控制器都位于`app/http/controllers`目录中。生成的`HomeController`包含:

```
''' A Module Description '''

class HomeController:
    ''' Class Docstring Description '''

    def show(self):
        pass 
```

Enter fullscreen mode Exit fullscreen mode

只需重命名 show 方法并返回一个视图:

```
class HomeController:

    def home(self):
        return view('home') 
```

Enter fullscreen mode Exit fullscreen mode

被称为助手函数，不需要任何输入，只是简单可用🔥。让我们用`craft`创建我们的主页模板。

```
$ craft view home 
```

Enter fullscreen mode Exit fullscreen mode

这将创建`resources/templates/home.html`。改变其内容:

```
<h1>Masonite is awesome!</h1> 
```

Enter fullscreen mode Exit fullscreen mode

在`http://localhost:8000/home`点打开浏览器，就看到:
[![Hello world](img/c8b67efb08a6e212e150a62673ee4e45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JTfYW8lB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4lrpc8wzrop48nsuyw5w.png)