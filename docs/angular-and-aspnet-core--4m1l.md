# 角形和 ASP.NET 芯

> 原文：<https://dev.to/ruidfigueiredo/angular-and-aspnet-core--4m1l>

[Angular CLI](https://cli.angular.io/) 提供了一种使用 Angular 开发前端应用的方法，隐藏了许多细节。例如，不需要了解[网络包](https://webpack.js.org/)或[系统如何工作。](https://github.com/systemjs/systemjs)

事实上，如果你对 Webpack(用于构建 Angular 应用程序的最新版本)了解不多，CLI 看起来就像魔术一样。你只需要做一个`ng new`和`ng serve --open`就可以在你的网络浏览器中打开一个可用的 Angular 应用程序。

CLI 隐藏了所有管道的事实可能会导致这样的问题:“我如何在 ASP.NET 核心中使用 Angular？”。

[![Angular and Asp.Net Core logos](img/5813b7d6ffd8dabe3c878f444bc3123d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dKBafg3O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.blinkingcaret.com/wp-content/uploads/2018/01/asp_net_core_and_angular_logo.png)

我希望在这篇博客文章结束时，你会清楚如何回答这个问题(不仅仅是 ASP.NET 核心，无论你想将 Angular 应用程序与哪种技术配合使用)。

你看，angular 应用程序本身就是一个应用程序，它确实需要由 web 服务器以某种方式“服务”。

当你编译一个 angular 应用程序时，你会产生一组 JavaScript、CSS 和一个 index.html 文件。就是这样。

那些“工件”被复制到的默认文件夹是`yourApplicationFolder/dist`。你可以去你的 Angular 应用程序做一个`ng build`来检查一下。

去吧，我等着。

当你这样做的时候，你实际上是在使用一个独立的 web 服务器( [webpack-dev-server](https://github.com/webpack/webpack-dev-server) )来服务 dist 文件夹中的 index.html 文件。

这篇博客文章的其余部分将描述几种方法，你可以用 ASP.NET 核心使用 Angular。第一种是让 ASP.NET 核心服务于 Angular 文件。

第二种方法是将角度核心和 ASP.NET 核心作为不同的应用。有一个如何使用 Nginx 实现这一点的示例，其中 Angular 和 ASP.NET 内核都使用端口 80 提供服务，而在 IIS 中，每个应用程序都从自己的端口提供服务。

帖子的最后部分描述了一个我认为理想的设置，在开发过程中你可以使用 Angular 的`ng serve`。

这篇文章很长，但是每个部分都是独立的。如果你只对最后一节感兴趣，并且你使用的是 Windows，我建议你也读一下关于如何在 IIS 中配置 Angular 的那一节。

## 利用 ASP.NET 核心为角应用服务

可以认为，在 ASP.NET 核心“内部”为 Angular 应用提供服务在资源方面是一种浪费。最终，Angular 应用程序只是一组静态文件，没有必要让对这些文件的请求通过 ASP.NET 核心中间件管道。

虽然这样做可能有一些好的理由，但是知道如何去做也没有坏处，因为这似乎是一种常见的方法，熟悉它可能是有用的。

为了理解我们如何一起为 ASP.NET 核心和 Angular 应用服务，需要知道的一件重要事情是理解请求在 ASP.NET 核心中是如何被处理的。

当你运行一个 ASP.NET 核心应用程序时，你的请求会通过一个[中间件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware?tabs=aspnetcore2x)的“管道”。每次请求进来时，它按照定义的顺序通过中间件，然后以相反的顺序。

每个中间件都有机会更改请求或响应两次，一次在其他中间件执行之前，另一次在其他中间件执行之后。这允许位于管道顶部的中间件处理例如由管道中更下方的中间件设置的 401 响应。

这方面的一个例子是将 401 响应更改为 302 重定向到登录页面的身份验证中间件。

您可以在`Configure`方法中的`Startup.cs`文件中找到该管道的定义。例如，这是你做`dotnet new mvc`时得到的管道:

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
} 
```

每次请求进入这个 ASP.NET 核心应用程序时，它最多只能通过三个中间件。首先是`DeveloperExceptionPage` / `ExceptionHandler`中间件，这取决于 ASP.NET 核心应用程序是否在开发模式下运行。然后是`StaticFiles`中间件，最后是`Mvc`中间件。

这里的关键中间件是`StaticFiles`。这是为包含在`wwwroot`文件夹中的文件提供服务的中间件，也就是说，如果一个请求传入 index.html，而在`wwwroot/index.html`有一个 index.html 文件，那么这个文件就被发送到客户端。`StaticFiles`此后，中间件不会调用它下面的中间件(在这种情况下是`Mvc`)。

您可能已经看到了这在角度应用程序中是如何工作的。就放在`wwwroot`下面吧。

这是绝对正确的，但是有一个关于`StaticFiles`的细节非常重要。`StaticFiles`不会尝试为你做任何猜测，即如果你的请求是针对`/`，`StaticFiles`不会寻找`/index.html`。它将假设这个请求不应该由它来处理，它将调用管道中的下一个中间件，在这种情况下是`Mvc`。

为了让这种方法工作，您需要另一个名为`DefaultFiles`的中间件，它必须位于管道中的`StaticFiles`之前:

```
//...
app.UseDefaultFiles();
app.UseStaticFiles();
//... 
```

[`DefaultFiles`](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/static-files) 会导致原因`StaticFiles`寻找`index.html`如果网址以`/`结尾。

现在唯一要做的就是配置 Angular CLI，编译到 ASP.NET 核心应用程序的 wwwroot 文件夹中。

如果你查看 Angular 的应用程序文件夹，你会发现一个. angular-cli.json 文件。在文件中查找`outDir`属性:

```
...
"apps": [
{
    ...
    "outDir": "dist",
... 
```

将其从“dist”更改为您的 ASP.NET 核心的 wwwroot 文件夹的路径。在你的 Angular 应用程序中运行`ng build`,现在如果你运行你的 ASP.NET 核心网络应用程序，你应该在浏览器中看到你的 Angular 应用程序。

一个很好的开发工作流程是在观察模式下运行 Angular CLI 构建:在控制台窗口中，如果您想保存一些击键，并让它运行，请执行`ng build --watch`或`ng build -w`。现在，每当你在 Angular 应用程序中进行更改时，你只需刷新浏览器就能看到更改(你还需要运行 ASP.NET 核心应用程序)。

不过，这种方法还缺少一点。深度链接支持，也就是说，如果你的 Angular 应用程序使用路由，并且你发送给用户一个带有有效 Angular 路由的 url(例如[http://yourapplication.com/products/5](http://yourapplication.com/products/5)),接收用户将无法打开它。尝试访问该路由将导致 404 Not Found 响应。

这是因为请求将一直通过您的 ASP.NET 核心应用程序的管道，当它到达 MVC 中间件时，它不知道如何处理它，并将响应的状态代码设置为 404 Page Not Found。

我们可以做的是在管道的顶部，我们寻找一个即将被发送的 404 响应，并改变它到我们的 Angular 应用程序的 index.html 文件的路径(这样，得到服务的是 Angular 应用程序，它将知道在路由方面如何处理 url)。在这之后，我们再次通过管道发出请求:

```
//add this at the start of Configure
app.Use(async (HttpContext context, Func<Task> next) =>
{
    await next.Invoke();

    if (context.Response.StatusCode == 404)
    {
        context.Request.Path = new PathString("/index.html");
        await next.Invoke();
    }
}); 
```

这修复了深层链接，但引入了一个新问题。如果您的 web api(您已经在 ASP.NET 核心应用程序中实现)需要发送 404 响应，该怎么办？这是非常合理的做法。服务调用将收到一个带有 index.html 的 200 响应，而不是 404。

这里的解决方案是查看 url 并决定它是用于 web api 还是角度路由。通常对 web api 的调用会在 url 中包含`/api`。这是一个简单的测试，可以解决这个问题。下面是解决这个问题的定制中间件的修订版:

```
//add this at the start of Configure
app.Use(async (HttpContext context, Func<Task> next) =>
{
    await next.Invoke();

    if (context.Response.StatusCode == 404 && !context.Request.Path.Value.Contains("/api")))
    {
        context.Request.Path = new PathString("/index.html");
        await next.Invoke();
    }
}); 
```

关于这种方法的最后一点说明。我看到过这样的例子，Angular 应用程序和 ASP.NET 应用程序在同一个 Visual Studio 解决方案中。Visual Studio(不是 VS Code)会尝试编译 typescript 文件。如果你正在使用`ng build -w`，你会希望 Visual Studio 不去管你的 Typescript 文件。[要做到这一点，打开你的项目的`.csproj`并添加任何`PropertyGroup`](https://stackoverflow.com/questions/42430300/disable-msbuild-typescript-compile) :

```
<TypescriptCompileBlocked>true</TypescriptCompileBlocked> 
```

## Nginx

Nginx 是一个 web 服务器，可以充当 ASP.NET 核心应用程序的反向代理，并且非常擅长提供静态内容。

在 Nginx 中，让 Angular 应用程序与 ASP.NET 核心一起工作的设置要简单得多。您只需要一个类似如下的配置:

```
server {
    listen 80;        

    location / {
        root /pathToYourAngularApplication/dist;
        index index.html;
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:5000;
    }
} 
```

这是一个典型的 Nginx 配置文件的样子。如果你不熟悉 Nginx 和 ASP.NET 核心我推荐我的博客文章:[ASP.NET 核心的 HTTPS 从零开始](http://www.blinkingcaret.com/2017/03/01/https-asp-net-core/)。其中有一节是关于如何使用 Nginx 安装和设置网站的说明。

这种配置允许我们在端口 80 上同时拥有角度和 ASP.NET 核心应用。让我们看看其中重要的部分。

`listen 80`语句确定 Nginx 将响应来自端口 80 的请求。

在`location`块中，我们将定义我们的两个应用程序(Angular 和 ASP。网)。每次有请求进来，Nginx 都会查看 URL，并试图找到与它最匹配的位置块。在这种情况下，位置块 url 的作用类似于“前缀匹配”，即第一个块将匹配每个 URL(每个以`/`开头的 URL)。第二个位置块匹配以`/api/`开头的 URL。

Nginx 选择最“具体”的位置块，因此即使对`/api/users`的请求匹配两个位置块，由于第二个位置块(`/api/`)更具体，它将被用来处理请求。

在第一个位置块(`/`):

`root /pathToYourAngularApplication/dist`将静态内容的查找路径设置为已编译的 Angular 应用程序文件的位置(`dist`是 CLI 的默认输出文件夹)。

`index index.html`指定应该为以`/`结尾的 URL 提供哪个文件。

`try_files $uri $uri/ /index.html`可以这样读:检查是否有匹配规范化 URL 的文件(如【http://www.yourwebsite.com/assets/image.jpg】->/assets/image . jpg)，如果该文件不存在，则尝试规范化 URL 加一个`/`(如[-](http://www.yourwebsite.com/documents)->/documents/->/documents/index . html，因为有`index`规则)。如果所有这些都失败，则为文件`/index.html`服务。

如果没有找到匹配，就提供服务`/index.html`,这使我们能够使用深度链接。例如，一个在文件系统中没有数学的 URL 如`http://www.yourwebsite.com/documents`将由 Angular 应用程序的 index.html 提供。`Index.html`将加载 Angular 应用程序运行所需的所有文件，特别是路由模块。路由模块然后会查看 url，并根据 angular 应用程序中定义的路由来决定加载哪个组件。

最后，最后一个位置块。它指示 Nginx 将从`/api/`开始的请求转发到一个在`localhost`上监听端口 5000 的 web 服务器。这将是你的 ASP.NET 核心的应用。

关于 Nginx 对`proxy_pass`的语法有一点需要注意。应用程序的 URL 末尾是否有一个`/`非常重要。如果`proxy_pass`中的 url 具有 [Nginx 文档中描述的“可选 URI”](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass)(可选 URI 不是一个好名字，因为最终 [URL 是一个 URI](https://danielmiessler.com/study/url-uri/) )，那么它将被区别对待。

带有可选 URI 的 URL 的一个例子是:`http://localhost:5000/optionalURI/`。如果位置的路径是`/api/`，那么对`http://yourwebsite.com/api/users`的请求将作为`http://localhost:5000/optionalURI/users`被转发到您的 ASP.NET 核心的应用程序。

这就是为什么不在`proxy_pass`的末尾添加`/`如此重要的原因，因为如果你这样做了(例如:`proxy_pass http://localhost:5000/;`)，它就属于“可选 URI”类别(它将被解释为空的可选 URI)，对`http://yourwebsite.com/api/users`的请求将在你的 ASP.NET 核心的应用中被视为对`http://localhost:5000/users`的请求。

如果您没有在末尾添加`/`(例如:`proxy_pass http://localhost:5000;`)，那么对`http://yourwebsite.com/api/users`的请求将在 ASP.NET 核心应用程序中被视为对`http://localhost:5000/api/users`的请求，这可能是您想要的。

如果你需要一个更完整的例子来解释如何在开发时场景之外完成这项工作(例如，让你的 ASP.NET 核心应用程序自动启动并保持在线，即使出现异常)，请从头开始查看 ASP.NET 核心的[HTTPS](http://www.blinkingcaret.com/2017/03/01/https-asp-net-core/)，其中有一个例子描述了你如何使用[管理器](http://supervisord.org/)来保持 ASP.NET 应用程序运行，即使在出现错误的情况下(通过自动重启它)。

## IIS

有了 IIS，拥有一个类似于 Nginx 的配置就变得非常麻烦，Nginx 的 Angular 和 ASP.NET 核心应用程序都在端口 80 上提供服务。

如果我们理解网站和应用程序的 IIS 概念，就可以理解为什么它会变得更容易。当您创建一个网站时，您需要定义(在其他设置中)提供服务的端口(例如 80)。一个网站“内部”可以有几个应用程序，所有这些应用程序将共享网站配置(因此在同一个端口上提供服务)。

例如，我们可以将 Angular 应用程序放在“默认网站”中，将 ASP.NET 核心应用程序作为 IIS 应用程序放在它的下面，并将其命名为“api”。

如果“默认网站”在 [http://localhost](http://localhost) 响应，那么 ASP.NET 核心应用程序可能在 [http://localhost/api](http://localhost/api) 。这似乎正是我们想要的。然而，在 url 中没有 api 的情况下，对 [http://localhost/api](http://localhost/api) 的请求可以在 ASP.NET 核心中看到。

据我所知，没有办法改变这种行为。

这意味着您的 ASP.NET 核心应用程序在 IIS 中运行时与直接执行时(无论是在 Visual Studio 中还是在 dotnet run 中)的行为会有所不同。

更糟糕的是，ASP.NET 核心应用程序需要发布(dotnet publish)才能在 IIS 中工作。它不像一个非核心的 ASP.NET 应用程序，你可以直接将一个 IIS 应用程序指向包含 ASP.NET 应用程序文件的文件夹。

因此，当使用 IIS 时，合理的选择是要么让 ASP.NET 核心服务于 angular 应用程序，如本文第一部分所述，要么拥有两个独立的网站。

让我们走一遍创建两个独立网站的过程。首先是 Angular 项目的网站，然后是 ASP.NET 核心网站。

### IIS 中的棱角

我们将在端口 80 上添加一个名为 *MyNgWebSite* 的网站。这意味着如果你有一个“默认网站”，你很可能会有，你需要停止它或改变它的绑定，因为它的默认端口是 80。

但是在此之前，我们需要为我们的 Angular 应用程序创建一个应用程序池。右键单击 IIS 管理器中的应用程序池:

[![Right click on application pools](img/5c5e92d96b3e3af24a22e2319e79e704.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3Uc2EqPB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.blinkingcaret.com/wp-content/uploads/2018/01/1_add_new_app_poll.png)

Angular 应用程序的应用程序池不需要托管代码(我们只需要提供静态文件)。我们应该在。NET CLR 版本:

[![No Managed Code application pool](img/c2db82bb5b7ea91cd3da1a541212fb2c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WlXuPIkR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.blinkingcaret.com/wp-content/uploads/2018/01/2_no_manage_code_app_pool.png)

我们现在可以添加一个新的 IIS 网站，并将我们创建的新应用程序池设置为它的应用程序池:

[![Add new web site](img/b7bec11910e77d6c4e193fa0543d92e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aWGVDJte--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.blinkingcaret.com/wp-content/uploads/2018/01/3_add_website.png)

[![Configure website](img/8b20165e2fee63bb3f648e8c8e8d1e68.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D_dzvsfP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.blinkingcaret.com/wp-content/uploads/2018/01/4_new_website_configuration.png)

物理路径应该设置为 Angular 项目被编译到的位置，通常是`dist`文件夹。

如果你现在试图访问`http://localhost`(假设你停止了“默认网站”或者使用了一个不同于 80 的端口)，你会得到一个权限错误。这是因为当你创建一个应用程序池时，一个[“虚拟”用户被创建](https://docs.microsoft.com/en-us/iis/manage/configuring-security/application-pool-identities)。该用户是本地用户，必须有权访问包含您试图提供的文件的文件夹。

那个用户的名字是`IIS AppPool\ApplicationPoolName`，在这个例子中是`IIS AppPool\ApplicationPoolForAngular`。

转到包含已编译的 Angular 项目的文件夹，右键单击它并选择 properties，转到 security 选项卡，单击 edit，然后添加，最后添加应用程序池用户:

[![Configure permissions for the folder](img/9e81f84e5ade97d622fca4aaeebb3713.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sa4AET4G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.blinkingcaret.com/wp-content/uploads/2018/01/5_permissions.png)

如果您转到`http://localhost`，我们现在应该可以访问您的角度应用程序。

不过，我们还需要做一件事。启用深度链接支持。

如果您的 Angular 应用程序中有路线，那么如果有人试图从 Angular 应用程序的“外部”访问它们，这些路线将不起作用。这意味着，如果导航到[http://localhost/documents](http://localhost/documents)在 Angular 应用程序中是有效的，并且您将该 url 发送给其他人，当其他人单击该链接时，他们将会看到来自 IIS 的 404 页面。

这是因为 IIS 既没有 documents 文件夹，也没有索引文件。我们需要告诉 IIS，当有人试图访问一个不存在的 URL 时，它必须提供文件`index.html`。

我们将使用与拥有一个[自定义 404 页面](https://dusted.codes/demystifying-aspnet-mvc-5-error-pages-and-error-logging)相同的机制，但是我们将服务于 Angular 应用程序，而不是 404 页面。

为此，我们需要创建一个`web.config`文件，并将其放在 Angular 应用程序的 src 文件夹中，其中包含:

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <httpErrors errorMode="Custom" existingResponse="Replace">
            <remove statusCode="404"/>
            <error statusCode="404" responseMode="ExecuteURL" path="/index.html"/>
        </httpErrors>
    </system.webServer>
</configuration> 
```

对正在发生的事情的快速解释。我们将`httpErrors`与`errorMode="Custom"`和`existingResponse="Replace"`一起使用。这指示 IIS 用我们将要指定的错误页替换默认错误页。

`remove statusCode="404"`将删除 404 页的任何自定义设置，如果它们已经存在的话。

如果出现 404 错误，`error statusCode="404" responseMode="ExecuteURL" path="/index.html"`将配置 IIS 执行`/index.html` url。这将有效地服务于您的 Angular 应用程序，并且不会改变客户端看到的 URL。

现在我们需要编辑`.angular-cli.json`文件，以便在编译应用程序时将`web.config`作为资产复制到输出文件夹中。“资产”部分在“应用程序”下，下面是一个示例:

```
{
"$schema": "./node_modules/@angular/cli/lib/config/schema.json",
"project": {
    "name": "your-app"
},
"apps": [
    {
    "root": "src",
    "outDir": "dist",
    "assets": [
        "assets",
        "favicon.ico", 
        "web.config"
    ],
    "index": "index.html",
... 
```

### IIS 中的 ASP.NET 核心

在 IIS 中配置 ASP.NET 核心应用程序的过程是类似的，尽管我们需要选择不同的端口。

但是在你开始之前，你需要确保你已经安装了用于 IIS 的 ASP.NET 核心模块。如果您安装了，它可能已经安装了。Net Core SDK，但是最好的确认方法是转到 IIS 管理器，看看它是否在模块列表中:

[![Module list with AspNetCoreModule](img/feb43d5a7ba6dd35a69888e022a562a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lulvsErp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.blinkingcaret.com/wp-content/uploads/2018/01/AspNetCoreModule.png)

如果你没有它，你可以找到更多关于它的信息[这里](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/aspnet-core-module?tabs=aspnetcore2x)和下载它的直接链接[这里](https://go.microsoft.com/fwlink/?linkid=837808)。

该模块负责启动和保持 ASP.NET 核心应用程序运行。

在我们用 IIS 创建网站之前，我们需要 ASP.NET 核心应用程序的发布版本。您可以在命令行中使用`dotnet publish`来完成，或者在完整的 Visual Studio 中，右键单击项目并选择 Publish，然后单击 publish to folder。

创建一个新的网站，指向 ASP.NET 核心项目发布文件夹，给它一个不同的端口号(例如 8080)，并为它创建一个应用程序池。

ASP.NET 核心应用程序的应用程序池也是非托管的(没有托管代码)。虽然这看起来很奇怪，但这是因为 [IIS 实际上只是充当了一个反向代理](https://weblog.west-wind.com/posts/2016/Jun/06/Publishing-and-Running-ASPNET-Core-Applications-with-IIS)。

在我们能够使用 IIS 运行 ASP.NET 项目之前，我们需要更改已发布文件夹的权限，以便应用程序池用户可以访问它。如果您不这样做，您将会得到这个不太有用的错误消息:

> HTTP 错误 500.19 -内部服务器错误
> 无法访问请求的页面，因为该页面的相关配置数据无效。

[![IIS permissions error](img/46628942d563ce61e5b41c6c2f2f6261.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f6AD1VO6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.blinkingcaret.com/wp-content/uploads/2018/01/Error_500_19_No_Permissions.png)

如果您查看`Config Error`部分，您会看到“由于权限不足，无法读取配置文件”，这几乎说明了一切。

转到已发布的文件夹，将应用程序池用户添加到对该文件夹拥有权限的用户列表中。

您的 ASP.NET 核心应用程序现在应该可以在您在 IIS 中创建网站时选择的端口上使用。然而，如果你试图从 Angular 应用程序调用它，你会得到这个错误“加载失败...请求的资源上没有“Access-Control-Allow-Origin”标头..."。下面是一个在开发人员工具控制台选项卡中的示例:

[![Failed CORS request](img/9d125209b7aedc10cda373f664c085da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J7L2fT14--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.blinkingcaret.com/wp-content/uploads/2018/01/failed_cors.png)

这是因为即使我们的 Angular 和 ASP.NET 核心应用程序在同一个域中，但它们在不同的端口，这足以在除 IE 之外的所有浏览器中将该请求定义为跨源资源共享(CORS)请求。

我们需要在 ASP.NET 核心应用上启用 CORS。为此，我们需要添加包`Microsoft.AspNetCore.Cors`，并在`Startup.cs`的`ConfigureServices(IServiceCollection services...`方法中添加`services.AddCors()`:

```
public void ConfigureServices(IServiceCollection services)
{
    //...
    services.AddCors();
    //...
} 
```

在`Configure`方法中，我们需要创建一个“策略”,表明我们正在等待来自`http://localhost`的请求。我们应该在 MVC 中间件出现之前这样做:

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    //...
    app.UseCors(builder => builder.WithOrigins("http://localhost"));
    app.UseMvc();
} 
```

你应该可以走了。你的角和 ASP.NET 核心应该都工作了。

## 平台无关的开发设置

Angular 和 ASP.NET 核心应用程序都提供了检测它们是运行在开发模式还是生产模式的方法。可以利用它来创建一个在 Linux、Windows 或 Mac 上都可以工作的设置。

运行 Angular 应用程序最简单的方法是使用 run `ng serve`。这启动了一个 [webpack 开发服务器](https://webpack.js.org/configuration/dev-server/),默认情况下在端口 4200 上为 Angular 应用程序提供服务。

这也有用[热模块代替](https://webpack.js.org/concepts/hot-module-replacement/)的优势，这意味着你可以立即看到你对 Angular 应用程序的更改，甚至不需要刷新浏览器。

理想情况下，我们希望这样运行角度应用程序。

对于 ASP.NET 核心应用程序，我们希望在不发布它的情况下运行它，如果它由 IIS 提供服务，您就必须发布它。

这是理想的开发场景，`ng serve`对于 Angular 和`dotnet run`或者从 Visual Studio 运行 ASP.NET 核心而不必发布它。

在这个理想的场景中，在开发时，我们可以让 Angular 应用程序运行在端口 4200 上(通过`ng serve`),让 ASP.NET 核心应用程序运行在端口 5000 上。在生产过程中，角度应用程序通常由端口 80 提供服务，而 ASP.NET 核心应用程序则由端口 8080 提供服务(或者由端口 80 上的不同服务器提供服务)。

在 ASP.NET 核心部分，我们必须配置 CORS 在开发时接受来自端口 4200 的请求，在生产时接受来自端口 80 的请求。在 Startup.cs 中应该是这样的:

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors();
    //...        
}

// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    //...
    if (env.IsDevelopment())
    {
        //...
        app.UseCors(builder => builder.WithOrigins("http://localhost:4200"));
    }else 
    {
        app.UseCors(builder => builder.WithOrigins("http://localhost"));
    }

    app.UseMvc();
} 
```

这就是 ASP.NET 核心应用程序。

对于 Angular，我们需要利用 environemnt.ts 和 environment . prod . ts 文件。你可以在 Angular 项目的`src`文件夹下的文件夹名`environemnts`下找到 then。

您在 environment.ts 中设置的内容将在您以开发模式(默认)运行时可用，而 environment.prod.ts 中的值将在生产中使用。要在环境设置为生产的情况下编译 Angular 项目，使用`--env=prod`标志(如`ng build --env=prod`)。

下面是一个简单的例子，说明如何配置环境文件来支持我们假设的场景 environment.ts:

```
export const environment = {
    production: false,
    apiBaseUrl: "http://localhost:4200/"
}; 
```

环境产品:

```
export const environment = {
    production: true,
    apiBaseUrl: "http://localhost/"
}; 
```

在您的角度服务中，要获得环境值，您只需导入环境(总是 environment，而不是 environment.prod):

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { environment } from '../environments/environment';

@Injectable()
export class MyServiceService {

    constructor(private httpClient: HttpClient) { }

    getStuff(){
        return this.httpClient.get(`${environment.apiBaseUrl}/api/suff`);
    }  
} 
```

即使你在 Nginx 或 IIS 上运行，这种方法也是可行的，所以如果你需要/想要支持开发者使用不同的平台，或者如果你只是想比较它们之间的性能，这可能是最好的选择。