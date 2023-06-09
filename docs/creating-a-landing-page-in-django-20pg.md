# 在 Django 中创建登录页面

> 原文：<https://dev.to/hmlon/creating-a-landing-page-in-django-20pg>

在本教程中，我们将在 [Django](https://www.djangoproject.com/) 中创建一个音乐发布通知应用程序的登录页面。首先，我们需要创建一个 Django 项目。

## 创建 Django 项目

我假设你已经安装了 Django，但是如果你没有，你可以使用官方安装指南。

要创建一个项目，在您的终端中运行以下命令

```
django-admin startproject mun 
```

项目创建后，通过`cd`命令
进入其目录

```
cd /mun 
```

而启动服务器就像
一样简单

```
python manage.py runserver 
```

访问完`127.0.0.1:8000`后，你应该会看到一个屏幕，显示一切都很好...

[![Django default landing page](img/0471f9ff983929c486ecc0a4a9272173.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W2i30EIh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kholinlabs.com/assets/2018-11-10-creating-a-landing-page-in-django/django_new_app.png)

除了它不是你的登陆页面...

## 我们从哪里开始？

要添加一个登陆页面，我们需要创建一个“app ”,这是一个 Django 的概念，用于与你的应用不相关的东西，并且可以从中提取(例如，也可以在其他应用中使用的身份验证)。

但是让我们不要太深入，只创建一个简单的应用程序，名为“页面”，它将只包含静态页面，如“登陆”，“联系我们”和“关于”。

```
python manage.py startapp pages 
```

当应用程序准备好时，我们需要将它添加到 Django 的`INSTALLED_APPS`中。我们将把我们的应用程序附加到其他应用程序的末尾，因为我们将需要其中的一些应用程序。

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'pages',
] 
```

新创建的`pages`应用程序有一个新的`pages`文件夹，其中有几个 python 文件。我们特别感兴趣的是`pages/urls.py`和`pages/views.py`。

## URLs

当请求到达服务器时，Django 必须找到一个“视图”，一个处理请求并发送响应的函数。Django 找到如何连接请求附带的 URL 和视图的方法是通过位于项目主目录中的`urlpatterns`。我的情况是`mun/urls.py`。

为了在`127.0.0.1:8000`上显示我们的登陆页面，本质上是在我们的主域上，我们需要将以下代码添加到`pages/urls.py`

```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
] 
```

路径是 URL 中域后面的内容。例如，在 URL `https://www.google.com/search?q=mun`中，路径应该是`/search?q=mun`。因为我们希望我们的登陆页面在`/`路径上，我们提供给`path`方法的第一个参数是`''`。第二个参数是我们需要呈现什么视图，在我们的例子中是一个`index`视图。

我们还需要将应用程序中的`urlpatterns`添加到主项目的`urlpatterns`中，并内置一个函数`include`

```
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('', include('pages.urls')),
    path('admin/', admin.site.urls),
] 
```

## 查看

如果您还记得，`index`视图还没有创建，那么让我们在`pages/views.py`
中创建它

```
from django.shortcuts import render

def index(request):
    return render(request, 'pages/index.html') 
```

我们的`index`动作只是简单地呈现了一个`'pages/index.html'`模板，这个模板还是没有被创建。所以让我们来创造它。

## 模板

我希望登录页面包含项目的名称，简要说明和功能列表。

首先，我们从页眉开始。我们的登录按钮现在不会做任何事情，所以它们的`href`现在是`#`(这意味着它不会做任何事情)。我们将在下一个故事中解决这个问题。

```
<header class="header">
  <div class="header-text">Sign in via:</div>
  <a href="#" class="header-link">Spotify</a>
  <a href="#" class="header-link">Deezer</a>
</header> 
```

其次，让我们添加品牌名称和描述

```
<section class="landing-section landing-main">
  <h1 class="brand">MuN</h1>
  <h2>
    <span class="brand-text">Mu</span>sic
    <span class="brand-text">N</span>otifications
  </h2>
  <p class="landing-main-text">
    Got tired of missing out on releases from your favourite artists?
    <br>
    You have found a perfect solution...
  </p>
</section> 
```

三、功能列表:

