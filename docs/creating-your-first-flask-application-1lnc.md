# 创建您的第一个 Flask 应用程序

> 原文：<https://dev.to/hackersandslackers/creating-your-first-flask-application-1lnc>

[![Creating Your First Flask Application](../Images/533f50ba5d8033a0ab7d585876b2260d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VFkH6-j0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackersandslackers-cdn.storage.googleapis.com/2020/02/flask-intro.jpg)

几年来，弗拉斯克掌权的证据一直在我们身边。任何密切关注初创公司选择的技术堆栈的人无疑都注意到了一个转变:在某个时候，行业标准完全背离了 Django。

整个行业都在烧瓶上下了巨大的赌注。Plotly 的著名的 **Dash** 产品是已经取得巨大成功的 Flask 的延伸。甚至主要的云提供商，即**谷歌云**，都选择默认使用 Flask 来实现无服务器应用的 Python 实现。谷歌云功能和谷歌应用引擎都在其核心运行 Flask。JetBrains 终于用他们的 [2018 Python 调查](https://www.jetbrains.com/research/python-developers-survey-2018/):47%**的 devs 使用 Flask 的报告给这一趋势加上了官方数字，Django 的 **45%** 。游戏:女式衬衫。**

<figure>[![Creating Your First Flask Application](../Images/c93be71a337db7a29307ae2187057081.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8wCE4d0J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/hackersandslackers-cdn/2019/09/0_t_K8nhHmTTmYpZ-b.png) 

<figcaption>烧瓶赢得 2018 年市场主导权。</figcaption>

</figure>

放下干草叉:这不是 Flask vs. Django 的帖子，也不是说一个框架优于另一个框架。这两种框架都有自己的位置，这个“位置”属于偏好领域。

## 为什么是烧瓶？

与在更传统的 MVC 框架中开发相比，在 Flask 中开发应用有着非常不同的叙述。在过去，建立一个框架很容易就要花上几个小时:假设我们的应用程序需要所有的附加功能，那就不可能得到一个“Hello world！”在没有完全理解数据库配置、静态资产、模板和其他我们的应用程序可能不需要的东西的情况下离开地面。这尤其是 Python 生态系统的一个问题。很少有人仅仅为了构建 web 应用程序而求助于 Python:绝大多数 Python 开发者都是数据分析领域的，没有应用程序开发的传统背景。要求数据分析师(他们大多已经习惯了 Jupyter 笔记本)在开始之前就掌握所有的 web 开发基础知识是不现实的。

Flask 的设置仅仅是以下五行的复制+粘贴:

<figure>

```
from flask import Flask

app = Flask( __name__ )

@app.route("/")
def hello():
    return "Hello World!" 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>app.py</figcaption>

</figure>

这五行代码创建了一个 live Flask 应用程序。没有任何关于框架的其他知识，我们可以立即插入任何我们已经拥有的 Python 逻辑来更改“Hello world！”以匹配任何可以想象的输出。虽然可以创建一个完整的 Flask 应用程序作为一个小文件，但是 Flask 可以像它的前辈一样强大和复杂。当 Flask 应用程序的作者认为有必要时，可以引入各种 Flask 插件来为我们提供健壮的应用程序逻辑。例子包括:

*   **Flask-SQLAlchemy** 用于数据库交互。
*   **Flask-Sessions** 用于用户会话管理。
*   **Flask-Login** 管理用户登录。
*   实际上有数百个其他图书馆。

这种即插即用的结构使 Flask 项目更具表现力，同时为从 0 开始的开发人员提供了简单性。不仅如此，看人家源码突然变得简单了:我知道这个 app 一定要做 XYZ，因为这个人导入了 XYZ。

## 解剖烧瓶的“Hello World！”

让我们回到我们的 5 行应用程序来挑选细节:

<figure>

```
from flask import Flask

app = Flask( __name__ ) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>app.py</figcaption>

</figure>

Flask Python 库最重要的部分是带有大写“F”的 **Flask** (如:`from flask import Flask`)。这个五个字母的单词创建了一个对象，它指的是整个应用程序本身:当我们声明`app = Flask( __name__ )`时，我们正在创建代表我们的应用程序的变量 **app** 。因此，当我们配置变量 **app 时，**我们正在配置我们整个应用程序的工作方式。比如设置`app = Flask()`可以接受几个属性:

<figure>

```
from flask import Flask

app = Flask(
    __name__ ,
    instance_relative_config=False,
    template_folder="templates",
    static_folder="static"
) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>app.py</figcaption>

</figure>

这是一个创建 Flask 应用程序的例子，有一些细节:我们的配置文件的位置，我们将存储 pages 模板的文件夹，以及我们将存储前端资产(JS、CSS、图像等)的文件夹。).

### 基本烧瓶路线

我们的应用程序的主要功能叫做`hello()`，它被 Flask 最重要的装饰器`.route()`包装起来。如果你不熟悉 Python 中的**装饰器**，装饰器是我们用来包装其他函数的函数。了解所有的细节并不重要，除了 Flask 附带了一个 route decorator，它允许我们根据用户正在加载的应用程序页面来提供功能。通过设置`@app.route("/")`，我们指定函数`hello()`应该在有人使用我们的应用时触发。

当然，我们可以返回除“Hello world！”之外的任何值如果我们愿意。假设你已经有了一个返回一个数的平方加上 9 的脚本。我们可以将该逻辑保存在一个名为`squareOfNumberPlusNine()`的函数中，一个名为`logic.py`的文件中。现在，我们的脚本可以看起来像这样:

<figure>

```
from flask import Flask
from logic import squareOfNumberPlusNine

app = Flask( __name__ )

@app.route("/")
def hello():
    return squareOfNumberPlusNine(5) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>app.py</figcaption>

</figure>

这将返回 **34** 而不是“**你好，世界！”**。在没有任何 Python web 开发知识的情况下，我们已经可以使用 Flask 插入我们编写的逻辑并提供结果。

## 烧瓶核心库的其他部分

除了`Flask`我们还可以导入其他东西`from flask`。以下是一些例子:

### 提供原始 HTML

`Markup`允许我们通过将字符串呈现为 HTML:
来返回 HTML 页面

<figure>

```
from flask import Flask, Markup

app = Flask( __name__ )

@app.route("/")
def hello():
    return Markup("<h1>Hello World!</h1>") 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>app.py</figcaption>

</figure>

### 提供 HTML 页面模板

`return_template`将通过在我们的`/templates`文件夹:
中找到页面来返回一个 HTML 页面

<figure>

```
from flask import Flask, render_template

app = Flask( __name__ , template_folder="templates")

@app.route("/")
def hello():
    return render_template("index.html") 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>app.py</figcaption>

</figure>

### 上菜回应

有许多用途，其中最显著的是以 JSON 对象的形式提供响应。如果我们的应用程序是一个 API，我们将返回一个响应对象而不是页面:

<figure>

```
from flask import Flask, make_response

app = Flask( __name__ )

@app.route("/")
def hello():
    headers = {"Content-Type": "application/json"}
    return make_response(
        'it worked!',
        200,
        headers
    ) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>app.py</figcaption>

</figure>

关于使用 Flask 创建 API 的主题，我们还可以指定手边的路由是 POST、GET 还是其他方法。这在路由装饰器中很容易处理:

<figure>

```
from flask import Flask, make_response, request

app = Flask( __name__ )

@app.route("/", methods=['GET'])
def hello():
    if request.method != 'GET':
        return make_response('Malformed request', 400)
    headers = {"Content-Type": "application/json"}
    return make_response(
        'it worked!',
        200,
        headers
    ) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>app.py</figcaption>

</figure>

上面的函数首先检查以确保用户使用正确的方法访问端点。如果他们使用了不正确的方法，我们返回一个错误。

要创建 JSON 响应，请查看 Flask 内置的`jsonify()`函数。`jsonify()`输出一个 Python 字典作为 JSON 内联:

<figure>

```
from flask import Flask, make_response, request, jsonify

app = Flask( __name__ )

@app.route("/", methods=['GET'])
def hello():
    if request.method != 'GET':
        return make_response('Malformed request', 400)
    my_dict = {'key': 'dictionary value'}
    headers = {"Content-Type": "application/json"}
    return make_response(
        jsonify(my_dict),
        200,
        headers
    ) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>app.py</figcaption>

</figure>

如果你正在寻找 Flask 中服务路线的深入指南，请查看我们的帖子[Flask 中的路线艺术](https://hackersandslackers.app/the-art-of-building-flask-routes/)。*/自我推销*

## 屈服于烧瓶

即使您选择坚持使用您的大型框架，也很容易理解为什么 Flask 对于许多场景来说是有用的嵌入式解决方案。毫无疑问，有大量有用的 Python 脚本被浪费了，因为使它们易于被其他人使用的最后一步从未完成。Flask 是实现这最后一步的绝佳方式，最棒的是:你已经知道如何使用它了。