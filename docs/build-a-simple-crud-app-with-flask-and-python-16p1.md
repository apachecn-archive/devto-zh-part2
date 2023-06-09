# 用 Flask 和 Python 构建一个简单的 CRUD 应用程序

> 原文：<https://dev.to/oktadev/build-a-simple-crud-app-with-flask-and-python-16p1>

今天我将带你构建一个简单的 Flask web 应用程序(博客),包括用户管理(登录、注册等)。)、数据库模型，以及随之而来的一切。

在这篇文章中，我将带你一段一段地看代码，解释你需要知道的一切。在本教程结束时，您将知道如何构建简单的 Flask web 应用程序，并很好地理解如何创建数据库模型、添加用户注册和登录到您的站点等。

通过这篇文章，你将了解并使用以下工具:

*   [Flask](https://github.com/pallets/flask) —我最喜欢的 Python web 框架。它很小，很小，很简单。
*   Flask-SQLAlchemy —一个非常流行的 Flask ORM。它允许您与 Postgres、MySQL、SQLite 等关系数据库服务器进行交互。在本教程中，我们将使用 SQLite 作为我们的数据库，但是其他任何数据库都可以在不修改代码的情况下工作得很好。
*   一个用于 Flask 的 OpenID 连接库。 [OpenID Connect](https://developer.okta.com/blog/2017/07/25/oidc-primer-part-1) 是一个处理用户认证和授权的开放协议。这是处理网络认证的“现代”方式。
*   Okta —一个免费使用的 API 服务，充当 OpenID Connect 授权服务器。Okta 将为您的应用程序存储用户帐户，并使其能够处理用户注册、登录等。以一种简单的方式。
*   [python-slugify](https://github.com/un33k/python-slugify) —一个简单的 python 库，可以生成 web 友好的 URL。我们将用它来将博客文章的标题转换成好看的 URL。

如果你想跳过教程并查看完整构建的项目，你可以在 GitHub 上查看。

## 用 Okta 初始化你的 Flask + Python App 的认证

Okta 是一个免费使用的 API 服务，它存储用户帐户并处理用户认证、授权、社交登录、密码重置等。—简单。Okta 利用像 OpenID Connect 这样的开放标准来实现无缝集成。

在本教程中，您将使用 Okta 来存储您的 web 应用程序的用户帐户，并且您将使用 OpenID Connect 与 Okta 进行对话，以处理与身份验证和授权相关的后勤工作。

首先，你需要创建一个免费的 Okta 开发者账户:[https://developer.okta.com/signup/](https://developer.okta.com/signup/)。一旦你创建了你的帐户并登录，按照下面的步骤配置 Okta，然后你就可以准备写一些代码了！

[![Okta registration page](img/bd3e917a88358a9a41320973c6fc4dc1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7w2fM5ha--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/okta-registration-page-ee14e7516bdc7df3e6e950a2c385706754c5300d842a1374785fd9e43cab2a6b.png)

### 第一步:存储您的组织网址

您需要做的第一件事是从 Okta 仪表板页面的右上方复制下 **Org URL** 。此 URL 将用于路由到您的授权服务器，与之通信，等等。稍后您将需要这个值，所以不要忘记它。

[![Okta org URL](img/b2c3bdc39749480ad8ce66d8c88913b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vCuhpjiT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/okta-org-url-b26a98af3fa71a8f88519b5154d16d10fae846ff3df95d09995fcd61fa2c6175.png)

### 步骤 2:创建一个 OpenID Connect 应用程序

Okta 允许您为您可能创建的多个应用程序存储和管理用户。这意味着在我们继续之前，您需要为这个项目创建一个新的 OpenID Connect 应用程序。

OpenID Connect 中的应用程序有一个用户名和密码(称为客户端 ID 和客户端密码)，允许您的授权服务器在任何给定时间识别哪个应用程序正在与它对话。

要创建新的应用程序，请浏览到**应用程序**选项卡，并点击**添加应用程序**。

[![Okta application page](img/fdd9edd21a6a6e3be4c9ac1dfe3a9299.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FowQiDOs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/okta-application-page-5d3d0ee99d0d6888f30bffc5fa3d58ddaafe907dfd2ec315589435b2fc7bb23b.png)

接下来，点击 **Web** 平台选项(因为这个项目是一个 web app)。

[![Okta create application page](img/d433287db6a575d40628641c1b8f6b8a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xa2sD_OJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/okta-create-application-page-681dcd71ac7de879a92e80d243020f9c9be6edc17e6ae0b0adb40a70936eac24.png)

在“设置”页面上，输入以下值:

*   **名称**:简易烧瓶 App
*   **基地 URIs** : `http://localhost:5000`
*   **登录重定向 URIs** : `http://localhost:5000/oidc/callback`

您可以保持所有其他值不变。

[![Okta create application settings page](img/4ea0202a583f20412913a0c1c03e4267.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C6IDt-Ez--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/okta-create-application-settings-page-1daaf2e72b07b2771b7197f04366cc59f3245a538fbcdb75ccec503494ebd681.png)

现在您的应用程序已经创建好了，请记下下页的**客户端 ID** 和**客户端秘密**值，稍后我们开始编写代码时会用到它们。

[![Okta application credentials page](img/72a2baf4384c4b3b62a082d5f384e4cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_Unft-5b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/okta-application-credentials-page-2f5b916ff40dc7425e731c801f9b087342846ed828a7979ab79127059d67d6b3.png)

### 步骤 3:创建认证令牌

为了访问 Okta APIs 并能够以更精细的方式管理您的用户帐户，您还需要创建一个 Okta 身份验证令牌。这是一个 API 密钥，稍后将用于与 Okta APIs 通信，并允许您执行以下操作:

*   创建、更新和删除用户
*   创建、更新和删除组
*   管理应用程序设置
*   等等。

要创建认证令牌，单击页面顶部的 **API** 选项卡，然后单击**创建令牌**按钮。给你的令牌起一个名字，最好和你的应用程序同名，然后点击**创建令牌**。创建令牌后，请将令牌值复制下来，因为您稍后会需要它。

[![Okta create token page](img/a2275795d61c2161d4c96694cfd7759c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nZGRY1qL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/okta-create-token-page-008a4364f7541ac93b9baf5d3b381ba889bd7188fd6101b19901f0cdf9821a6c.png)

### 第四步:启用用户注册

您需要完成的最后一项设置是为授权服务器启用用户注册功能。通常，授权服务器只支持登录、注销之类的功能。但是 Okta 的授权服务器也支持自助注册，用户可以创建账号、登录账号、重置密码，基本上所有的事情都不用你为它写任何代码。

在您的 Okta 仪表板中，您会注意到页面左上角有一个标有 **< >开发者控制台**的小按钮。将鼠标悬停在该按钮上，选择出现的**经典用户界面**菜单选项。

[![Okta classic UI dropdown](img/6fd5923038ef889da308dac84ef129c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LY5EAKle--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/okta-classic-ui-dropdown-249d1b924c3db38af5d62bf17be65a32579117d9d23c8c28cb7e365eba7d8f85.png)

接下来，将鼠标悬停在页面顶部的**目录**选项卡上，然后选择**自助注册**菜单项。在此页面上点击**启用注册**按钮。

[![Okta enable self-service registration page](img/77cdd3e534817f7865786d421148f8ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v1hSbBWf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/okta-enable-self-service-registration-page-2cfef05884c196fa53bc7c55728c77437713fffa5d549e1874e18c2593825b1f.png)

在配置页面上，将所有设置保留为默认值，只有两项除外:

*   禁用**用户必须验证要激活的电子邮件地址。**复选框。此设置取消了新用户在被允许访问您的 web 应用程序之前验证其电子邮件地址的要求。
*   点击**自定义 URL** 单选框，输入`http://localhost:5000/dashboard`作为值，设置**默认重定向**选项。此设置告诉授权服务器在用户在您的站点上成功创建新帐户后将他们重定向到哪里。

一旦你点击了**保存**，你需要做的最后一件事就是切换回开发者控制台。

[![Okta enable self-service registration settings page](img/3c210baa10510aac921e70cee08be355.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gfaq6RwM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/okta-self-service-registration-settings-page-c39b81450dd9cf108d58625f7552da1982902c7ff175e737396e051be29f19e8.png)

将鼠标悬停在页面右上角的**经典 UI** 按钮上，从下拉菜单中选择 **< >开发者控制台**菜单项。

## 安装 Python 和 Flask 的依赖关系

初始化 Flask 应用程序的第一件事是安装所有必需的依赖项。如果您的计算机上还没有安装 Python，请前往[立即安装](https://www.python.org/downloads/)。确保使用最新的 Python 3+版本。

**注**:我也强烈建议你有空的时候熟悉一下 [pipenv](https://docs.pipenv.org/) 。这是一个非常棒的工具，它使得管理 Python 依赖关系变得非常简单。

现在安装该应用程序所需的依赖项。

```
pip install Flask==1.0.2
pip install Flask-SQLAlchemy==2.3.2
pip install flask-oidc==1.4.0
pip install python-slugify==1.2.5
pip install okta==0.0.4 
```

Enter fullscreen mode Exit fullscreen mode

## 初始化你的烧瓶应用程序

既然已经安装了依赖项，让我们从创建一个简单的 Flask 应用程序开始。我们将建立在这个简单的“你好，世界！”应用程序，直到我们获得所有的功能包括在内。

首先，为您的项目创建一个新目录。

```
mkdir simple-flask-app
cd simple-flask-app 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在项目中创建一个目录来保存 Flask 应用程序代码。这个文件夹将保存所有的项目源代码。

```
mkdir blog 
```

Enter fullscreen mode Exit fullscreen mode

创建一个`blog/ __init__.py`文件并输入以下代码。这个文件将保存你的 Flask 应用程序的初始化代码。

```
from flask import Flask

app = Flask( __name__ )

@app.route("/")
def index():
    return "hello, world!" 
```

Enter fullscreen mode Exit fullscreen mode

打开终端并运行以下代码来启动您的新 Flask 应用程序。

```
FLASK_APP=blog flask run 
```

Enter fullscreen mode Exit fullscreen mode

一旦你的 Flask 应用程序运行，在浏览器中访问`http://localhost:5000`查看 hello world 消息！

从上面的小文件中可以看出，用 Flask 构建一个极简的应用程序真的很简单。你需要做的就是:

*   导入烧瓶库
*   创建烧瓶应用程序对象
*   定义一个函数(称为视图),当用户请求一个特定的 URL(在本例中为`/` URL)时，该函数将运行

不错吧？

## 创建烧瓶模板

我主要是一名后端开发人员。我做一些基本的前端工作，但这不是我的强项。正因为如此，我喜欢先解决棘手的问题，在我的例子中，棘手的问题就是*模板化*。

用 Flask 构建网站时，通常会使用内置的 [Jinja2](http://jinja.pocoo.org/docs/2.10/) 模板语言。

你可以把 Jinja2 想象成带有一点额外东西的 HTML:变量、过滤器和其他一些工具，它们使得构建大型网站变得更加简单。我发现 Jinja2 很容易上手，一旦你掌握了窍门，我相信你也会喜欢它的。

要开始创建模板，让我们创建必要的模板文件，我们将填写这些文件。

```
mkdir -p blog/static
touch simple-flask-app/blog/static/style.css
mkdir -p blog/templates/blog
touch blog/templates/{403.html,404.html,layout.html}
touch blog/templates/blog/{dashboard.html,edit.html,index.html,post.html} 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该拥有以下目录结构。

```
simple-flask-app
└── blog
    ├── __init__.py
    ├── static
    │ └── style.css
    └── templates
        ├── 403.html
        ├── 404.html
        ├── blog
        │ ├── dashboard.html
        │ ├── edit.html
        │ ├── index.html
        │ └── post.html
        └── layout.html 
```

Enter fullscreen mode Exit fullscreen mode

`templates`文件夹保存了项目的所有模板。该文件夹中有一些“顶级”模板:

*   `layout.html` —该模板是所有其他模板的基础。它包含所有的样本 HTML 代码等。所有其他页面将彼此共享。
*   `403.html` —如果用户遇到 403 错误，该模板将显示给用户
*   `404.html` —如果用户遇到 404 错误，该模板将显示给用户
*   `static/` —该文件夹保存网站的所有静态文件(图像、CSS 等)。)
*   `static/style.css` —该文件保存所有网站样式
*   `blog/` —此文件夹包含所有与博客相关的模板
*   `blog/index.html` —博客主页
*   `blog/dashboard.html` —用户仪表板
*   `blog/edit.html` —帖子编辑页面
*   `blog/post.html` —显示单个帖子的页面

接下来，将下面的 CSS 复制到`blog/static/style.css`文件中。

```
footer {
  text-align: center;
  font-style: italic;
  margin-top: 1em;
}

.nav {
  float: right;
}

h2 {
  margin-bottom: 2em;
}

.posts ul {
  list-style-type: none;
}

.posts a {
  font-size: 1.3em;
  text-decoration: underline;
  color: #212529;
}

.posts span {
  font-size: 1.1em;
  float: right;
}

.empty {
  font-size: 2em;
  margin-bottom: 5em;
}

.container {
  padding-top: 2em;
}

.unauthenticated p {
  font-size: 1.3em;
  text-align: center;
}

hr.bottom {
  margin-top: 4em;
}

.submit-btn {
  float: right;
}

.alert p {
  font-size: 1.1em;
}

.author {
  font-size: 1.2em;
  margin-top: 2em;
}

.body {
  margin-top: 2em;
  font-size: 1.2em;
}

.edit {
  padding-left: 0;
}

.edit a {
  text-decoration: underline;
  color: #212529;
  font-size: 1.5em;
}

.edit li {
  list-style-type: none;
  line-height: 2.5em;
}

.edit button {
  float: right;
}

.delete {
  margin-left: 1em;
}

.your-posts {
  margin-top: 2em;
}

.hidden {
  display: inline;
} 
```

Enter fullscreen mode Exit fullscreen mode

我不会解释这一点，因为我假设你熟悉 CSS。我基本上只是定义了一些简单的样式，稍后会用到。

接下来，让我们创建`blog/templates/layout.html`模板。此模板包含可重用的 HTML，站点的所有其他页面都将共享它。

```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.0/css/bootstrap.min.css" integrity="sha384-9gVQ4dYFwwWSjIDZnLEWnxCjeSWFphJiwGPXr1jddIhOegiu1FwO5qRGvFXOdJZ4" crossorigin="anonymous">
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
    Blog | {% block title %}{% endblock %}
  </head>
  <body>
    <div class="d-flex flex-column flex-md-row align-items-center p-3 px-md-4 mb-3 bg-white border-bottom box-shadow">
      <h5 class="my-0 mr-md-auto font-weight-normal">Blog</h5>
      <nav class="my-2 my-md-0 mr-md-3">
        <a class="p-2 text-dark" href="/" title="Home">Home</a>
        {% if not g.user %}
          <a class="p-2 text-dark" href="/login">Log In / Register</a>
        {% else %}
          <a class="p-2 text-dark" href="/dashboard">Dashboard</a>
          <a class="p-2 text-dark" href="/logout">Logout</a>
        {% endif %}
      </nav>
    </div>
    <div class="container">
      {% block body %}{% endblock %}
    </div>
    <footer class="text-center">Created by <a href="https://twitter.com/rdegges">@rdegges</a>, built using <a href="https://twitter.com/okta">@okta</a>.
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这个页面的大部分只是标准的 HTML。你可以很快地看到有一个 head 标签，包括 [Bootstrap](https://getbootstrap.com/) UI 库(让事情看起来体面)，一个将显示在网站所有页面上的 navbar，以及一个页脚。除此之外，还有几个关键因素使得这个模板很特别。我们来分析一下。

```
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}"> 
```

Enter fullscreen mode Exit fullscreen mode

上面 head 部分中的 link 标签使用了一个特殊的函数`url_for`，来正确地链接到上面创建的`style.css`文件。这是一个内置的 Flask 函数，它根据您的 Flask 配置计算出静态文件将在哪个 URL 上提供服务。这比硬编码像`/static/style.css`这样的东西要好得多，因为在未来的任何时候，你都可以改变 Flask 设置，并将所有静态文件移动到一个新的 URL。

你可能也注意到了特殊的博客标签:

```
Blog | {% block title %}{% endblock %}

<!-- ... -->

{% block body %}{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

这些特殊的`block`标签充当未来值的占位符。由于这个模板将在我们网站的每个页面上重复使用，如果网站的每个页面都必须有完全相同的标题或相同的正文内容，那就太傻了。要解决这个问题，您可以使用上面显示的`block`标记。子模板将能够插入他们自己的标题和正文内容到这个模板中，以生成一个完整的页面，而无需重复代码！

最后，看看 navbar 代码和它包含的`if/else`语句。

```
{% if not g.user %}
  <a class="p-2 text-dark" href="/login">Log In / Register</a>
{% else %}
  <a class="p-2 text-dark" href="/dashboard">Dashboard</a>
  <a class="p-2 text-dark" href="/logout">Logout</a>
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个 Jinja2 条件块，工作起来很直观。如果变量`g.user`不存在，那么用户将在导航栏中看到一个按钮，提示他们登录或注册。否则，用户将看到一个仪表板和注销按钮。

将来,`g.user`变量将包含一个登录的用户帐户(如果存在的话),这是这个条件将检查的内容。但稍后会详细介绍！

接下来，将下面的代码复制到`blog/templates/403.html`文件中。

```
{% extends "layout.html" %}

{% block title %}Insufficient Permissions{% endblock %}

{% block body %}
  <h1 class="text-center">Insufficient Permissions</h1>
  <p class="text-center">Mind your own business &gt;:S</p>
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

这是子模板的样子。它使用特殊的`extends`标签来“扩展”您刚刚创建的`layout.html`模板。

在这个模板中，`block`标签之间的内容被插入到指定的父模板(`layout.html`)中。该模板在呈现时将显示一个完整的页面，而不需要复制任何代码。相当整洁！

接下来，将下面的代码复制到`blog/templates/404.html`文件中。当用户访问未找到的页面时，此页面将显示给用户。

```
{% extends "layout.html" %}

{% block title %}Page Not Found{% endblock %}

{% block body %}
  <h1 class="text-center">Page Not Found</h1>
  <p class="text-center">Whatever you did, it's not working :(</p>
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

现在将下面的代码复制到`blog/templates/blog/index.html`文件中。此模板将成为我们正在创建的博客应用程序的主页。它将列出博客文章，以便读者可以查看。

```
{% extends "layout.html" %}

{% block title %}Home{% endblock %}

{% block body %}
  <h2 class="text-center">Recent Posts</h2>
  {% if posts %}
    <div class="posts">
      <ul>
        {% for post in posts %}
          <div class="row">
            <div class="offset-sm-2 col-sm-8">
              <li>
                <a href="/{{ post.slug }}" title="{{ post.title }}">{{ post.title }} <span>  by {{ post.author_name }}</span></a>
              </li>
            </div>
          </div>
        {% endfor %}
      </ul>
    </div>
  {% else %}
    <p class="empty text-center">Uh-oh. There are no posts to view.</p>
  {% endif %}
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

注意，这个模板使用了`for`标签来遍历一组`post`对象，然后通过使用`post`对象属性，例如`post.author_name`、`post.title`等，生成到每个博客文章的链接。使用`{{ ... }}`语法可以将变量值输出到页面的 HTML 中。

现在将下面的代码复制到`blog/templates/blog/dashboard.html`文件中。这将是作者可以用来管理他们的文章的仪表板。它将允许他们创建文章、编辑文章和删除文章。

```
{% extends "layout.html" %}

{% block title %}Dashboard{% endblock %}

{% block body %}
  <div class="row">
    <div class="offset-sm-2 col-sm-8">
      <h2>Create a Post</h2>
    </div>
  </div>

  {% if post %}
    <div class="row">
     <div class="offset-sm-2 col-sm-8">
       <div class="alert alert-successful text-center" role="alert">
         <p>Your new post was created successfully! <a href="/{{ post.slug }}">View it?</a>
        </div>
      </div>
    </div>
  {% endif %}

   <div class="row">
    <div class="offset-sm-2 col-sm-8">
      <form method="post">
        <div class="form-group">
          <label for="title">Post Title</label>
          <input class="form-control" id="title" type="text" name="title" placeholder="Title" required>
        </div>
        <div class="form-group">
          <label for="title">Post Body</label>
          <textarea class="form-control" id="post" name="body" rows="6" required></textarea>
        </div>
        <button class="btn btn-primary submit-btn" type="submit">Update</button>
      </form>
    </div>
  </div>

  <div class="row">
    <div class="offset-sm-2 col-sm-8">
      <h2 class="your-posts">Your Posts</h2>
      <ul class="edit">
        {% for post in posts %}
          <li>
            <a href="/{{ post.slug }}" title="{{ post.title }}">{{ post.title }}</a>
            <form class="hidden" method="post" action="/{{ post.slug }}/delete">
              <button class="btn btn-outline-danger delete">Delete</button>
            </form>
            <a href="/{{ post.slug }}/edit" title="{{ post.title }}">
              <button class="btn btn-outline-secondary">Edit</button>
            </a>
          </li>
        {% endfor %}
      </ul>
    </div>
  </div>
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

让我们继续将下面的代码复制到`blog/templates/blog/edit.html`文件中。当用户想要编辑他们的文章时，这个模板将显示给用户。

```
{% extends "layout.html" %}

{% block title %}{{ post.title }}{% endblock %}

{% block body %}
  <h2 class="text-center">Edit Post</h2>
  <div class="row">
    <div class="offset-sm-2 col-sm-8">
      <form method="post">
        <div class="form-group">
          <label for="title">Post Title</label>
          <input class="form-control" id="title" type="text" name="title" value="{{ post.title }}" required>
        </div>
        <div class="form-group">
          <label for="title">Post Body</label>
          <textarea class="form-control" id="post" name="body" rows="6" required>{{ post.body }}</textarea>
        </div>
        <button class="btn btn-primary submit-btn" type="submit">Update</button>
      </form>
    </div>
  </div>
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

最后，将下面的代码复制到`blog/templates/blog/post.html`文件中。当用户试图查看博客文章时，将向用户显示该模板。

```
{% extends "layout.html" %}

{% block title %}{{ post.title }}{% endblock %}

{% block body %}
  <h2 class="text-center">{{ post.title }}</h2>
  <div class="row">
    <div class="offset-sm-2 col-sm-8">
      <div class="body">{{ post.body|safe }}</div>
      <p class="author">Written by {{ post.author_name }}</p>
    </div>
  </div>
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

至此，我们已经完成了模板的创建！耶！

现在您还不能测试这个(我们还没有编写任何代码来使这个东西工作)，但是不要担心，我们很快就会实现。

## 为你的 Flask + Python App 创建数据库模型

接下来我们要做的是为应用程序创建必要的数据库模型。

一旦构建了前端模板，应用程序的下一个复杂部分就是数据模型。您的应用程序将存储和引用哪些数据？我喜欢在早期定义这些模型，这样我就知道以后随着应用程序的发展如何将这些东西组合在一起。

对于这个应用程序，我们将建立一个简单的博客。这意味着我们将主要存储两部分数据:用户帐户数据和博客文章数据。因为我们将使用 OpenID Connect 和 Okta 来存储我们的用户帐户数据，所以只剩下博客文章数据由我们来存储和管理。

我们的应用程序中的一篇博客文章需要几个关键的数据:

*   一个`id`字段，因此我们可以在数据库中为每篇博客文章分配一个唯一的标识符
*   一个`author_id`字段，这样我们可以看到哪个作者写了一篇博客文章
*   一个`created`字段，这样我们可以看到一篇博客文章是在什么日期和时间创建的
*   一个`title`字段，这样我们可以看到每篇博客文章的标题是什么
*   一个`body`字段，所以我们可以看到每篇博客文章的内容是什么(这将是 HTML)
*   一个`slug`字段，这样我们可以确定将每篇博客文章显示为哪个 URL(例如，一篇标题为“我最喜欢的文章”的博客文章可能在 URL[https://ourblog.com/my-favorite-post](https://ourblog.com/my-favorite-post)可用，因此“我最喜欢的文章”将是 slug)

现在我们知道了哪些数据是存储的关键，让我们设置 Flask-SQLAlchemy(我们将用来管理数据库的 ORM 库)以及我们的数据库模型。

### 初始化烧瓶-SQLAlchemy

为了连接我们的数据库，第一步是让 Flask-SQLAlchemy 工作。

创建一个新文件，`blog/db.py`，并复制下面的代码。这个文件将保存我们所有的数据库相关代码。

```
from datetime import datetime

from click import command, echo
from flask_sqlalchemy import SQLAlchemy
from flask.cli import with_appcontext

# Initialize SQLAlchemy with no settings
db = SQLAlchemy()

@command("init-db")
@with_appcontext
def init_db_command():
    """Initialize the database."""
    db.create_all()
    echo("Initialized the database.")

def init_app(app):
    """Initialize the Flask app for database usage."""
    db.init_app(app)
    app.cli.add_command(init_db_command) 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看这里发生了什么。

首先，我们创建一个`db`全局对象。这将用于稍后创建我们的数据库模型，并管理我们与数据库的关系。当我们初始化`db`对象时，我们正在初始化 Flask-SQLAlchemy 扩展，但实际上没有给它任何设置或配置。我们很快就会谈到这一点。

接下来是`init_db_command`功能。这是一个特殊的命令，当您键入以下内容时，您将能够在命令行上运行它。

```
FLASK_APP=blog flask init-db 
```

Enter fullscreen mode Exit fullscreen mode

该命令运行 SQLAlchemy 提供的一个名为`create_all`的特殊方法，该方法将初始化任何指定的数据库表和配置。现在这不会做任何事情，但是一旦我们马上定义数据库模型，这将变得有用。

使用 Flask 附带的 [click](http://click.pocoo.org/5/) 库构建`init_db_command`函数。click 是一个非常流行的构建命令行应用程序的库，就像这个。

接下来是`init_app`功能。这意味着从主应用程序初始化代码运行，它将正确配置 Flask-SQLAlchemy 扩展，并以正确的方式将`init_db_command`函数连接到应用程序。

现在我们的数据库代码已经写好了，将下面的代码复制到`blog/ __init__.py`文件中，覆盖之前的内容。

```
from os.path import dirname, join

from flask import Flask

from . import db

app = Flask( __name__ )
app.config.from_mapping(
    SQLALCHEMY_DATABASE_URI="sqlite:///" + join(dirname(dirname( __file__ )), "database.sqlite"),
)

db.init_app(app) 
```

Enter fullscreen mode Exit fullscreen mode

在应用程序的“主”部分，我们正在做的是导入刚刚创建的数据库模型(`blog/db.py`)，指定一些应用程序配置数据(告诉 Flask-SQLAlchemy 我们正在使用什么类型的数据库)，最后，调用我们在`blog/db.py`文件中定义的特殊的`db.init_app()`函数，它正确地初始化 Flask-SQLAlchemy。

对于几乎所有的 Flask 应用程序，在某些时候你需要指定一些扩展或助手库使用的配置信息。你可以通过上面的`app.config.from_mapping`调用来完成。此方法允许您定义可以在 Flask 应用程序中共享的配置数据和设置。

在本例中，我们指定了 Flask-SQLAlchemy 需要的设置，`SQLALCHEMY_DATABASE_URI`，这基本上告诉它我们正在使用什么类型的数据库以及如何访问它。在本例中，我们将把 SQLite 数据库存储在项目文件夹根目录下的一个名为`database.sqlite`的文件中。

对于这个博客应用程序，我们将使用 [SQLite 数据库](https://www.sqlite.org/index.html)。SQLite 是一个非常受欢迎的数据库，它将所有数据存储在磁盘上的一个文件中。这使得它非常适合构建便携式应用程序。如果您正在构建需要高吞吐量和数据处理水平的东西，SQLite 可能不是最合适的…但是对于几乎所有其他东西，它不仅简单，而且非常方便，因为您甚至不需要数据库服务器！

现在，如果您返回并重新运行 Flask 应用程序，您会注意到它应该运行良好。唯一的问题是，除了初始化数据库，我们还没有完成任何事情。

### 创建 SQLAlchemy 数据库模型

既然我们的数据库已经初始化，让我们创建我们需要的实际数据模型。这个模型将允许我们像前面描述的那样将博客文章数据存储到 SQLite 数据库中。

打开`blog/db.py`并插入以下代码，覆盖之前的内容。

```
from datetime import datetime

from click import command, echo
from flask_sqlalchemy import SQLAlchemy
from flask.cli import with_appcontext

# Initialize SQLAlchemy with no settings
db = SQLAlchemy()

class Post(db.Model):
    """A blog post."""
    id = db.Column(db.Integer, primary_key=True)
    author_id = db.Column(db.Text, nullable=False)
    created = db.Column(db.DateTime, default=datetime.utcnow)
    title = db.Column(db.Text, nullable=False)
    body = db.Column(db.Text, nullable=False)
    slug = db.Column(db.Text, nullable=False, unique=True)

@command("init-db")
@with_appcontext
def init_db_command():
    """Initialize the database."""
    db.create_all()
    echo("Initialized the database.")

def init_app(app):
    """Initialize the Flask app for database usage."""
    db.init_app(app)
    app.cli.add_command(init_db_command) 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里添加的是新的`Post`类。这是一个数据库模型，它告诉 SQLAlchemy 我们正在存储什么类型的数据(一篇博客文章)，以及它包含哪些字段。为此，我们使用典型的关系数据库列类型(整数、文本、日期时间等。).如果你不熟悉 SQL，你可能想看看这个关于这个主题的[神奇可汗学院课程](https://www.khanacademy.org/computing/computer-programming/sql)。

既然已经定义了数据库模型，我们需要做的就是初始化数据库，这样我们就可以开始从数据库中读写数据了。为此，让我们使用之前创建的新的`init-db`命令。

```
FLASK_APP=blog flask init-db 
```

Enter fullscreen mode Exit fullscreen mode

如果您查看项目文件夹的根目录，您现在会看到一个名为`database.sqlite`的文件。这是您所有应用程序数据的存储位置！

## 设置用户注册、用户登录等。在烧瓶中

您的数据库现在已经准备好了，但是您仍然有一个问题:您如何允许用户登录到您的网站，创建帐户，等等。？答案很简单:OpenID Connect 和 [Okta](https://developer.okta.com/) ！

### 步骤 1:创建一个 OpenID 连接配置文件

在项目文件夹的根目录下创建一个名为`client_secrets.json`的新文件，并插入以下代码。

```
{
  "web": {
    "client_id": "{{ OKTA_CLIENT_ID }}",
    "client_secret": "{{ OKTA_CLIENT_SECRET }}",
    "auth_uri": "{{ OKTA_ORG_URL }}/oauth2/default/v1/authorize",
    "token_uri": "{{ OKTA_ORG_URL }}/oauth2/default/v1/token",
    "issuer": "{{ OKTA_ORG_URL }}/oauth2/default",
    "userinfo_uri": "{{ OKTA_ORG_URL }}/oauth2/default/userinfo",
    "redirect_uris": [
      "http://localhost:5000/oidc/callback"
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

确保用实际的 Okta 信息替换占位符变量。

在您的仪表板页面上将`{{ OKTA_ORG_URL }}`替换为组织 URL 在您的应用程序页面上将`{{ OKTA_CLIENT_ID }}`替换为客户 ID 在您的应用程序页面上将`{{ OKTA_CLIENT_SECRET }}`替换为客户机密

这个文件将被我们马上要配置的 Flask-OIDC 库使用。这些设置实际上告诉 OpenID Connect 库您正在使用哪个 OpenID Connect 应用程序进行身份验证，以及您的授权服务器 API 端点是什么。

上面的 URIs 指向您新创建的 Okta 资源，以便 Flask 库能够正确地与它对话。

### 第二步:配置烧瓶-OIDC

打开`blog/ __init__.py`并粘贴以下代码。

```
from os.path import dirname, join

from flask import Flask, g

from . import auth, db

app = Flask( __name__ )
app.config.from_mapping(
    SECRET_KEY={{ LONG_RANDOM_STRING }},
    OIDC_CLIENT_SECRETS=join(dirname(dirname( __file__ )), "client_secrets.json"),
    OIDC_COOKIE_SECURE=False,
    OIDC_CALLBACK_ROUTE="/oidc/callback",
    OIDC_SCOPES=["openid", "email", "profile"],
    OIDC_ID_TOKEN_COOKIE_NAME="oidc_token",
    SQLALCHEMY_DATABASE_URI="sqlite:///" + join(dirname(dirname( __file__ )), "database.sqlite"),
)

auth.oidc.init_app(app)
db.init_app(app)

app.register_blueprint(auth.bp)

@app.before_request
def before_request():
    """
    Load a user object into `g.user` before each request.
    """
    if auth.oidc.user_loggedin:
        g.user = auth.okta_client.get_user(auth.oidc.user_getfield("sub"))
    else:
        g.user = None 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做的是为弗拉斯克-OIDC 库指定设置:

*   `OIDC_CLIENT_SECRETS`设置告诉 Flask-OIDC 你的 OpenID 连接配置文件(你在上一节创建的那个)在哪里。
*   `OIDC_COOKIE_SECURE`设置允许您在不使用 SSL 的情况下测试开发中的用户登录和注册。如果你打算公开运行你的站点，你应该去掉这个选项，在你的站点上使用 SSL。
*   设置告诉弗拉斯克-OIDC 在你的网站上什么 URL 将处理用户登录。这是 OpenID 连接流程的标准部分。这超出了本文的范围，但是如果你想了解更多，请阅读我们的 [OpenID Connect primer](https://developer.okta.com/blog/2017/07/25/oidc-primer-part-1) 。
*   `OIDC_SCOPES`设置告诉弗拉斯克-OIDC 公司当用户登录时需要什么数据。在本例中，我们请求基本的用户信息(电子邮件、姓名等。).
*   `SECRET_KEY`设置应设置为一个*长的*，随机字符串。这是用来保护你的 Flask session(cookies)的，这样就没人能篡改它们了。确保这个变量是私有的。绝不应该公开曝光。

一旦创建了配置数据，我们还使用一个(目前)未定义的`auth`模块(我们稍后会讲到)来初始化 Flask-OIDC 库。

然后我们注册一个蓝图(这将在下一节讨论)。

最后，我们定义一个名为`before_request`的特殊函数。这个函数将在每个用户请求之前自动运行(因此有了`@app.before_request`装饰器)，并在用户对象可用时加载它。

这是如何工作的。

*   一旦用户使用 OpenID Connect 登录，他们将创建一个会话并存储在服务器端 cookie 中
*   `if auth.oidc.user_loggedin`代码将检查这个 cookie 是否存在以及是否有效。如果是，那么我们将使用 [okta Python 库](https://github.com/okta/okta-sdk-python)来检索用户的帐户，作为一个我们可以轻松使用的`User`对象，并将其赋给特殊变量`g.user`。
*   如果没有用户登录，那么`g.user`将被设置为`None`。

特殊的`g.user`值是我们可以用来存储应用程序需要的重要数据的东西，比如当前用户。我们可以在模板、服务器端代码等中使用这个变量。这使得它非常方便。

### 步骤 3:在 Flask 中创建用户登录和注销视图

创建一个新文件`blog/auth.py`，并粘贴以下代码。这个文件将保存我们所有的认证相关代码。

```
from os import environ

from flask import Blueprint, redirect, url_for
from flask_oidc import OpenIDConnect
from okta import UsersClient

bp = Blueprint("auth", __name__ , url_prefix="/")
oidc = OpenIDConnect()
okta_client = UsersClient("{{ OKTA_ORG_URL }}", "{{ OKTA_AUTH_TOKEN }}")

@bp.route("/login")
@oidc.require_login
def login():
    """
    Force the user to login, then redirect them to the dashboard.
    """
    return redirect(url_for("blog.dashboard"))

@bp.route("/logout")
def logout():
    """
    Log the user out of their account.
    """
    oidc.logout()
    return redirect(url_for("blog.index")) 
```

Enter fullscreen mode Exit fullscreen mode

确保用适当的 Okta 值替换`OKTA_ORG_URL`和`OKTA_AUTH_TOKEN`。

这个文件的作用是:

*   定义一个特殊的`auth`蓝图。烧瓶中的蓝图是模块化代码的方法，使其在大型系统中可重用。每个蓝图都有一个名称、一个 URL 前缀(在这种情况下，这是不相关的)，以及它自己的迷你应用程序对象。
*   定义一个`okta_client`对象。这允许我们与 Okta API 对话来检索用户数据。虽然这不是绝对必要的，但它允许我们更容易地与用户一起工作。
*   定义一个`login`视图来处理用户登录功能。这些视图函数的作用是:当用户访问`/login`时，如果用户已经登录(通过`@oidc.require_login`装饰器)，他们将通过`redirect(...)`函数调用被重定向到仪表板页面。如果用户没有登录，`@oidc.require_login`装饰器会将用户重定向到 Okta 授权服务器，并提示他们要么创建一个新帐户(用户注册)，要么登录一个现有的帐户。
*   定义一个`logout`视图来处理注销功能。如果用户访问`/logout` URL，他们的会话 cookie 将被删除(通过`oidc.logout()`调用)，然后他们将被重定向到网站的主页。

恭喜，您现在已经将用户注册、登录和用户管理完全集成到您的应用程序中了！不错吧？

## 定义 Flask 中的博客浏览量

到目前为止，我们已经构建了前端模板，创建了数据库模型，并连接了用户身份验证。然而，我们还没有做的一件事是为我们的应用程序定义核心*行为*:视图！

在 Flask 中，视图是用户访问特定 URL 时运行的函数。例如，如果用户访问`/dashboard`，Flask 需要知道运行什么函数，以及做什么来向用户显示仪表板页面。

既然我们已经想通了其他的事情，让我们开始构建视图吧！

然而，首先，我们需要更新我们的应用程序初始化代码，以便我们即将创建的博客视图能够工作！打开`blog/ __init__.py`并复制以下代码。

```
from os.path import dirname, join

from flask import Flask, g

from . import auth, blog, db

app = Flask( __name__ )
app.config.from_mapping(
    SECRET_KEY='LONG_RANDOM_STRING',
    OIDC_CLIENT_SECRETS=join(dirname(dirname( __file__ )), "client_secrets.json"),
    OIDC_COOKIE_SECURE=False,
    OIDC_CALLBACK_ROUTE="/oidc/callback",
    OIDC_SCOPES=["openid", "email", "profile"],
    OIDC_ID_TOKEN_COOKIE_NAME="oidc_token",
    SQLALCHEMY_DATABASE_URI="sqlite:///" + join(dirname(dirname( __file__ )), "database.sqlite"),
)

auth.oidc.init_app(app)
db.init_app(app)

app.register_blueprint(auth.bp)
app.register_blueprint(blog.bp)

@app.before_request
def before_request():
    """
    Load a user object into `g.user` before each request.
    """
    if auth.oidc.user_loggedin:
        g.user = auth.okta_client.get_user(auth.oidc.user_getfield("sub"))
    else:
        g.user = None

@app.errorhandler(404)
def page_not_found(e):
    """Render a 404 page."""
    return render_template("404.html"), 404

@app.errorhandler(403)
def insufficient_permissions(e):
    """Render a 403 page."""
    return render_template("403.html"), 403 
```

Enter fullscreen mode Exit fullscreen mode

除了导入一个`blog`模块并注册为蓝图之外，我们在这里没有太大的改动。我们还定义了两个额外的函数:一个将在用户访问一个不存在的页面时运行(a 404)，另一个将在用户访问一个他们无权访问的页面时运行(a 403)。

接下来，创建文件`blog/blog.py`并粘贴以下代码。

```
from flask import Blueprint, abort, g, render_template, redirect, request, url_for
from slugify import slugify

from .auth import oidc, okta_client
from .db import Post, db

bp = Blueprint("blog", __name__ , url_prefix="/")

def get_posts(author_id):
    """
    Return all of the posts a given user created, ordered by date.
    """
    return Post.query.filter_by(author_id=author_id).order_by(Post.created.desc())

@bp.route("/")
def index():
    """
    Render the homepage.
    """
    posts = Post.query.order_by(Post.created.desc())
    posts_final = []

    for post in posts:
        u = okta_client.get_user(post.author_id)
        post.author_name = u.profile.firstName + " " + u.profile.lastName
        posts_final.append(post)

    return render_template("blog/index.html", posts=posts_final)

@bp.route("/dashboard", methods=["GET", "POST"])
@oidc.require_login
def dashboard():
    """
    Render the dashboard page.
    """
    if request.method == "GET":
        return render_template("blog/dashboard.html", posts=get_posts(g.user.id))

    post = Post(
        title=request.form.get("title"),
        body=request.form.get("body"),
        author_id = g.user.id,
        slug = slugify(request.form.get("title"))
    )

    db.session.add(post)
    db.session.commit()

    return render_template("blog/dashboard.html", posts=get_posts(g.user.id))

@bp.route("/<slug>")
def view_post(slug):
    """View a post."""
    post = Post.query.filter_by(slug=slug).first()
    if not post:
        abort(404)

    u = okta_client.get_user(post.author_id)
    post.author_name = u.profile.firstName + " " + u.profile.lastName

    return render_template("blog/post.html", post=post)

@bp.route("/<slug>/edit", methods=["GET", "POST"])
def edit_post(slug):
    """Edit a post."""
    post = Post.query.filter_by(slug=slug).first()

    if not post:
        abort(404)

    if post.author_id != g.user.id:
        abort(403)

    post.author_name = g.user.profile.firstName + " " + g.user.profile.lastName
    if request.method == "GET":
        return render_template("blog/edit.html", post=post)

    post.title = request.form.get("title")
    post.body = request.form.get("body")
    post.slug = slugify(request.form.get("title"))

    db.session.commit()
    return redirect(url_for(".view_post", slug=post.slug))

@bp.route("/<slug>/delete", methods=["POST"])
def delete_post(slug):
    """Delete a post."""
    post = Post.query.filter_by(slug=slug).first()

    if not post:
        abort(404)

    if post.author_id != g.user.id:
        abort(403)

    db.session.delete(post)
    db.session.commit()

    return redirect(url_for(".dashboard")) 
```

Enter fullscreen mode Exit fullscreen mode

我知道这需要理解很多，所以让我们一次完成一个函数。

### get _ posts 辅助函数

```
def get_posts(author_id):
    """
    Return all of the posts a given user created, ordered by date.
    """
    return Post.query.filter_by(author_id=author_id).order_by(Post.created.desc()) 
```

Enter fullscreen mode Exit fullscreen mode

这个函数被设计成一个助手。它所做的是查询数据库，寻找任何由特定作者写的文章，并按日期降序返回它们。

这在稍后显示用户仪表板页面时会很有用，因为我们只想向用户显示他们已经在仪表板页面上创建的博客文章。这个函数允许我们轻松地获得这些帖子的列表，而无需重复代码。

### 索引视图

```
@bp.route("/")
def index():
    """
    Render the homepage.
    """
    posts = Post.query.order_by(Post.created.desc())
    posts_final = []

    for post in posts:
        u = okta_client.get_user(post.author_id)
        post.author_name = u.profile.firstName + " " + u.profile.lastName
        posts_final.append(post)

    return render_template("blog/index.html", posts=posts_final) 
```

Enter fullscreen mode Exit fullscreen mode

这个查看功能就是网站的主页。该功能将在用户每次访问`/` URL 时运行。它的作用是:

*   获取网站上所有博客文章的列表，按日期降序排列
*   遍历每篇文章并提取作者的 ID
*   使用 Okta Python 库检索相应用户的名字和姓氏(这将允许我们向博客读者显示作者的信息)
*   呈现我们早些时候创建的`blog/index.html`模板，它显示所有博客文章的列表，以及是谁写的

### 仪表盘视图

```
@bp.route("/dashboard", methods=["GET", "POST"])
@oidc.require_login
def dashboard():
    """
    Render the dashboard page.
    """
    if request.method == "GET":
        return render_template("blog/dashboard.html", posts=get_posts(g.user.id))

    post = Post(
        title=request.form.get("title"),
        body=request.form.get("body"),
        author_id = g.user.id,
        slug = slugify(request.form.get("title"))
    )

    db.session.add(post)
    db.session.commit()

    return render_template("blog/dashboard.html", posts=get_posts(g.user.id)) 
```

Enter fullscreen mode Exit fullscreen mode

该视图功能是用户仪表板页面。该功能将在用户每次访问`/dashboard` URL 时运行。它的作用是:

*   如果用户正在查看仪表板，它只是呈现我们之前定义的`blog/dashboard.html`模板。该模板向用户显示了他们以前创建的博客文章的列表，并且还允许用户通过填写表单来创建新的博客文章。
*   如果用户正在提交一个表单(试图创建一个新的帖子)，这个视图将创建一个新的`Post`对象，并将其保存到数据库中。在这个过程中，它将使用前面提到的 python-slugify 库将文章的标题转换成 URL 友好的格式。

### 视图 _ 帖子视图

```
@bp.route("/<slug>")
def view_post(slug):
    """View a post."""
    post = Post.query.filter_by(slug=slug).first()
    if not post:
        abort(404)

    u = okta_client.get_user(post.author_id)
    post.author_name = u.profile.firstName + " " + u.profile.lastName

    return render_template("blog/post.html", post=post) 
```

Enter fullscreen mode Exit fullscreen mode

这个视图函数呈现文章页面。该功能将在用户每次访问`/<some-blog-slug-here>` URL 时运行。它的作用是:

*   在数据库中查找带有 URL 中指定的 slug 的博客文章。如果可以找到，它将呈现该页面并向用户显示博客。
*   如果找不到匹配的博客文章，用户将看到一个 404 页面，仅此而已

### 编辑 _ 帖子查看

```
@bp.route("/<slug>/edit", methods=["GET", "POST"])
def edit_post(slug):
    """Edit a post."""
    post = Post.query.filter_by(slug=slug).first()

    if not post:
        abort(404)

    if post.author_id != g.user.id:
        abort(403)

    post.author_name = g.user.profile.firstName + " " + g.user.profile.lastName
    if request.method == "GET":
        return render_template("blog/edit.html", post=post)

    post.title = request.form.get("title")
    post.body = request.form.get("body")
    post.slug = slugify(request.form.get("title"))

    db.session.commit()
    return redirect(url_for(".view_post", slug=post.slug)) 
```

Enter fullscreen mode Exit fullscreen mode

此查看功能允许用户编辑帖子。该功能将在用户每次访问`/<some-blog-slug-here>/edit` URL 时运行。它的作用是:

*   在数据库中查找带有 URL 中指定的 slug 的博客文章。如果可以找到，它会相应地更新帖子(假设用户有正确的权限)。如果用户没有正确的权限，他们会看到一个 403 页面。
*   如果找不到匹配的博客文章，用户将看到一个 404 页面，仅此而已

### 删除 _ 帖子查看

```
@bp.route("/<slug>/delete", methods=["POST"])
def delete_post(slug):
    """Delete a post."""
    post = Post.query.filter_by(slug=slug).first()

    if not post:
        abort(404)

    if post.author_id != g.user.id:
        abort(403)

    db.session.delete(post)
    db.session.commit()

    return redirect(url_for(".dashboard")) 
```

Enter fullscreen mode Exit fullscreen mode

此查看功能允许用户删除帖子。该功能将在用户每次访问`/<some-blog-slug-here>/delete` URL 时运行。它的作用是:

*   在数据库中查找带有 URL 中指定的 slug 的博客文章。如果可以找到，它会从数据库中删除帖子(假设用户有正确的权限)。如果用户没有正确的权限，他们会看到一个 403 页面。
*   如果找不到匹配的博客文章，用户将看到一个 404 页面，仅此而已

还有…就是这样！

## 测试你的新 Flask + Python 应用

现在你的应用已经完全构建好了，去测试一下吧！打开`http://localhost:5000`，创建账户，登录等。

[![app usage](img/6c132abd310f45364797d044ea308d74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--02fjitUS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets/blog/build-a-simple-crud-app-with-python-and-flask/app-usage-738749b9ea16985d42493459c6ebc0421da61c19700a0a99f1f9608c50ec63d2.gif)

如你所见，用用户注册、登录、数据库、模板等构建 Flask 应用。不一定很难！

如果你有兴趣了解更多关于 web 认证和安全的知识，你可能还想看看我们的其他文章，或者在 Twitter 上关注我们，我们写了很多有趣的 web 开发主题。

以下是我最喜欢的三个:

*   [烧瓶教程:简单的用户注册和登录](https://developer.okta.com/blog/2018/07/12/flask-tutorial-simple-user-registration-and-login)
*   [在 10 分钟内为任何网页添加认证](https://developer.okta.com/blog/2018/06/08/add-authentication-to-any-web-page-in-10-minutes)
*   如果你的 JWT 被偷了会怎么样？