```
<section class="landing-section langing-features">
  <section class="landing-feature feature-music-services">
    <h2>Connect your favourite <span class="brand-text">mu</span>sic services</h2>
    <ul>
      <li>Spotify</li>
      <li>Deezer</li>
    </ul>
  </section>
  <section class="landing-feature feature-notifications">
    <h2>Get <span class="brand-text">n</span>otifications wherever you want them</h2>
    <ul>
      <li>Email</li>
      <li>Telegram</li>
    </ul>
  </section>
</section> 
```

然而，如果我们现在检查浏览器，我们会看到一个不那么令人愉快的画面:

[![A landing page without css](img/f782d8a522130de7ee6d2d9c2f038184.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pkGZoJd6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kholinlabs.com/assets/2018-11-10-creating-a-landing-page-in-django/landing-with-no-html.png)

## CSS 是一个静态文件

为了给模板添加 CSS，Django 有一个名为 [`django.contrib.staticfiles`](https://docs.djangoproject.com/en/2.1/ref/contrib/staticfiles/) 的完整应用。默认情况下，Django 会在所有应用程序的`static`文件夹中查找静态文件。为了给模板添加样式，我们需要告诉 Django 我们将使用静态文件，并添加一个样式表标签，如下所示:

```
{% load static %}
<link rel="stylesheet" type="text/css" href="{% static 'pages/style.css' %}"> 
```

我们还需要下面一行，这样我们的页面在移动设备上看起来就很好

```
<meta name="viewport" content="width=device-width, initial-scale=1"> 
```

同时，让我们为我们的页面添加一个标题

```
MuN: Music Notifications 
```

下面是粘在一起的完整代码:

```
{% load static %}
MuN: Music Notifications
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="stylesheet" type="text/css" href="{% static 'pages/style.css' %}">

<header class="header">
  <div class="header-text">Sign in via:</div>
  <a href="#" class="header-link">Spotify</a>
  <a href="#" class="header-link">Deezer</a>
</header>
<section class="landing-section landing-main">
  <div class="landing-section-content">
    <h1 class="brand">MuN</h1>
    <h2>
      <span class="brand-text">Mu</span>sic
      <span class="brand-text">N</span>otifications
    </h2>
    <p class="landing-main-text">
      Got tired of missing out on releases from your favourite artists?
      <br>
      You have found a perfect solution...
    </p>
  </div>
</section>
<section class="landing-section langing-features">
  <section class="landing-feature feature-music-services">
    <h2>Connect your favourite <span class="brand-text">mu</span>sic services</h2>
    <ul>
      <li>Spotify</li>
      <li>Deezer</li>
    </ul>
  </section>
  <section class="landing-feature feature-notifications">
    <h2>Get <span class="brand-text">n</span>otifications wherever you want them</h2>
    <ul>
      <li>Email</li>
      <li>Telegram</li>
    </ul>
  </section>
</section> 
```

## 增加一些样式

[![Some style](img/ac21b96d639ff99bd6598f11550243a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6ipNuYA---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1523268755815-fe7c372a0349%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3D78f4a9da162471fff0acce3b5be33393%26auto%3Dformat%26fit%3Dcrop%26w%3D2250%26q%3D80)

首先，我希望有一个全屏的背景图片，中间是项目的名称和描述。

我们可以用 flexbox 在中间做文本。首先，我们需要使我们的`landing-section`容器的宽度和高度达到全屏。CSS 对此有一个特殊的单位:视口高度(或`vh`)和视口宽度(或`vw`)。如果我们将它们都设置为`100`，这将占据整个屏幕(或视口)。

然而，如果我们有太多的内容，而屏幕太小(例如手机)，那么我们需要显示比`100vh`更多的内容。为此，我们可以使用`min-height`而不是`height`。

```
.landing-section {
  min-height: 100vh;
  width: 100vw;
} 
```

现在我们的容器占据了整个屏幕，我们可以用 flexbox 把内容放在中间。首先我们需要设置`display: flex`来使用 flexbox。然后我们可以使用`align-items: center`和`justify-content: center`将内容定位在中间。

[![Display flex without flex-direction](img/e2008d2d8b19e2ad04a1168a48577f69.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oJ5GHMWl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kholinlabs.com/assets/2018-11-10-creating-a-landing-page-in-django/css-1.png)

只剩下一个问题，那就是内容的方向。对它的修复相当简单——添加`flex-direction: column`。

[![Landing page with flexbox](img/523d4b8b7b847fbc4ca2b1627f83cb22.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W6wcR6Zg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kholinlabs.com/assets/2018-11-10-creating-a-landing-page-in-django/css-2.png)

想了解更多 flexbox，我推荐阅读本指南[。](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

这是我们最后的`landing-section`类的样子

```
.landing-section {
  min-height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
} 
```

## 背景中的图像

我已经使用了 [Unsplash](https://unsplash.com/) 来寻找符合我偏好的图像。

[![The image I found](img/956e5c422820408eef9095f8a959da4c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MjRrHjGZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1460667262436-cf19894f4774%3Fixlib%3Drb-0.3.5%26s%3D54bdd105057cf056b35be4150ce317eb%26auto%3Dformat%26fit%3Dcrop%26w%3D2250%26q%3D80)

在 [CSS-Tricks](https://css-tricks.com/perfect-full-page-background-image/) 的一点帮助下，我将图片设置为背景。

```
.landing-main { 
  background: url(https://images.unsplash.com/photo-1460667262436-cf19894f4774) no-repeat center center fixed; 
  background-size: cover;
} 
```

然而，图像有浅色和深色，我不能用黑色或白色覆盖它。所以我决定添加一点覆盖，并使文本白色(或者说浅灰色，因为白色太强烈)。

```
.landing-section-content {
  padding: 2rem;
  background: rgba(0,0,0,0.5);
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  color: #ccc;
}

.brand {
  margin: 0;
  color: #c55e00;
}

.brand-text {
  color: #c55e00;
} 
```

和标题的一点样式...

```
.header {
  width: 100vw;
  display: flex;
  justify-content: flex-end;
  top: 0;
  left: 0;
  position: absolute;

  background: rgba(0,0,0,0.5);
}

.header-link {
  margin: 1rem;
  border-bottom: 3px solid #c55e00;
  font-size: 1.2rem;
  color: #222;
  text-decoration: none;
  color: #ccc;
}

.header-text {
  margin: 1rem;
  font-size: 1.2rem;
  color: #ccc;
} 
```

这是我得到的:

[![Final version of the landing page](img/b670890d6abb33065679609779f68725.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vFVPZHhd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kholinlabs.com/assets/2018-11-10-creating-a-landing-page-in-django/css-3.png)

如果你对功能部分有什么想法，请写在评论里，因为它现在看起来很糟糕。

## 还有一件事

到目前为止，我们已经完成了大部分工作，但是还有一件。是图标。为此我喜欢 [LogoMark](https://logomakr.com) 网站。它使用起来非常简单，有很多漂亮的图标和东西。

[![Making an icon in the LogoMark](img/2ca05dc84a1a9625f96b6a87a5aff2b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SgNsMYjs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kholinlabs.com/assets/2018-11-10-creating-a-landing-page-in-django/logo.png)

当你下载图标时，它被保存在`.png`扩展名中，但是`.ico`更适合作为图标。 [ICOConverter](http://icoconvert.com/) 是一个很棒的 it 网站。

下载完`.icon`扩展名中的图标后，只需将其复制到项目的`pages/static`文件夹中，并在`index.html`模板中添加一行:

```
<link rel="shortcut icon" href="{% static 'favicon.ico' %}"/> 
```

## 结论

在我报道的这个故事中:

*   如何创建 Django 项目
*   Django 的应用程序概念
*   Django 如何使用`urlpatterns`找到你的观点
*   如何为你的登陆页面编写 HTML
*   如何让你的登陆页面看起来更好
*   蛋糕上的樱桃——你的偶像

这是关于模拟世界系列文章的第一部分。请继续关注第二部分。你可以在我的 [GitHub 页面](https://github.com/hmlON)找到[这个项目](https://github.com/hmlON/mun)的代码，以及我的其他项目。如果你喜欢这篇文章，你可以关注我，如果你不喜欢，你可以在下面留下愤怒的评论。