# 教程:用 Node.js 构建一个基本的 CRUD 应用

> 原文：<https://dev.to/oktadev/tutorial-build-a-basic-crud-app-with-nodejs-1ohn>

Node.js 正在吞噬世界。许多最大的公司正在用 Node.js 构建越来越多的网站和 API 服务，而且没有放缓的迹象。我从 2012 年开始使用 Node.js，看到社区和工具的成长和发展，我感到非常兴奋——现在是开始 Node.js 开发的最佳时机。

本教程将带您一步一步地构建一个功能齐全的 Node.js 网站。在这个过程中，您将了解最流行的 web 框架 Express.js，使用 [OpenID Connect](https://developer.okta.com/blog/2017/07/25/oidc-primer-part-1) 进行用户身份验证，锁定路由以实施登录限制，以及对数据库执行 CRUD 操作(创建、读取、更新和删除数据)。本教程使用以下技术，但不需要任何先前的经验:

*   [Node.js](https://nodejs.org/en/)
*   [Express.js](https://expressjs.com/) 和[帕格](https://pugjs.org/api/getting-started.html)
*   Okta 的[OIDC-中间件](https://github.com/okta/okta-oidc-js/tree/master/packages/oidc-middleware)和[节点 SDK](https://github.com/okta/okta-sdk-nodejs)
*   Sequelize.js ，一个在 Node.js 中处理数据库的流行 ORM

如果你想跳过教程，只查看完整构建的项目，你可以在 GitHub 上查看。

## 关于 Express.js

Express.js 是 Node.js 生态系统中最受欢迎的 web 框架。它非常简单和简约。此外，有成千上万的开发人员库使用 Express，使得使用它进行开发变得有趣和灵活。

[![express website screenshot](../Images/449e2a5db960cf854fb117827cd1cb2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EUVxbapB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/tutorial-build-a-basic-crud-app-with-node/express-website-screenshot-6e39ae77cc4841f1356326199315a4c10903be4bd18a3dfecd823c27b17d59d3.png)

不管你是想建立一个网站还是一个 API，Express.js 都提供了大量的特性和良好的开发者体验。

通过本教程，你将建立一个简单的博客。您创建的博客将有一个列出最近帖子的主页、一个用户可以验证的登录页面、一个用户可以创建和编辑帖子的仪表板页面以及注销功能。

博客将使用 Express.js 构建，用户界面将使用 Pug 构建，认证组件将由 [Okta](https://developer.okta.com/) 处理，博客文章存储和数据库管理将由 Sequelize.js 处理。

## 创建您的 Express.js 应用程序

在开始之前，请确保您安装了 Node.js 的最新版本。如果您还没有安装 Node.js，请[访问此页面](https://nodejs.org/en/download/package-manager/)并在继续之前为您的操作系统安装它。

为了让你的项目快速启动，你可以利用 [express-generator](https://github.com/expressjs/generator) 。这是一个官方维护的程序，允许你用最少的努力轻松地搭建一个 Express.js 网站。

安装`express-generator`运行:

```
npm install -g express-generator 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要初始化您的项目。为此，使用新安装的 express-generator 程序来引导您的应用程序:

```
express --view pug blog
cd blog
npm install
npm start 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将初始化一个名为 **blog** 的新项目，将您移动到新项目文件夹中，安装所有项目依赖项，并启动一个 web 服务器。

运行完上面的命令后，将您最喜欢的浏览器指向`http://localhost:3000`，您应该会看到您的应用程序正在运行:

[![express generator page](../Images/11a5c87ea8ad2bf97da8fb39c5d8f26f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cR2gNx1d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/tutorial-build-a-basic-crud-app-with-node/express-generator-page-0b86cb095fdd227d7b61051795d52a4a02d757fe3b320b9f8e6a0d5a2bec9c59.png)

## 初始化认证

对于每个开发人员来说，处理 web 应用程序中的用户认证是一个巨大的痛苦。这就是 Okta 的闪光点:它帮助您以最小的努力保护您的 web 应用程序。首先，您需要在 Okta 中创建一个 OpenID Connect 应用程序。[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)(或者如果你已经有一个的话登录)。

[![Okta signup page](../Images/6b7f3a716e01c08ab4d2129103f7e6a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BYYhj4fc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/tutorial-build-a-basic-crud-app-with-node/okta-signup-94ec70377e8a1d69fa41cfcd66d9d7f5faf2044e6a361452d155a02762532443.png)

一旦您登录并进入仪表板页面，复制下下图中的 **Org URL** 。你以后会需要这个的。

[![Okta Org URL](../Images/4189ba3dea3b356820fd5eef251044d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---eXBSvqX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/tutorial-build-a-basic-crud-app-with-node/okta-org-url-93fb639009e0f0389af1db64678e6561cd01811348bef6350e487a0aa1aada1d.png)

然后通过浏览到**应用**选项卡并点击**添加应用**来创建一个新的应用。

[![Okta app dashboard](../Images/c85649e5c39b73dbf38d33ad6116297a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SG-LqVMB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/tutorial-build-a-basic-crud-app-with-node/okta-app-dashboard-054d9b9b13c1760fe0fb857a4b39ddefad7b219ce3ea72af617bddc360d0d88b.png)

接下来，单击 **Web** 平台选项(因为我们的博客项目是一个 Web 应用程序)。

[![Okta create app platform](../Images/ee182a46390241aa3bd7feea2450cbed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A5JydYy3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/tutorial-build-a-basic-crud-app-with-node/okta-create-app-platform-d3aa9a1834906c601d29fd2c7843329dd09c1b5be59f8f35b85e82ffc4cfcc46.png)

在“设置”页面上，输入以下值:

*   **名称**:博客
*   **基地 URIs** : `http://localhost:3000`
*   **登录重定向 URIs** : `http://localhost:3000/users/callback`

您可以保持所有其他值不变。

[![Okta create app settings](../Images/08425fb1c5e04172d233b1c29f28153f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e40KAqvF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/tutorial-build-a-basic-crud-app-with-node/okta-create-app-settings-4d0c56e9c1ea35eae1922ab70f8948ea6bcf3e2ad2af210a1e524ecde8738816.png)

现在您的应用程序已经创建好了，请记下下页的**客户端 ID** 和**客户端秘密**值，您很快就会用到它们。

[![Okta app secrets](../Images/7801bfab57796abe82553c324bba1101.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WwhJxLuw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/tutorial-build-a-basic-crud-app-with-node/okta-app-secrets-d4c5570e7679ca4e5af1c90f8fccc1bed0d880bed69eb4b9a9287aaf1e2398cf.png)

最后，创建一个新的身份验证令牌。这将允许你的应用程序与 Okta 对话，以检索用户信息等。为此，点击页面顶部的 **API** 标签，然后点击**创建令牌**按钮。给你的令牌起一个名字，最好和你的应用程序同名，然后点击**创建令牌**。记下这个令牌值，因为您很快就会用到它。

[![Okta create token](../Images/99e75eb9d8dc8598e589728a42b1bcb6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--at3rFQ7s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/tutorial-build-a-basic-crud-app-with-node/okta-create-token-87e20254a5485f3482d1fc6a928df9590fac7b0c7a60fbffaca216078f91626f.png)

## 安装依赖项

为了初始化 Express.js 应用程序，您需要做的第一件事是安装所有必需的依赖项。

```
npm install express@4.16.3
npm install @okta/oidc-middleware@0.1.2
npm install @okta/okta-sdk-nodejs@1.1.0
npm install sqlite3@4.0.1
npm install sequelize@4.38.0
npm install async@2.6.1
npm install slugify@1.3.0
npm install express-session@1.15.6 
```

Enter fullscreen mode Exit fullscreen mode

## 用序列定义数据库模型

当开始一个新项目时，我喜欢做的第一件事是定义我的应用程序需要存储什么数据，这样我就可以准确地模拟出我正在处理什么数据。

创建一个名为`./models.js`的新文件，并将下面的代码复制到其中。

```
const Sequelize = require("sequelize");

const db = new Sequelize({
  dialect: "sqlite",
  storage: "./database.sqlite"
});

const Post = db.define("post", {
  title: { type: Sequelize.STRING },
  body: { type: Sequelize.TEXT },
  authorId: { type: Sequelize.STRING },
  slug: { type: Sequelize.STRING }
});

db.sync();

module.exports = { Post }; 
```

Enter fullscreen mode Exit fullscreen mode

这段代码初始化了一个新的 SQLite 数据库，它将用于存储博客数据，还定义了一个名为`Post`的模型，它将博客文章存储在数据库中。每篇文章都有一个标题、一个主体、一个作者 ID 和一个 slug 字段。

*   字段将包含文章的标题，例如:“一篇好文章”
*   The `body` field will hold the body of the article as HTML, eg: “

    我的第一篇帖子！

    ”
*   `authorId`字段将存储作者的唯一 ID。这是关系数据库中的一种常见模式:只存储链接资源的标识符，以便以后可以查找作者的最新信息。
*   `slug`字段将存储文章标题的 URL 友好版本，例如:“一篇伟大的文章”

**注**:如果你以前没用过 SQLite，那就太神奇了。这是一个将你的数据存储在一个文件中的数据库。它非常适合构建不需要大量并发的应用程序，比如这个简单的博客。

一旦这段 JavaScript 代码运行，对文件底部的`db.sync();`的调用将自动创建数据库和所有必要的表。

## 初始化你的 Express.js App

在定义了数据库模型之后，我想做的下一件事是初始化我的应用程序代码。这通常包括:

*   配置应用程序设置
*   安装为应用程序提供功能的中间件
*   处理错误
*   等等。

打开`./app.js`文件，用以下代码替换其内容。

```
const createError = require("http-errors");
const express = require("express");
const logger = require("morgan");
const path = require("path");
const okta = require("@okta/okta-sdk-nodejs");
const session = require("express-session");
const ExpressOIDC = require("@okta/oidc-middleware").ExpressOIDC;

const blogRouter = require("./routes/blog");
const usersRouter = require("./routes/users");

const app = express();
const client = new okta.Client({
  orgUrl: "{yourOktaOrgUrl}",
  token: "{yourOktaToken}"
});

app.set("views", path.join(__dirname, "views"));
app.set("view engine", "pug");

// Middleware
app.use(logger("dev"));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(express.static(path.join(__dirname, "public")));

const oidc = new ExpressOIDC({
  issuer: "{yourOktaOrgUrl}/oauth2/default",
  client_id: "{yourOktaClientId}",
  client_secret: "{yourOktaClientSecret}",
  redirect_uri: "http://localhost:3000/users/callback",
  scope: "openid profile",
  routes: {
    login: {
      path: "/users/login"
    },
    callback: {
      path: "/users/callback",
      defaultRedirect: "/dashboard"
    }
  }
});

app.use(session({
  secret: "{aLongRandomString}",
  resave: true,
  saveUninitialized: false
}));

app.use(oidc.router);

app.use((req, res, next) => {
  if (!req.userinfo) {
    return next();
  }

  client.getUser(req.userinfo.sub)
    .then(user => {
      req.user = user;
      res.locals.user = user;
      next();
    });
});

// Routes
app.use("/", blogRouter);
app.use("/users", usersRouter);

// Error handlers
app.use(function(req, res, next) {
  next(createError(404));
});

app.use(function(err, req, res, next) {
  res.locals.message = err.message;
  res.locals.error = req.app.get("env") === "development" ? err : {};

  res.status(err.status || 500);
  res.render("error");
});

module.exports = app; 
```

Enter fullscreen mode Exit fullscreen mode

确保用实际的 Okta 信息替换占位符变量。

*   在您的仪表板页面上用组织 URL 替换`{yourOktaOrgUrl}`
*   在您的应用程序页面上用客户 ID 替换`{yourOktaClientId}`
*   在您的应用程序页面上用客户机密替换`{yourOktaClientSecret}`
*   用一个随机的长字符串替换`{aLongRandomString}`(只要用手指在键盘上敲击几秒钟)

让我们看看这段代码做了什么。

### 初始化 Node.js 中间件

Express.js 中的中间件是根据每个请求运行的函数。您可以安装并使用许多开源中间件来为 Express.js 应用程序添加功能。下面的代码使用了几个流行的 Express.js 中间件，并定义了一些新的中间件。

```
// Middleware
app.use(logger("dev"));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(express.static(path.join(__dirname, "public")));

const oidc = new ExpressOIDC({
  issuer: "{yourOktaOrgUrl}/oauth2/default",
  client_id: "yourOktaClientId}",
  client_secret: "{yourOktaClientSecret}",
  redirect_uri: "http://localhost:3000/users/callback",
  scope: "openid profile",
  routes: {
    login: {
      path: "/users/login"
    },
    callback: {
      path: "/users/callback",
      defaultRedirect: "/dashboard"
    }
  }
});

app.use(session({
  secret: "{aLongRandomString}",
  resave: true,
  saveUninitialized: false
}));

app.use(oidc.router);

app.use((req, res, next) => {
  if (!req.userinfo) {
    return next();
  }

  client.getUser(req.userinfo.sub)
    .then(user => {
      req.user = user;
      res.locals.user = user;

      next();
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

最初的几个中间件都是标准的东西:它们支持日志记录、解析表单数据和提供静态文件。需要注意的有趣的事情是`ExpressOIDC`中间件的使用。

这个中间件处理支持登录、注销等的应用程序的 OpenID 连接认证逻辑。传递给`ExpressOIDC`中间件的设置是配置选项，它规定了用户使用什么 URL 登录应用程序，以及用户登录后将被重定向到哪里。

下一个中间件是`session`中间件。这个中间件负责管理用户 cookies 并记住用户是谁。它需要的`secret`必须是一个你定义的私有的长随机字符串。这个秘密使得攻击者无法篡改 cookies。

`oidc.router`中间件使用您在创建`ExpressOIDC`时定义的设置来创建处理用户认证的路由。例如，每当用户访问`/users/login`，他们就会被带到一个登录页面。这一行代码使之成为可能。

最后，还有一个定制的中间件。这个中间件创建了一个`req.user`对象，稍后您将能够使用它来更容易地访问当前登录用户的个人信息。

### 初始化 Node.js 路线

路由代码告诉 Express.js 当用户访问一个特定的 URL 时运行什么代码。这是来自`./app.js`的路线代码。

```
// Routes
app.use("/", blogRouter);
app.use("/users", usersRouter); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码告诉 Express.js，在我们(尚未创建的)博客和用户路由文件中，有一些函数应该在某些 URL 被点击时执行。如果用户访问以`/users`开头的 URL，Express.js 将在用户路由文件中寻找其他匹配的 URL。如果用户访问任何以`/` URL 开头的 URL，Express.js 将查看 blog routes 文件，看看该怎么做。

### 初始化错误处理程序

上面我们应用程序的最后一点代码是错误处理中间件。

```
// Error handlers
app.use(function(req, res, next) {
  next(createError(404));
});

app.use(function(err, req, res, next) {
  res.locals.message = err.message;
  res.locals.error = req.app.get("env") === "development" ? err : {};

  res.status(err.status || 500);
  res.render("error");
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果发生任何 4XX 或 5XX 类型的错误，这些中间件将会运行。在这两种情况下，他们都会向用户呈现一个简单的 web 页面来显示错误。

## 创建 Express.js 视图

Express.js 中的视图相当于 HTML 模板——它们是存储前端代码和逻辑的地方。您将在这个项目中使用的视图将使用最流行的模板语言之一。

通过运行以下命令删除现有视图。

```
rm views/* 
```

Enter fullscreen mode Exit fullscreen mode

接下来，创建一个`./views/layout.pug`文件。这是一个基本的“布局”模板，所有其他模板都将继承它。它定义了通用的 HTML，包括[引导](https://getbootstrap.com/) CSS 库，还定义了一个简单的导航菜单。

```
block variables
  - var selected = 'Home'

doctype html
html(lang='en')
  head
    meta(charset='utf-8')
    meta(name='viewport' content='width=device-width, initial-scale=1, shrink-to-fit=no')
    link(rel='stylesheet' href='https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css' integrity='sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm' crossorigin='anonymous')
    link(rel='stylesheet', href='/stylesheets/style.css')
    title Blog: #{title}
  body
    div.d-flex.flex-column.flex-md-row.align-items-center.p-3.px-md-4.mb-3.bg-white.border-bottom.box-shadow
      h5.my-0.mr-md-auto.font-weight-normal Blog
      nav.my-2.my-md-0.mr-md-3
        a.p-2.text-dark(href="/", title="Home") Home

        if user == undefined
          a.p-2.text-dark(href="/users/login") Log In
        else
          a.p-2.text-dark(href="/dashboard") Dashboard
          a.p-2.text-dark(href="/users/logout") Logout
    .container
      block content

    hr.bottom
    footer.
      Built with #[a(href='https://expressjs.com/') Express.js], login powered by #[a(href='https://developer.okta.com/') Okta]. 
```

Enter fullscreen mode Exit fullscreen mode

接下来，创建`./views/error.pug`文件。出现错误时，将显示此页面。

```
extends layout

block content
  h1= message
  h2= error.status
  pre #{error.stack} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，创建`./views/unauthenticated.pug`文件。当用户试图访问某个页面但未登录时，将显示该页面。

```
extends layout

block variables
  - var title = "Unauthenticated"

block content
  .unauthenticated
    h2.text-center Whoops!
    p.
      You must be signed in to view this page. Please #[a(href="/users/login", title="Login") login] to view this page. 
```

Enter fullscreen mode Exit fullscreen mode

现在定义`./views/index.pug`模板。这是该网站的主页，按日期顺序列出了当前所有的博客文章。

```
extends layout

block variables
  - var title = "Home"

block content
  h2.text-center Recent Posts

  if posts == null
    p.empty.text-center Uh oh. There are no posts to view!

  .posts
    ul
      each post in posts
        .row
          .offset-sm-2.col-sm-8
            li
              a(href="/" + post.slug, title=post.title)= post.title
              span   by #{post.authorName} 
```

Enter fullscreen mode Exit fullscreen mode

下一个要定义的视图是显示一篇博客文章的`./views/post.pug`。

```
extends layout

block variables
  - var title = post.title

block content
  h2.text-center= title

  .row
    .offset-sm-2.col-sm-8
      .body !{post.body}
      p.author Written by #{post.authorName} 
```

Enter fullscreen mode Exit fullscreen mode

现在创建包含博客文章编辑页面标记的文件`./views/edit.pug`。

```
extends layout

block variables
  - var title = post.title

block content
  h2.text-center Edit Post

  .row
    .offset-sm-2.col-sm-8
      form(method="post")
        .form-group
          label(for="title") Post Title
          input.form-control#title(type="text", name="title", value=post.title, required)
        .form-group
          label(for="body") Post Body
          textarea.form-control#post(name="body", rows="6", required)= post.body
        button.btn.btn-primary.submit-btn(type="submit") Update

  .row
    .offset-sm-2.col-sm-8
      .body !{post.body}
      p.author Written by #{post.authorName} 
```

Enter fullscreen mode Exit fullscreen mode

最后，创建`./views/dashboard.pug`，它将呈现用户登录后将看到的仪表板页面。该页面允许用户创建新的帖子以及编辑和删除他们现有的帖子。

```
extends layout

block variables
  - var title = "Dashboard"

block content
  .row
    .offset-sm-2.col-sm-8
      h2 Create a Post

  if post != undefined
    .row
      .offset-sm-2.col-sm-8
        .alert.alert-success(role="alert").text-center
          p Your new post was created successfully! #[a(href="/" + post.slug) View it?]

  .row
    .offset-sm-2.col-sm-8
      form(method="post")
        .form-group
          label(for="title") Post Title
          input.form-control#title(type="text", name="title", placeholder="Title", required)
        .form-group
          label(for="body") Post Body
          textarea.form-control#post(name="body", rows="6", required)
        button.btn.btn-primary.submit-btn(type="submit") Submit

  .row
    .offset-sm-2.col-sm-8
      h2.your-posts Your Posts
      ul.edit
        each post in posts
          li
            a(href="/" + post.slug, title=post.title)= post.title
            form.hidden(method="post", action="/" + post.slug + "/delete")
              button.btn.btn-outline-danger.delete Delete
            a(href="/" + post.slug + "/edit", title=post.title)
              button.btn.btn-outline-secondary Edit 
```

Enter fullscreen mode Exit fullscreen mode

## 创建样式

我不是一个网页设计师(这就是为什么我喜欢使用 Bootstrap)，但每个项目都需要一点视觉天赋。我已经尽力创建了一些简单的 CSS 样式。

由于 CSS 很简单，而且不是本教程的重点，您可以简单地将下面的 CSS 复制到`./public/stylesheets/style.css`文件中。

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

## 创建路线

在任何 Express.js 应用程序中，路由都是真正发生动作的地方。它们决定了当用户访问一个特定的 URL 时会发生什么。

首先，删除 express-generator 应用程序创建的现有路线。

```
rm routes/* 
```

Enter fullscreen mode Exit fullscreen mode

接下来，创建您将需要的两个路由文件。

```
touch routes/{blog.js,users.js} 
```

Enter fullscreen mode Exit fullscreen mode

`./routes/blog.js`文件将包含所有与博客功能相关的路由。`./routes/users.js`文件将包含与用户功能相关的路线。虽然你可以把所有的逻辑放在主`./app.js`文件中，但是把你的路线放在不同的基于目的的文件中是个好主意。

### 创建用户路线

因为 Okta 的[oidc-中间件库](https://github.com/okta/okta-oidc-js/tree/master/packages/oidc-middleware)已经在处理应用程序的用户认证，所以我们不需要创建很多面向用户的功能。

您需要定义的唯一与用户管理相关的路由是注销路由—该路由将用户从其帐户中注销，并将他们重定向到站点的主页。虽然 oidc-middleware 库提供了注销助手，但它并没有创建实际的路由。

打开`./routes/users.js`文件，复制以下代码。

```
const express = require("express");

const router = express.Router();

// Log a user out
router.get("/logout", (req, res, next) => {
  req.logout();
  res.redirect("/");
});

module.exports = router; 
```

Enter fullscreen mode Exit fullscreen mode

理解这条路线的方法很简单。当用户访问`/logout` URL 时，将运行一个函数:

使用 oidc-middleware 库将用户从他们的帐户中注销，将现在注销的用户重定向到站点的主页

### 创建博客路线

因为您正在构建的应用程序是一个博客，所以您需要添加的最后一大块功能是实际的博客路由代码。这将决定博客实际上如何工作:如何创建帖子、编辑帖子、删除帖子等。

打开`./routes/blog.js`文件，复制以下代码。如果一下子看起来很多，不要担心——我将在下面详细介绍每条路线。

```
const async = require("async");
const express = require("express");
const okta = require("@okta/okta-sdk-nodejs");
const sequelize = require("sequelize");
const slugify = require("slugify");

const models = require("../models");

const client = new okta.Client({
  orgUrl: "{yourOktaOrgUrl}",
  token: "{yourOktaToken}"
});
const router = express.Router();

// Only let the user access the route if they are authenticated.
function ensureAuthenticated(req, res, next) {
  if (!req.user) {
    return res.status(401).render("unauthenticated");
  }

  next();
}

// Render the home page and list all blog posts
router.get("/", (req, res) => {
  models.Post.findAll({
    order: sequelize.literal("createdAt DESC")
  }).then(posts => {
    let postData = [];

    async.eachSeries(posts, (post, callback) => {
      post = post.get({ plain: true });
      client.getUser(post.authorId).then(user => {
        postData.push({
          title: post.title,
          body: post.body,
          createdAt: post.createdAt,
          authorName: user.profile.firstName + " " + user.profile.lastName,
          slug: post.slug
        });
        callback();
      }).catch(err => {
        postData.push({
          title: post.title,
          body: post.body,
          createdAt: post.createdAt,
          slug: post.slug
        });
        callback();
      });
    }, err => {
      return res.render("index", { posts: postData });
    });
  });
});

// Render the user dashboard
router.get("/dashboard", ensureAuthenticated, (req, res, next) => {
  models.Post.findAll({
    where: {
      authorId: req.user.id
    },
    order: sequelize.literal("createdAt DESC")
  }).then(posts => {
    let postData = [];

    posts.forEach(post => {
      postData.push(post.get({ plain: true }));
    });

    return res.render("dashboard", { posts: postData });
  });
});

// Create a new post
router.post("/dashboard", ensureAuthenticated, (req, res, next) => {
  models.Post.create({
    title: req.body.title,
    body: req.body.body,
    authorId: req.user.id,
    slug: slugify(req.body.title).toLowerCase()
  }).then(newPost => {
    models.Post.findAll({
      where: {
        authorId: req.user.id
      },
      order: sequelize.literal("createdAt DESC")
    }).then(posts => {
      let postData = [];

      posts.forEach(post => {
        postData.push(post.get({ plain: true }));
      });

      res.render("dashboard", { post: newPost, posts: postData });
    });
  });
});

// Render the edit post page
router.get("/:slug/edit", ensureAuthenticated, (req, res, next) => {
  models.Post.findOne({
    where: {
      slug: req.params.slug,
      authorId: req.user.id
    }
  }).then(post => {
    if (!post) {
      return res.render("error", {
        message: "Page not found.",
        error: {
          status: 404,
        }
      });
    }

    post = post.get({ plain: true });
    client.getUser(post.authorId).then(user => {
      post.authorName = user.profile.firstName + " " + user.profile.lastName;
      res.render("edit", { post });
    });
  });
});

// Update a post
router.post("/:slug/edit", ensureAuthenticated, (req, res, next) => {
  models.Post.findOne({
    where: {
      slug: req.params.slug,
      authorId: req.user.id
    }
  }).then(post => {
    if (!post) {
      return res.render("error", {
        message: "Page not found.",
        error: {
          status: 404,
        }
      });
    }

    post.update({
      title: req.body.title,
      body: req.body.body,
      slug: slugify(req.body.title).toLowerCase()
    }).then(() => {
      post = post.get({ plain: true });
      client.getUser(post.authorId).then(user => {
        post.authorName = user.profile.firstName + " " + user.profile.lastName;
        res.redirect("/" + slugify(req.body.title).toLowerCase());
      });
    });
  });
});

// Delete a post
router.post("/:slug/delete", (req, res, next) => {
  models.Post.findOne({
    where: {
      slug: req.params.slug,
      authorId: req.user.id
    }
  }).then(post => {
    if (!post) {
      return res.render("error", {
        message: "Page not found.",
        error: {
          status: 404,
        }
      });
    }

    post.destroy();
    res.redirect("/dashboard");
  });
});

// View a post
router.get("/:slug", (req, res, next) => {
  models.Post.findOne({
    where: {
      slug: req.params.slug
    }
  }).then(post => {
    if (!post) {
      return res.render("error", {
        message: "Page not found.",
        error: {
          status: 404,
        }
      });
    }

    post = post.get({ plain: true });
    client.getUser(post.authorId).then(user => {
      post.authorName = user.profile.firstName + " " + user.profile.lastName;
      res.render("post", { post });
    });
  });
});

module.exports = router; 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:确保在这个文件的顶部用你的值替换占位符变量。您需要用适当的值替换`{yourOktaOrgUrl}`和`{yourOktaToken}`。

这是大量的代码，所以让我们来看看每条路线以及它是如何工作的。

#### 创建一个认证助手

在 blog routes 中，您会注意到的第一个函数是`ensureAuthenticated`函数。

```
// Only let the user access the route if they are authenticated.
function ensureAuthenticated(req, res, next) {
  if (!req.user) {
    return res.status(401).render("unauthenticated");
  }

  next();
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数是一个特殊的中间件，您稍后将使用它来呈现您之前创建的`unauthenticated.pug`视图，告诉用户除非登录，否则他们无权查看页面。

这个中间件通过寻找`req.user`变量来工作，如果它不存在，意味着用户当前没有登录。这将有助于确保只有登录的用户才能访问站点的特定页面(例如，允许用户创建新博客文章的页面)。

#### 创建主页

索引路径(又名:“主页路径”)是用户访问站点根目录时运行的路径。它将显示所有按日期排序的博客文章，没有其他内容。这是路线代码。

```
// Render the home page and list all blog posts
router.get("/", (req, res) => {
  models.Post.findAll({
    order: sequelize.literal("createdAt DESC")
  }).then(posts => {
    let postData = [];

    async.eachSeries(posts, (post, callback) => {
      post = post.get({ plain: true });
      client.getUser(post.authorId).then(user => {
        postData.push({
          title: post.title,
          body: post.body,
          createdAt: post.createdAt,
          authorName: user.profile.firstName + " " + user.profile.lastName,
          slug: post.slug
        });
        callback();
      }).catch(err => {
        postData.push({
          title: post.title,
          body: post.body,
          createdAt: post.createdAt,
          slug: post.slug
        });
        callback();
      });
    }, err => {
      return res.render("index", { posts: postData });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

其工作方式是首先使用 [Sequelize.js](http://docs.sequelizejs.com/) 从数据库中按照`createdAt`字段的顺序检索所有博客文章的列表。每当一篇新的博客文章存储在数据库中时，Sequelize.js 会自动为其分配一个`createdAt`和`updatedAt`时间字段。

一旦从数据库返回了一个帖子列表，您将迭代每一个帖子，以 JSON 格式检索它，然后使用 [Okta 的 Node SDK](https://github.com/okta/okta-sdk-nodejs) 通过 authorId 字段检索作者的信息。

最后，您将构建一个由所有博客文章和作者姓名组成的数组，并将呈现`index.pug`模板，该模板将获取数据并显示完整的 web 页面。

#### 创建仪表板路线

仪表板页面是用户登录后看到的第一个页面。它将:

*   允许用户创建新的博客文章
*   向用户显示他们以前创建的博客文章列表
*   提供允许用户编辑或删除以前创建的博客帖子的按钮

下面是支持仪表板路线的代码。

```
// Render the user dashboard
router.get("/dashboard", ensureAuthenticated, (req, res, next) => {
  models.Post.findAll({
    where: {
      authorId: req.user.id
    },
    order: sequelize.literal("createdAt DESC")
  }).then(posts => {
    let postData = [];

    posts.forEach(post => {
      postData.push(post.get({ plain: true }));
    });

    return res.render("dashboard", { posts: postData });
  });
});

// Create a new post
router.post("/dashboard", ensureAuthenticated, (req, res, next) => {
  models.Post.create({
    title: req.body.title,
    body: req.body.body,
    authorId: req.user.id,
    slug: slugify(req.body.title).toLowerCase()
  }).then(newPost => {
    models.Post.findAll({
      where: {
        authorId: req.user.id
      },
      order: sequelize.literal("createdAt DESC")
    }).then(posts => {
      let postData = [];

      posts.forEach(post => {
        postData.push(post.get({ plain: true }));
      });

      res.render("dashboard", { post: newPost, posts: postData });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这里有两条技术路线。第一个路由函数在用户发出对`/dashboard`页面的 GET 请求时运行，而第二个路由函数在用户发出对`/dashboard`页面的`POST`请求时运行。

第一条路线检索该用户创建的所有博客文章的列表，然后呈现仪表板页面。注意它是如何使用我们之前创建的`ensureAuthenticated`中间件的。通过将`ensureAuthenticated`中间件插入到路由中，这保证了只有当前登录的用户正在访问该页面时，路由代码才会执行。

如果用户选择创建一个新的博客帖子，这将触发对`/dashboard` URL 的 post 请求，这将最终运行上面显示的第二个仪表板路由。

这条路径使用 Sequelize.js 创建一个新的数据库条目，存储博客文章和作者的详细信息，然后再次呈现仪表板页面。

#### 创建编辑路线

编辑路线控制允许用户编辑其现有博客帖子的页面。实现这一点的代码如下所示。

```
// Render the edit post page
router.get("/:slug/edit", ensureAuthenticated, (req, res, next) => {
  models.Post.findOne({
    where: {
      slug: req.params.slug,
      authorId: req.user.id
    }
  }).then(post => {
    if (!post) {
      return res.render("error", {
        message: "Page not found.",
        error: {
          status: 404,
        }
      });
    }

    post = post.get({ plain: true });
    client.getUser(post.authorId).then(user => {
      post.authorName = user.profile.firstName + " " + user.profile.lastName;
      res.render("edit", { post });
    });
  });
});

// Update a post
router.post("/:slug/edit", ensureAuthenticated, (req, res, next) => {
  models.Post.findOne({
    where: {
      slug: req.params.slug,
      authorId: req.user.id
    }
  }).then(post => {
    if (!post) {
      return res.render("error", {
        message: "Page not found.",
        error: {
          status: 404,
        }
      });
    }

    post.update({
      title: req.body.title,
      body: req.body.body,
      slug: slugify(req.body.title).toLowerCase()
    }).then(() => {
      post = post.get({ plain: true });
      client.getUser(post.authorId).then(user => {
        post.authorName = user.profile.firstName + " " + user.profile.lastName;
        res.redirect("/" + slugify(req.body.title).toLowerCase());
      });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这些路由通过匹配一个可变的模式 URL。如果用户访问一个类似于`/<something>/edit`的 URL，那么编辑路径将会运行。因为路由中的 URL 模式被定义为`/:slug/edit`，Express.js 将在`req.params.slug`变量中沿着 URL 路由传递，所以您可以使用它。

这些路由处理编辑页面的呈现，并在需要时更新现有的帖子。

#### 创建删除路线

删除路径很简单:如果用户向 URL `/<post-url>/delete`发送 post 请求，那么 Sequelize.js 将从数据库中删除 POST。

下面是实现这一点的代码。

```
// Delete a post
router.post("/:slug/delete", (req, res, next) => {
  models.Post.findOne({
    where: {
      slug: req.params.slug,
      authorId: req.user.id
    }
  }).then(post => {
    if (!post) {
      return res.render("error", {
        message: "Page not found.",
        error: {
          status: 404,
        }
      });
    }

    post.destroy();
    res.redirect("/dashboard");
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建显示路线

显示路径是其中最简单的:它在页面上呈现特定的博客文章。通过使用可变的 URL 模式，它的工作方式很像上面的其他路由。

当用户访问类似于`/my-great-article`的 URL 时，这个路径将运行，查询数据库中任何 slug 为`my-great-article`的博客文章，然后在页面上显示该文章。

```
// View a post
router.get("/:slug", (req, res, next) => {
  models.Post.findOne({
    where: {
      slug: req.params.slug
    }
  }).then(post => {
    if (!post) {
      return res.render("error", {
        message: "Page not found.",
        error: {
          status: 404,
        }
      });
    }

    post = post.get({ plain: true });
    client.getUser(post.authorId).then(user => {
      post.authorName = user.profile.firstName + " " + user.profile.lastName;
      res.render("post", { post });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 测试你的新 CRUD App！

至此，您已经使用 Express.js 和 Okta 构建了一个功能完整的 Node.js 网站。要测试它，运行以下命令启动您的 web 服务器，然后在浏览器中访问`http://localhost:3000`。

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

如果您能够正确地复制代码，您应该能够登录、创建帖子、编辑帖子和删除帖子。

## 用节点做更多事！

我希望你喜欢用 Node.js 和 Express.js 构建一个简单的 CRUD 应用程序。我发现 Express.js 拥有丰富的库和工具生态系统，可以使 web 开发变得简单有趣。您可以在 GitHub 上找到本教程[中创建的示例的源代码。](https://github.com/rdegges/okta-express-blog)

如果您想了解更多关于在 Node 中构建 web 应用程序的信息，您可能想看看这些其他很棒的帖子:

*   [使用 Passport.js 和 OpenID Connect 建立安全节点认证](https://dev.to/oktadev/build-secure-node-authentication-with-passportjs-and-openid-connect-3a60-temp-slug-6741213)
*   [用 Node、React 和 Okta 建立用户注册](https://developer.okta.com/blog/2018/02/06/build-user-registration-with-node-react-and-okta)
*   [简单节点认证](https://dev.to/oktadev/simple-node-authentication-366l-temp-slug-673868)
*   [用 Vue.js 和节点](https://developer.okta.com/blog/2018/02/15/build-crud-app-vuejs-node)构建一个基本的 CRUD App

如果您有兴趣了解更多关于底层认证组件如何工作(OpenID Connect)的信息，您可能会对我们的 [OpenID Connect 初级系列](https://developer.okta.com/blog/2017/07/25/oidc-primer-part-1)感兴趣，它解释了作为开发人员您需要了解的关于 OpenID Connect 的一切。

最后，请[在 Twitter 上关注@ okta dev](https://twitter.com/OktaDev)以找到更多类似的优秀资源，请求我们撰写其他主题，并关注我们新的开源库和项目！

还有…如果你有任何问题，请在下面留下评论！