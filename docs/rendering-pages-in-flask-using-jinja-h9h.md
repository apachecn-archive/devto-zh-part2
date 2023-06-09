# 使用 Jinja 在 Flask 中呈现页面

> 原文：<https://dev.to/hackersandslackers/rendering-pages-in-flask-using-jinja-h9h>

[![Rendering Pages in Flask Using Jinja](img/e031109d00b3490259d2f2b6d28a5cda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P0jAYF3c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/hackersandslackers-cdn/2019/02/jinja.jpg)

如果 Dreamweaver 这个名字对你来说意味着什么，你可能还记得 90 年代的网络开发有多糟糕。CSS 不是“东西”，我们滥用 HTML 表格，像`<marquee>`这样的标签被认为是合法的。比这一切更糟糕的是*手动*T5】的重复；HTML 从来没有打算识别基本的编程概念，比如继承、导入。即使站点*看起来*在页面之间有共同的元素(比如导航)，现实却是在站点的每个页面上手工硬编码这些元素的噩梦。更改导航链接意味着在数百个页面上手动进行相同的更改。太棒了。

我们设法从石器时代爬了出来，进入了一个可重用“模板”、“部分”、“组件”的时代，以及各种各样的方法来确保我们尽可能少地编码，如果有的话(看看你，NPM)。Flask 的方法是通过一个叫做 **Jinja2** 的模板系统:和让我们写 HTML 的 HTML 预处理器，它基于*逻辑*和*上下文*而改变。与静态 HTML 不同，像 Jinja 这样的模板系统使我们能够在页面之间共享代码片段，或者根据上下文有条件地呈现页面。

如果你熟悉车把或者 Django 的模板系统，Jinja 会感觉很熟悉。任何模板引擎的相同概念仍然适用于 Jinja，但是 Jinja 恰好更好。不要@我。

## 布局、页面和局部

如果我们要剖析组成一个站点的 HTML 元素的“类型”,很容易将 HTML 块归类为属于 3 种类型之一:

*   布局:大多数网络应用都有一些页面间共享的“框架”结构。这可以简单到在多个页面上重复相同的内容，或者实施一个通用的物理结构，比如标准的*内容+侧边栏*外观。一个连贯的 web 应用程序很可能只有很少的独特布局(我不认为我每个项目需要超过 1 个)。
*   页面模板:页面模板是模板化的重要组成部分。虽然这个博客有数百个帖子，但每个“帖子”只是单个**帖子模板**的一个实例:我们网站的一个独特组装的部分，可以用不同的数据复制。
*   **Partial**:Partial 是独立的片段，可以在需要的地方被页面共享。想想导航元素、窗口小部件或任何其他设计为在站点的不同部分之间共享的东西

### **为动作设置**

我们将创建一个微型 Flask 应用程序来演示如何使用 Jinja 利用我们刚刚学到的三个概念来构建网站。我们将首先创建一个应用程序，它使用三种模板类型中的一种:**layout.html、**和**nav.html**。这些模板将保存在一个*/模板*文件夹中，因此留给我们一个这样的应用:

```
/flask-jinja-tutorial
├── /flask_jinja_tutorial
│   ├── __init__.py
│   ├── routes.py
│   ├── /static
│   └── /templates
│       ├── home.html
│       ├── layout.html
│       └── navigation.html
└── wsgi.py 
```

如果没有应用程序为模板服务，模板对我们没有任何好处。在 ***init* 中创建一个 Flask app 对象后。py** ，让我们在 **routes.py** 中创建一条路线，设置一个基本主页:

<figure>

```
"""Route declaration."""
from flask import current_app as app
from flask import render_template

@app.route('/')
def home():
    """Landing page."""
    return render_template('home.html',
                           title="Jinja Demo Site",
                           description="Smarter page templates \
 with Flask & Jinja.") 
```

<figcaption>app.py</figcaption>

</figure>

我们在这里所做的就是设置一个名为`home()`的路径，每当用户访问我们的 Flask 应用程序时，它都会为**home.html**模板提供服务。除了指定提供哪个页面模板，我们还向`render_template()`传递两个关键字参数:**标题**和**描述**。一会儿我们将看到这些技术的实际应用。

## 渲染一个模板

让我们看看当我们完成提供页面模板这个看似简单的任务时发生了什么。这是我为**home.html**:
准备的

<figure>

```
{% extends 'layout.html' %}

{% block content %}
    <div class="container">
        <h1>{{title}}</h1>
        <p>{{description}}</p>
    </div>
{% endblock %} 
```

<figcaption>home.html</figcaption>

</figure>

哇，这是什么`{% extends 'layout.html' %}`废话？还有`{% block content %}`是什么意思？

你可能还记得之前我们在*/模板*文件夹中创建了一个名为**layout.html**的文件。我们已经介绍过“布局”包含页面间共享的重复结构(或样板文件)。为了更好地理解 home.html 的**和**，让我们来看看 layout.html 的**:** 

 **<figure>

```
<!doctype html>
<html>

  <head>
    {{title}}
    <meta charset="utf-8">
    <meta name="description" content={{description}}>
    <link rel="shortcut icon" href="/favicon.ico">
  </head>

  <body>
    {% include 'navigation.html' %}
    {% block content %}{% endblock %}
  </body>

</html> 
```

<figcaption>layout.html</figcaption>

</figure>

当一个被请求的页面模板“扩展”另一个时，被请求的模板将包含被扩展的父模板的所有 HTML。当**home.html**扩展**layout.html**时，我们需要计算出我们的页面将被安装在**layout.html**的什么地方。Jinja 通过在名为“blocks”的模板中声明和匹配保留空间来处理这个问题。

**layout.html**有一个名为“content”的块，它同时出现在我们的页面模板和它扩展的布局中——这让 Jinja 知道 home.html**的内容应该加载到这个块中，这实际上在运行时产生了下面的模板:** 

<figure>

```
<!doctype html>
<html>

  <head>
    {{title}}
    <meta charset="utf-8">
    <meta name="description" content={{description}}>
    <link rel="shortcut icon" href="/favicon.ico">
  </head>

  <body>
    {% include 'navigation.html' %}
    <div class="container">
        <h1>{{title}}</h1>
        <p>{{description}}</p>
    </div>
  </body>

</html> 
```

<figcaption>The result of **home.html** extending **layout.html**.</figcaption>

</figure>

上面演示了一个**home.html**页面被加载到**layout.html**后呈现的内容。我们的页面已经看起来更像 HTML 了。

既然你已经理解了块的概念，这里有一个让你保持警惕的难题:布局和页面可以包含多个块，这让我们可以做一些令人兴奋的事情。例如，假设我们希望能够将样式注入到我们的布局中，这取决于哪个页面扩展了它。这里的 home.html 有两个块:一个用于插入页面的 CSS 样式`<head>`，另一个用于加载页面内容:

<figure>

```
{% extends 'layout.html' %}

{% block css %}
  <link href="{{ url_for('static', filename='css/home.css') }}" rel="stylesheet">
{% endblock %}

{% block content %}
    <div class="container">
        <h1>{{title}}</h1>
        <p>{{description}}</p>
    </div>
{% endblock %} 
```

<figcaption>home.html</figcaption>

</figure>

我打赌你能猜到当我们把**home.html**放入这个新布局
时会发生什么

<figure>

```
<!doctype html>
<html>

  <head>
    {{title}}
    <meta charset="utf-8">
    <meta name="description" content={{description}}>
    <link rel="shortcut icon" href="/favicon.ico">
    {% block css %}{% endblock %}
  </head>

  <body>
    {% include 'nav.html' %}
    {% block content %}{% endblock %}
  </body>

</html> 
```

<figcaption>layout.html</figcaption>

</figure>

来自 home.html**的*【CSS】*和*【内容】*块能够被匹配并加载到 layout.html**中它们各自的块中**这使得我们能够改变布局模板而不受 layout.html**的 DOM 结构的约束。****

让我们来解决房间中的`{{title}}`和`{{description}}`模块。这些双括号项表示保留的变量名。回到 **routes.py** ，当我们使用`render_template()`时，我们传递了几个匹配这些名称的关键字参数。我们的模板将在运行时呈现这些值:

<figure>

```
<!doctype html>
<html>

  <head>
    Jinja Demo Site
    <meta charset="utf-8">
    <meta name="description" content="Smarter page templates with Flask & Jinja.">
    <link rel="shortcut icon" href="/favicon.ico">
    <link href="/static/css/home.css') }}" rel="stylesheet">
  </head>

  <body>
    {% include 'navigation.html' %}
    <div class="container">
        <h1>Jinja Demo Site</h1>
        <p>Smarter page templates with Flask & Jinja.</p>
    </div>
  </body>

</html> 
```

<figcaption>Our page with rendered kwargs.</figcaption>

</figure>

这留给我们最后一个金贾声明:`{% include 'navigation.html' %}`。`include`告诉 Jinja“在这里加载一个名为【nav.html】T2 的单独模板。”这些被放入更大模板中的独立模板被称为*分模板*。我的**navigation.html**是一个简单的标题导航，看起来像这样:

<figure>

```
<header>
   <nav>
    <a href="https://example.com">Link 1</a>
    <a href="https://example.com">Link 2</a>
    <a href="https://example.com">Link 3</a>
  </nav>
</header> 
```

<figcaption>navigation.html</figcaption>

</figure>

现在我们可以看到我们渲染的主页的整体外观:

<figure>

```
<!doctype html>
<html>

  <head>
    Jinja Demo Site
    <meta charset="utf-8">
    <meta name="description" content="Smarter page templates with Flask & Jinja.">
    <link rel="shortcut icon" href="/favicon.ico">
    <link href="/static/css/home.css') }}" rel="stylesheet">
  </head>

  <body>
    <header>
       <nav>
        <a href="https://example.com">Link 1</a>
        <a href="https://example.com">Link 2</a>
        <a href="https://example.com">Link 3</a>
      </nav>
    </header>
    <div class="container">
        <h1>Jinja Demo Site</h1>
        <p>Smarter page templates with Flask & Jinja.</p>
    </div>
  </body>

</html> 
```

<figcaption>Our rendered homepage.</figcaption>

</figure>

我们做到了！我们已经剖析了 Jinja 的三种模板“类型”，并使用每种类型构建了一个动态 HTML 页面。这种水平的工作知识已经相当不错了——完全有可能只用我们到目前为止介绍的内容来构建 web 应用程序。也就是说，金贾可以走得更远、更深。

## 晋嘉的逻辑

Jinja 不仅仅是在页面之间共享 HTML。Jinja 可以执行一些重要的业务逻辑，有条件地呈现部分模板。最明显的例子是一个简单的`if`语句:

<figure>

```
<div class="status">
  {% if status.active %}
    Activated.
  {% elif status.disabled %}
    Disabled.
  {% else %}
    OH GOD WHAT IS THE STATUS?!
  {% endif %}
</div> 
```

<figcaption>Standard Jinja `if` statement.</figcaption>

</figure>

Jinja `if`语句可以包含你所期望的任何条件操作符。`{% if status == 1 %}`和`{% if "active" in status %}`分别是评估数值和字符串值的有效条件。

### 循环&数据类型

我们之前传递了几个字符串给**home.html**，但是实际上我们也可以传递几乎任何可以想到的数据类型。让我们把 navigation.html 的**重新想象成一个动态模板。与其将链接硬编码成 HTML，不如让我们基于从 **routes.py** 传递给【navigation.html】T4**的数据来构建这些导航链接。

表示这些数据的一个很好的方式是*字典的*列表*:*

<figure>

```
...

@app.route('/')
def home():
    """Landing page."""
    nav = [{'name': 'Home', 'url': 'https://example.com/1'},
           {'name': 'About', 'url': 'https://example.com/2'},
           {'name': 'Pics', 'url': 'https://example.com/3'}]
    return render_template('home.html',
                           nav=nav,
                           title="Jinja Demo Site",
                           description="Smarter page templates \
 with Flask & Jinja.") 
```

<figcaption>app.py</figcaption>

</figure>

我们可以像之前传递`title`和`description`一样，将类似`nav`的 Python 列表传递给 Jinja 模板。现在我们可以调整**navigation.html**根据这个列表的内容动态建立链接:

<figure>

```
<header>
   <nav>
    {% for link in nav %}
      <a href="{{ link.url }}">{{ link.name }}</a>
    {% endfor %}
  </nav>
</header> 
```

<figcaption>navigation.html</figcaption>

</figure>

Jinja 可以使用与 Python 几乎相同的语法遍历可迭代的数据类型(比如列表)。上面的例子循环遍历我们的列表`nav`。`nav`的每个成员都是一个字典，因此我们可以通过`{{ link.url }}`和`{{ link.name }}`用点符号得到每个字典的值。

## 一个金甲忍者的道路

正如你可以从[官方 Jinja 文档](https://jinja.palletsprojects.com/en/2.11.x/)中收集到的，我们仅仅触及了 Jinja 所能处理的表面。请自便，但是要注意:掌握模板的每一个细节就像是成为正则表达式大师的一个更没有价值的版本。拥有强大的工具作为临时知识是很酷的，但是掌握普遍适用的模式和“有点混蛋”之间有一条很难的界限。

如果您感兴趣，可以在 GitHub 上找到本教程的源代码:

[https://github.com/hackersandslackers/flask-jinja-tutorial](https://github.com/hackersandslackers/flask-jinja-tutorial)**