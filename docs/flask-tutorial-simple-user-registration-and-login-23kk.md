# Flask 教程:简单的用户注册和登录

> 原文：<https://dev.to/oktadev/flask-tutorial-simple-user-registration-and-login-23kk>

Flask 是我最喜欢的 Python web 框架。它很小，很快，最重要的是:*很有趣*。我喜欢 Flask 开发的几乎所有东西，只有一个例外:用户管理。

Flask 中的用户管理，就像许多其他 web 框架一样，很困难。我无法告诉你我已经创建了多少次用户数据库，设置了组和角色，集成了社交登录提供商，处理了密码重置工作流，配置了多因素身份验证工作流等。随着需求的变化，即使是像 [Flask-Login](http://flask-login.readthedocs.io/en/latest/) 和 [Flask-Security](https://pythonhosted.org/Flask-Security/) 这样出色的库也很难长时间设置和维护。

在这个简短的教程中，我将向您展示我认为管理 Flask web 应用程序用户的最好和最简单的方法之一:OpenID Connect。如果你还没听说过， [OpenID Connect](https://developer.okta.com/blog/2017/07/25/oidc-primer-part-1) 是一个开放的协议，它简化了用户认证和授权的管理。

跟随以下内容，您将学会如何:

*   创建一个简单的烧瓶网站
*   使用 OpenID Connect 进行用户验证和授权
*   使用 Okta 作为您的授权服务器，以简单直接的方式存储和管理您的用户帐户

如果你想跳过教程，只查看完整构建的项目，你可以在 GitHub 上查看。

## 用 Okta 初始化你的 Flask App 的认证

Okta 是一个免费使用的 API 服务，它存储用户帐户，并处理用户认证、授权、社交登录、密码重置等。—简单。Okta 利用像 OpenID Connect 这样的开放标准来实现无缝集成。

在本教程中，您将使用 Okta 来存储您的 web 应用程序的用户帐户，并且您将使用 OpenID Connect 与 Okta 进行对话，以处理与身份验证和授权相关的后勤工作。

首先，你需要创建一个免费的 Okta 开发者账户:[https://developer.okta.com/signup/](https://developer.okta.com/signup/)。一旦你创建了你的帐户并登录，按照下面的步骤配置 Okta，然后你就可以准备写一些代码了！

[![Okta registration page](../Images/52ddf91dbe36889e21731976c7dd44be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pUiLgahY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/okta-registration-page-ee14e7516bdc7df3e6e950a2c385706754c5300d842a1374785fd9e43cab2a6b.png)

### 第一步:存储您的组织网址

您需要做的第一件事是从 Okta 仪表板页面的右上方复制下 **Org URL** 。此 URL 将用于路由到您的授权服务器，与之通信，等等。稍后您将需要这个值，所以不要忘记它。

[![Okta org URL](../Images/cbb9fd1220169681bc11ec51f7d1b4df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tq_0r50u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/okta-org-url-b26a98af3fa71a8f88519b5154d16d10fae846ff3df95d09995fcd61fa2c6175.png)

### 步骤 2:创建一个 OpenID Connect 应用程序

Okta 允许您为您可能正在创建的多个应用程序存储和管理用户。这意味着在我们继续之前，您需要为这个项目创建一个新的 OpenID Connect 应用程序。

OpenID Connect 中的应用程序有一个用户名和密码(称为客户端 ID 和客户端密码)，允许您的授权服务器在任何给定时间识别哪个应用程序正在与它对话。

要创建新的应用程序，请浏览到**应用程序**选项卡，并点击**添加应用程序**。

[![Okta application page](../Images/e87558842120ce73488d16a510676ade.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C1rzRuU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/okta-application-page-5d3d0ee99d0d6888f30bffc5fa3d58ddaafe907dfd2ec315589435b2fc7bb23b.png)

接下来，点击 **Web** 平台选项(因为这个项目是一个 web app)。

[![Okta create application page](../Images/b6329c2292b3bd3bfe57019e495ca386.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tvXZAScz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/okta-create-application-page-681dcd71ac7de879a92e80d243020f9c9be6edc17e6ae0b0adb40a70936eac24.png)

在“设置”页面上，输入以下值:

*   **名称**:简易烧瓶 App
*   **基地 URIs** : `http://localhost:5000`
*   **登录重定向 URIs** : `http://localhost:5000/oidc/callback`

您可以保持所有其他值不变。

[![Okta create application settings page](../Images/e8ca1eeec7efcd4e3ab2c354d159d4a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KJsS0yZ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/okta-create-application-settings-page-6113616138c4f65e25532ad8013ed3359a8e389b08f8f093acb25c8ca43f9091.png)

现在您的应用程序已经创建好了，请记下下页的**客户端 ID** 和**客户端秘密**值，稍后我们开始编写代码时会用到它们。

[![Okta application credentials page](../Images/568be6eea8be7a29845039ddae63302f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jw4mcbWk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/okta-application-credentials-page-2f5b916ff40dc7425e731c801f9b087342846ed828a7979ab79127059d67d6b3.png)

### 步骤 3:创建认证令牌

为了访问 Okta APIs 并能够更精细地管理您的用户帐户，您还需要创建一个 Okta 身份验证令牌。这是一个 API 密钥，稍后将用于与 Okta APIs 通信，并允许您执行以下操作:

*   创建、更新和删除用户
*   创建、更新和删除组
*   管理应用程序设置
*   等等。

要创建认证令牌，单击页面顶部的 **API** 选项卡，然后单击**创建令牌**按钮。给你的令牌起一个名字，最好和你的应用程序同名，然后点击**创建令牌**。创建令牌后，请将令牌值复制下来，因为您稍后会需要它。

[![Okta create token page](../Images/793a0dea1bd431baa250cf5119c37cda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j47owuuP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/okta-create-token-page-008a4364f7541ac93b9baf5d3b381ba889bd7188fd6101b19901f0cdf9821a6c.png)

### 第四步:启用用户注册

您需要完成的最后一项设置是为授权服务器启用用户注册功能。通常，授权服务器只支持登录、注销之类的功能。但是 Okta 的授权服务器也支持自助注册，用户可以创建账号、登录账号、重置密码，基本上所有的事情都不用你为它写任何代码。

在您的 Okta 仪表板中，您会注意到页面左上角有一个标有 **< >开发者控制台**的小按钮。将鼠标悬停在该按钮上，选择出现的**经典用户界面**菜单选项。

[![Okta classic UI dropdown](../Images/b6bb70f2d6cbe58c012c062a986e8b24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hGlhVsrV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/okta-classic-ui-dropdown-249d1b924c3db38af5d62bf17be65a32579117d9d23c8c28cb7e365eba7d8f85.png)

接下来，将鼠标悬停在页面顶部的**目录**选项卡上，然后选择**自助注册**菜单项。在此页面上点击**启用注册**按钮。

[![Okta enable self-service registration page](../Images/ed890f904a65739e310b7dcff6e625a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p20vDo0y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/okta-enable-self-service-registration-page-2cfef05884c196fa53bc7c55728c77437713fffa5d549e1874e18c2593825b1f.png)

在配置页面上，将所有设置保留为默认值，只有两项除外:

*   禁用**用户必须验证要激活的电子邮件地址。**复选框。此设置取消了新用户在被允许访问您的 web 应用程序之前验证其电子邮件地址的要求。
*   点击**自定义 URL** 单选框，输入`http://localhost:5000/dashboard`作为值，设置**默认重定向**选项。此设置告诉授权服务器在用户在您的站点上成功创建新帐户后将他们重定向到哪里。

一旦你点击了**保存**，你需要做的最后一件事就是切换回开发者控制台。

[![Okta self-service registration settings page](../Images/2554e18e28d1146d430f8e5ff0b4fdf9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cRYwVTf_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/okta-self-service-registration-settings-page-5b1f7922a1342ce35f0f876587e479660d0f51f6fa59d69b239a6d965da8dbf7.png)

将鼠标悬停在页面右上角的**经典 UI** 按钮上，从下拉菜单中选择 **< >开发者控制台**菜单项。

## 安装 Python 和 Flask 的依赖关系

为了初始化 Flask 应用程序，您需要做的第一件事是安装所有必需的依赖项。如果您的计算机上还没有安装 Python，请前往[立即安装](https://www.python.org/downloads/)。

**注**:我也强烈建议你有空的时候熟悉一下 [pipenv](https://docs.pipenv.org/) 。这是一个非常棒的工具，它使得管理 Python 依赖关系变得非常简单。

现在安装该应用程序所需的依赖项。

```
pip install Flask>=1.0.0
pip install flask-oidc>=1.4.0
pip install okta==0.0.4 
```

Enter fullscreen mode Exit fullscreen mode

## 初始化你的烧瓶应用程序

既然已经安装了依赖项，让我们从创建一个简单的 Flask 应用程序开始。我们将建立在这个简单的“你好，世界！”应用程序，直到我们获得所有的功能包括在内。

创建一个`app.py`文件，并输入以下代码:

```
from flask import Flask

app = Flask( __name__ )

@app.route('/')
def index():
    return 'hello, world!' 
```

Enter fullscreen mode Exit fullscreen mode

打开终端并运行以下代码来启动您的新 Flask 应用程序。

```
FLASK_APP=app.py flask run 
```

Enter fullscreen mode Exit fullscreen mode

一旦你的 Flask 应用程序运行，在浏览器中访问`http://localhost:5000`查看 hello world 消息！

从上面的小文件中可以看出，用 Flask 构建一个极简的应用程序真的很简单。你需要做的就是:

*   导入烧瓶库
*   创建烧瓶应用程序对象
*   定义一个函数(称为视图),当用户请求一个特定的 URL(在本例中为`/` URL)时，该函数将运行

不错吧？

## 在 Flask 中创建索引和仪表板视图

构建简单 Flask 应用程序的下一步是创建主页和仪表板页面。主页是当用户访问`/` URL 时向用户显示的内容，而仪表板页面(`/dashboard`)将在用户登录其帐户后向用户显示。

当我在构建 web 应用程序时，我喜欢先定义我的模板和视图，这样我就可以解决困难的部分(设计不是我的强项)。

首先，打开之前的`app.py`文件，修改如下:

```
from flask import Flask, render_template

app = Flask( __name__ )

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/dashboard")
def dashboard():
    return render_template("dashboard.html") 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到现在已经定义了两个视图函数:`index`(呈现主页)和`dashboard`(呈现仪表板页面)。这两个视图函数都调用了`render_template` Flask 函数，它负责向用户显示一个 HTML 页面。

现在，你显然还没有创建那些 HTML 模板，所以让我们接下来做！

Flask 中的模板是使用 [Jinja2 模板语言](http://jinja.pocoo.org/docs/2.10/)构建的。如果你熟悉 HTML，它应该看起来很自然。

让我们从创建我们需要的模板文件开始。

```
mkdir templates
touch templates/{layout.html,index.html,dashboard.html} 
```

Enter fullscreen mode Exit fullscreen mode

Flask 中的所有模板应该位于`templates`文件夹中。

接下来，打开`templates/layout.html`文件并输入以下代码。

```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.0/css/bootstrap.min.css" integrity="sha384-9gVQ4dYFwwWSjIDZnLEWnxCjeSWFphJiwGPXr1jddIhOegiu1FwO5qRGvFXOdJZ4" crossorigin="anonymous">
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
    Simple Flask App | {% block title %}{% endblock %}
  </head>
  <body>
    <div class="d-flex flex-column flex-md-row align-items-center p-3 px-md-4 mb-3 bg-white border-bottom box-shadow">
      <h5 class="my-0 mr-md-auto font-weight-normal">Simple Flask App</h5>
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

这个`layout.html`文件是我们的*基础*模板。它基本上是一个构建块，所有其他模板都将继承它。通过在这个文件中定义我们的公共的，*共享的* HTML，我们可以避免在其他地方编写多余的 HTML。耶！

现在，这个文件包含了您可能会想到的所有基本内容:

*   一个简单的基于[引导](https://getbootstrap.com/)的布局
*   一个导航条(包含一些特殊的 Jinja2 逻辑)
*   特殊的 Jinja2 车身
*   页脚

让我们来看看模板中一个有趣的部分。

```
{% if not g.user %}
  <a class="p-2 text-dark" href="/login">Log In / Register</a>
{% else %}
  <a class="p-2 text-dark" href="/dashboard">Dashboard</a>
  <a class="p-2 text-dark" href="/logout">Logout</a>
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

Jinja2 标签中的任何内容(即`{% … %}`内容)在显示给用户之前都会被 Flask 编译。在上面的例子中，我们基本上是告诉 Flask，如果对象`g.user`存在，我们应该在导航栏中呈现一个仪表板和注销链接，但是如果没有`g.user`对象存在，我们应该显示一个登录按钮。

我们这样做是因为最终我们将通过`g.user`对象访问用户的帐户，并且我们希望导航条在用户看到的内容方面是智能的。

模板的下一个有趣部分是 body 标签:

```
<div class="container">
  {% block body %}{% endblock %}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

Jinja2 标签允许我们将子模板的内容注入到父模板中。这让我们无需编写多余的代码就能构建复杂的 HTML 页面。

现在布局已经定义好了，让我们创建主页。打开`templates/index.html`文件并插入以下代码。

```
{% extends "layout.html" %}

{% block title %}Home{% endblock %}

{% block body %}
  <h1 class="text-center">Simple Flask App</h1>
  <div class="row">
    <div class="col-sm-6 offset-sm-3">
      <div class="jumbotron">
        Welcome to this simple Flask example app. It shows you how to easily
        enable users to register, login, and logout of a Flask web app using <a
           href="https://developer.okta.com">Okta</a>.
      </div>
    </div>
  </div>
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

`{% extends "layout.html" %}`标签告诉模板引擎这个模板依赖于`layout.html`模板才能工作。

然后，`block`标签中的所有内容都被注入到之前的父模板中。通过将这两者结合在一起，你现在就可以拥有一个完整的主页了！

接下来，将下面的代码放到`templates/dashboard.html`文件中。

```
{% extends "layout.html" %}

{% block title %}Dashboard{% endblock %}

{% block body %}
  <h1 class="text-center">Dashboard</h1>
  <div class="row">
    <div class="col-sm-8 offset-sm-2">
      <p>Welcome to the dashboard, {{ g.user.profile.firstName }}!</p>
      <p>Your user information has been pulled from the <code>g.user</code>
      object, which makes accessing your user information simple. Your first
      name, for example, is available via the <code>g.user.profile.firstName</code>
      property. Your user id (<code>{{ g.user.id }}</code>), is pulled from the <code>g.user.id</code> property!</p>
    </div>
  </div>
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

这个模板以同样的方式工作，除了它还输出一些变量。例如，`{{ g.user.id }}`值会将 ID 值直接输出到 HTML 模板中。一旦我们连接上 OpenID 连接库，这些变量最终将是可用的。

在测试之前，你需要做的最后一件事是添加一些 CSS 来使事情看起来更好。

```
mkdir static
touch static/style.css 
```

Enter fullscreen mode Exit fullscreen mode

打开`static/style.css`复制到下面的 CSS 中。

```
h1 {
  margin: 1em 0;
}

footer {
  padding-top: 2em;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，现在你的模板已经创建好了，去测试它们吧！

访问`http://localhost:5000`，你会看到你漂亮的新网站。

[![app homepage](../Images/d1b3467b037488c5ce55321f26c98478.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--65Vz0Ezj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/app-homepage-22521e4e865fb034c4c49a14143bb699c5f4c467f8d08fcd077d16e4464c8942.png)

## 添加用户注册并登录到您的 Flask 应用程序

既然应用程序的 UI 已经完成，让我们开始有趣的工作:用户注册和登录。

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

*   在您的仪表板页面上用组织 URL 替换`{{ OKTA_ORG_URL }}`
*   在您的应用程序页面上用客户 ID 替换`{{ OKTA_CLIENT_ID }}}`
*   在您的应用程序页面上用客户机密替换`{{ OKTA_CLIENT_SECRET }}`

这个文件将被我们马上要配置的 Flask-OIDC 库使用。这些设置实际上告诉 OpenID Connect 库您正在使用哪个 OpenID Connect 应用程序进行身份验证，以及您的授权服务器 API 端点是什么。

上面的 URIs 简单地指向您新创建的 Okta 资源，以便 Flask 库能够正确地与它对话。

### 第二步:配置烧瓶-OIDC

打开`app.py`并粘贴以下代码。

```
from flask import Flask, render_template
from flask_oidc import OpenIDConnect

app = Flask( __name__ )
app.config["OIDC_CLIENT_SECRETS"] = "client_secrets.json"
app.config["OIDC_COOKIE_SECURE"] = False
app.config["OIDC_CALLBACK_ROUTE"] = "/oidc/callback"
app.config["OIDC_SCOPES"] = ["openid", "email", "profile"]
app.config["SECRET_KEY"] = "{{ LONG_RANDOM_STRING }}"
oidc = OpenIDConnect(app)

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/dashboard")
def dashboard():
    return render_template("dashboard.html") 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做的是配置弗拉斯克-OIDC 库。

*   `OIDC_CLIENT_SECRETS`设置告诉 Flask-OIDC 你的 OpenID 连接配置文件(你在上一节创建的那个)在哪里。
*   `OIDC_COOKIE_SECURE`设置允许您在不使用 SSL 的情况下测试开发中的用户登录和注册。如果你打算公开运行你的站点，你应该去掉这个选项，在你的站点上使用 SSL。
*   设置告诉弗拉斯克-OIDC 在你的网站上什么 URL 将处理用户登录。这是 OpenID 连接流程的标准部分。这超出了本文的范围，但是如果你想了解更多，去阅读我们的 [OpenID Connect primer](https://developer.okta.com/blog/2017/07/25/oidc-primer-part-1) 。
*   `OIDC_SCOPES`设置告诉弗拉斯克-OIDC 公司当用户登录时需要什么数据。在本例中，我们请求基本的用户信息(电子邮件、姓名等。).
*   `SECRET_KEY`设置应设置为一个*长的*，随机字符串。这是用来保护你的 Flask session(cookies)的，这样就没人能篡改它们了。确保这个变量是私有的。绝不应该公开曝光。

最后，在所有配置完成后，我们通过创建`oidc`对象来初始化 Flask-OIDC 扩展。

### 第三步:将用户注入到每个请求中

打开`app.py`并粘贴以下代码。

```
from flask import Flask, render_template, g
from flask_oidc import OpenIDConnect
from okta import UsersClient

app = Flask( __name__ )
app.config["OIDC_CLIENT_SECRETS"] = "client_secrets.json"
app.config["OIDC_COOKIE_SECURE"] = False
app.config["OIDC_CALLBACK_ROUTE"] = "/oidc/callback"
app.config["OIDC_SCOPES"] = ["openid", "email", "profile"]
app.config["SECRET_KEY"] = "{{ LONG_RANDOM_STRING }}"
app.config["OIDC_ID_TOKEN_COOKIE_NAME"] = "oidc_token"
oidc = OpenIDConnect(app)
okta_client = UsersClient("{{ OKTA_ORG_URL }}", "{{ OKTA_AUTH_TOKEN }}")

@app.before_request
def before_request():
    if oidc.user_loggedin:
        g.user = okta_client.get_user(oidc.user_getfield("sub"))
    else:
        g.user = None

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/dashboard")
def dashboard():
    return render_template("dashboard.html") 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做的是导入`okta` Python 库，并使用它来定义`okta_client`对象。此客户端对象将用于检索健壮的用户对象，您可以使用该对象来:

*   识别当前登录的用户
*   对用户帐户进行更改
*   存储和检索用户信息

确保用你在本教程第一部分写下的值替换`{{ OKTA_ORG_URL }}`和`{{ OKTA_AUTH_TOKEN }}`。这两个变量是强制的，所以`okta`库可以与 Okta API 服务通信。

```
@app.before_request
def before_request():
    if oidc.user_loggedin:
        g.user = okta_client.get_user(oidc.user_getfield("sub"))
    else:
        g.user = None 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码是神奇的地方。在正常的视图代码运行之前，每当用户请求查看站点上的页面时，都会执行该函数。这个函数的作用是:

*   检查用户是否通过 OpenID Connect 登录(`oidc.user_loggedin`值由弗拉斯克-OIDC 库提供)
*   如果用户登录，它将从用户的会话中获取用户的惟一用户 ID，然后使用该 ID 从 Okta API 中获取用户对象

在所有情况下，都会有一个新创建的值:`g.user`。在 Flask 中，可以在`g`对象上存储请求数据，可以从任何地方访问:视图代码、模板等。这使得它可以方便地存储像用户对象这样的东西，以便以后可以很容易地使用。

### 第四步:启用用户注册、登录和注销

打开`app.py`并插入以下代码。

```
from flask import Flask, render_template, g, redirect, url_for
from flask_oidc import OpenIDConnect
from okta import UsersClient

app = Flask( __name__ )
app.config["OIDC_CLIENT_SECRETS"] = "client_secrets.json"
app.config["OIDC_COOKIE_SECURE"] = False
app.config["OIDC_CALLBACK_ROUTE"] = "/oidc/callback"
app.config["OIDC_SCOPES"] = ["openid", "email", "profile"]
app.config["SECRET_KEY"] = "{{ LONG_RANDOM_STRING }}"
app.config["OIDC_ID_TOKEN_COOKIE_NAME"] = "oidc_token"
oidc = OpenIDConnect(app)
okta_client = UsersClient("{{ OKTA_ORG_URL }}", "{{ OKTA_AUTH_TOKEN }}")

@app.before_request
def before_request():
    if oidc.user_loggedin:
        g.user = okta_client.get_user(oidc.user_getfield("sub"))
    else:
        g.user = None

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/dashboard")
@oidc.require_login
def dashboard():
    return render_template("dashboard.html")

@app.route("/login")
@oidc.require_login
def login():
    return redirect(url_for(".dashboard"))

@app.route("/logout")
def logout():
    oidc.logout()
    return redirect(url_for(".index")) 
```

Enter fullscreen mode Exit fullscreen mode

这里只有一些不同之处:

*   您现在有了一个`login`视图。视图会将用户重定向到 Okta(OpenID 连接提供者)进行注册或登录。这是由 Flask-OIDC 库提供的`@oidc.require_login`装饰器驱动的。用户登录后，他们将被重定向到仪表板页面。
*   `logout`视图也存在。这只是使用`oidc.logout()`方法将用户注销，然后将用户重定向到主页。

至此，您的应用程序现在功能齐全了！

## 测试你的新 Flask 应用程序

现在你的应用已经完全构建好了，去测试一下吧！打开`http://localhost:5000`，创建账户，登录等。

[![app usage](../Images/28fe245c27b69266951b8fe64e75b166.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iQLY6L1d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets/blog/flask-tutorial-simple-user-registration-and-login/app-usage-6f982cf53a06b2aa89c0981a861631ab066e0e474f9342b6161e8b06be4bd7d0.gif)

如你所见，构建一个 Flask 应用程序，包括用户注册、登录等。不一定很难！

如果你有兴趣了解更多关于 web 认证和安全的知识，你可能还想看看我们的其他文章，或者在 Twitter 上关注我们，我们写了很多有趣的 web 开发主题。

这里有两个我最喜欢的:

*   [在 10 分钟内为任何网页添加认证](https://developer.okta.com/blog/2018/06/08/add-authentication-to-any-web-page-in-10-minutes)
*   如果你的 JWT 被偷了会怎么样？