# 通过 BootStrap 导航条绘制精细的航道图

> 原文：<https://dev.to/emma_odia/fine-course-charting-through-the-bootstrap-nav-menu-4l45>

好吧！我甚至不想站在前面:我喜欢使用 BOOTSTRAP！

我认为这与我 4 年前开始使用 Ruby on Rails 时，Bootstrap 是设计前端的推荐资源有很大关系。天哪，它真的很好地集成到 Rails 中了！！？！！你打赌！

在 dev.to 上，我将尽可能多地分享一些容易让你因使用 Bootstrap 而恼火的小问题和相应的代码片段。如果这是你感兴趣的事情，我建议你跟着一起去。简单介绍一下。什么是自举？

“利用世界上最流行的前端组件库，在 web 上构建响应迅速、移动优先的项目。

Bootstrap 是一个开源工具包，用于开发 HTML、CSS 和 JS。使用我们的 Sass 变量和 mixins、响应式网格系统、广泛的预构建组件和基于 jQuery 的强大插件，快速构建您的想法原型或构建您的整个应用程序。"

*   来源:[官方自举网站。](https://getbootstrap.com/)

嗯，我不知道声称是世界上最流行的前端组件库。尽管如此，它经常被使用吗？的确如此。一定要这样。我假设有两种读者，一种需要一个简化的分解，另一种只想看 Github 要点。如果你是后者，请跳过下一段。

*“尽可能简单，但不要再简单了”*

*   [人名]阿尔伯特·爱因斯坦(犹太裔理论物理学家)

基本上，我把 Bootstrap 定义为当你不想/喜欢/渴望为你的 web 应用程序编写 CSS 样式时的资源。您只需利用默认的已创建的可用引导选项。很多时候，你所要做的只是改变前面出现的文本，我将向你展示如何为你的 web 应用程序创建一个简单的导航栏。

首先，我们去看看 Bootstrap 文档，在那里我们有 3 个选项，我们必须从中选择一个来让 Bootstrap 在我们的应用程序中工作。我更喜欢 CDN，这也是我在这篇文章和以后所有关于 Bootstrap 的文章中所依赖的。使用 CDN 有以下[个优点](https://www.globaldots.com/9-benefits-using-cdn/)

现在，让我们开始构建导航条。因此，让我们从默认的引导启动模板代码开始:

```
<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.2/css/bootstrap.min.css" integrity="sha384-Smlep5jCw/wG7hdkwQ/Z5nLIefveQRIY9nfy6xoR1uRYBtpZgI6339F5dgvm/e9B" crossorigin="anonymous">

    BootStrap Nav!
  </head>
  <body>

    <!-- Plug Bootstrap Nav Bar code here -->

    <h1>Hello, world!</h1>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.2/js/bootstrap.min.js" integrity="sha384-o+RDsa0aLu++PJvFqy8fFScvbHFLtbvScb8AjopnFD+iEQ7wo/CG0xlczd+2O/em" crossorigin="anonymous"></script>
  </body>
</html> 
```

通常情况下，一个网络应用程序有一部分导航功能:

主页|关于|联系人|常见问题|博客

在本系列的下一篇文章中，我们将只讨论这 5 个项目。让我们去 Bootstrap 文档中复制创建导航条的代码。

```
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Navbar</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>

  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Link</a>
      </li>
      <li class="nav-item dropdown">
        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
          Dropdown
        </a>
        <div class="dropdown-menu" aria-labelledby="navbarDropdown">
          <a class="dropdown-item" href="#">Action</a>
          <a class="dropdown-item" href="#">Another action</a>
          <div class="dropdown-divider"></div>
          <a class="dropdown-item" href="#">Something else here</a>
        </div>
      </li>
      <li class="nav-item">
        <a class="nav-link disabled" href="#">Disabled</a>
      </li>
    </ul>
    <form class="form-inline my-2 my-lg-0">
      <input class="form-control mr-sm-2" type="search" placeholder="Search" aria-label="Search">
      <button class="btn btn-outline-success my-2 my-sm-0" type="submit">Search</button>
    </form>
  </div>
</nav> 
```

这是 Bootstrap 中 Navbar 的默认导航视图，我们所要做的就是将它插入 html starter 模板，我在那里留下了注释。

然后我们有: