# 在本地移动应用中使用 WordPress 内容

> 原文：<https://dev.to/progress/using-wordpress-content-in-a-native-mobile-app-3ome>

WordPress 是目前最流行的内容管理系统(CMS)。60%的 CMS 市场被 WordPress 占有，此外，几乎 30%的**所有网站**都是在 WordPress 上运行的[。这意味着许多网站上的大量内容渴望以新的方式在新的设备上使用。在某些情况下，在原生移动应用中利用这些内容是非常有意义的。输入 NativeScript。](https://w3techs.com/technologies/overview/content_management/all)

[![wordpress and nativescript](img/69a0e577210c2b41e6c2e59f8f552bf9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t5Xr4jMq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/wordpress/wordpress-plus-nativescript.png)

是的，WordPress 是用于管理网页内容(HTML)的，NativeScript 是用于构建跨平台原生移动应用的框架(显然*不是* HTML)。那么这两者有什么共同点呢？

> 我们不提倡在手机 app 中重新创建网站。对于一个不独特或“不像应用程序”的应用程序，你有违反[苹果条款](https://developer.apple.com/app-store/review/guidelines/#minimum-functionality)的风险，更重要的是，如果你只是简单地重新创建相同的网络体验，用户可能会避开你的应用程序。这是一个发挥创造力的机会！👨‍🎨

## API FTW

正如任何伟大的关系一样，这一切都始于 RESTful API...

NativeScript + WordPress =😍

开箱即用，WordPress 包括用于 WordPress 数据类型的 RESTful API 端点，为 web 开发者(以及移动和桌面)提供了以新的和令人兴奋的方式与存储内容进行交互的能力。当然，所提供的端点是语言不可知的。任何可以消费 JSON 的框架都会很乐意消化 WordPress 提供的东西。鉴于 NativeScript 应用程序是基于 JavaScript 构建的，通过一个简单的`fetch`调用来使用这样一个 API 是很常见的。

## 我们来建个 App 吧

我想象如果你在这里，你有一个现有的 WordPress 网站，有几周、几个月甚至几年的内容。至少可以说，在一个本地的、跨平台的移动应用程序中重新利用所述内容的潜力是有趣的。

我认为没有比亲自动手更好的学习方法了。**所以我们来建个 app 吧！**

让我们组装一个简单的 NativeScript 应用程序来利用 WordPress 内容类别、帖子和帖子内容，在 iOS 和 Android 上运行，所有这些都来自同一个共享代码库。

> 虽然深入研究 WordPress API 超出了本文的范围，但可以说:[这个 API 有很好的文档记录](http://v2.wp-api.org/)。

## 原生脚本助手

相信我，当我说每一个好的 NativeScript 应用都是从由 NativeScript 助手提供的初学者工具包开始的。

Sidekick 是一款适用于 Mac、Windows 和 Linux 的免费工具，运行在 NativeScript CLI 之上，为您提供模板、插件管理、云构建和应用商店发布。

> 在这个[“Sidekick 周”博客系列](https://www.nativescript.org/blog/welcome-to-a-week-of-nativescript-sidekick)中，阅读 NativeScript Sidekick 提供的所有特性。

一旦你安装了 Sidekick，打开它，**创建一个新的应用程序**，并选择**空白**模板:

[![nativescript sidekick starter templates](img/5c911015a4cc37d62d4943db7ca699d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XOTPgBgD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/wordpress/sidekick-starter-kits.png)

我将坚持使用普通的 JavaScript，但是如果您对这些架构更熟悉，也可以使用 TypeScript 或 Angular。

在打开我们选择的代码编辑器之前，让我们向我们的应用程序添加几个我们知道会需要的页面。

点击**新页面**按钮，向我们的应用程序添加另外两个页面或视图。

[![nativescript sidekick add new page](img/6ff7687c78759d7fe68000185b02f0be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k-dgLv1v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/wordpress/sidekick-new-page.png)

两个页面都可以只是**空白**页面，可以命名第一个`category`和第二个`post`。

## 代码

我们的脚手架应用有三个基本视图:

*   `home-page.xml`(自带空白模板)
*   (这是你创造的)
*   (这也是你创造的)

> 值得注意的是，如果你迷路了，可以在 Github 上找到这个应用的完整版本[！](https://github.com/rdlauer/tns-wordpress)

我们的视图只是一个按钮。因为谁不爱一个好纽扣呢？

[![wordpress button screen](img/3fd53aeeeb40ba77b2acd20f3e8834b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FgiAXM7e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/wordpress/wordpress-button-screenshot.png)

为了呈现这个屏幕，我们的`/home/home-page.xml`文件只需要一些简单的带有按钮的布局代码:

```
<Page 
    class="page bg"
    backgroundSpanUnderStatusBar="true">

    <Page.actionBar>
        <ActionBar title="WordPress + NativeScript = ❤️" class="action-bar">
        </ActionBar>
    </Page.actionBar>

    <StackLayout class="p-20">
        <Label text="WordPress Demo" class="h1 text-center m-t-30 heading"/>
        <Button text="Load Categories" tap="showCategories" class="btn btn-primary btn-active"/>
    </StackLayout>

</Page> 
```

Enter fullscreen mode Exit fullscreen mode

...它对应的`home-page.js`文件需要一点管道来连接按钮，把我们送到下一个视图，`category-page` :

```
 var frameModule = require('ui/frame');

    exports.showCategories = function() {
      var navigationEntry = {
        moduleName: './category/category-page',
        animated: true
      };
      var topmost = frameModule.topmost();
      topmost.navigate(navigationEntry);
    }; 
```

Enter fullscreen mode Exit fullscreen mode

**现在事情变得有趣了。**打开`/category/category-page.xml`，用下面的本地脚本`ListView`(包括一个项目模板)替换现有代码，如下所示:

```
 <Page 
        class="page bg"
        loaded="pageLoaded">

        <Page.actionBar>
            <ActionBar title="WordPress Categories" icon="" class="action-bar">
                <NavigationButton text="Back" android.systemIcon="ic_menu_back" />
            </ActionBar>
        </Page.actionBar>

        <ListView id="listview" items="{{ items }}" class="list-group">
            <ListView.itemTemplate>
                <StackLayout class="list-group-item" id="{{ id }}" tap="showPost">
                    <Label text="{{ name }}" class="wp-category" />
                        <Label text="{{ description }}" textWrap="true" class="wp-subtitle" />
                    </StackLayout>
            </ListView.itemTemplate>
        </ListView>

    </Page> 
```

Enter fullscreen mode Exit fullscreen mode

该视图附带的 JavaScript 文件`category-page.js`包含两个函数。毫不奇怪，`pageLoaded`会在页面加载时执行，`showPost`会将我们导航到下一个视图(`post-page`，保留用户点击的类别的*上下文*:

```
 var frameModule = require('ui/frame');
    var Observable = require('data/observable').Observable;
    var ObservableArray = require('data/observable-array').ObservableArray;

    var page;
    var items = new ObservableArray([]);
    var pageData = new Observable();

    exports.pageLoaded = function(args) {
      page = args.object;
      page.bindingContext = pageData;

      fetch('https://demo.wp-api.org/wp-json/wp/v2/categories')
        .then(response => {
          return response.json();
        })
        .then(function(r) {
          pageData.set('items', r);
        });
    };

    exports.showPost = function(args) {
      var navigationEntry = {
        moduleName: './post/post-page',
        animated: true,
        context: { id: args.view.id }
      };

      var topmost = frameModule.topmost();
      topmost.navigate(navigationEntry);
    }; 
```

Enter fullscreen mode Exit fullscreen mode

留给我们一个可爱的小屏幕，包含我们的 WordPress 文章类别:

[![wordpress categories](img/bbdef52530e380cef7162581465eef6c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x0A2iGlY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/wordpress/wordpress-categories-screenshot.png)

`category-page.js`中的关键代码是[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 。`fetch`允许我们从一个远程端点请求数据，并在 JSON 中返回数据，使它在我们的应用程序中易于使用！

> 你还会很快注意到我们使用的 API 端点利用了 WordPress 演示数据集。*拥有大量的知识。*

如果我们看一看返回的 JSON，我们会看到一个非常清晰的数据集:

```
 "id":2,
    "count":3,
    "description":"Neque quibusdam nihil sequi quia et inventore",
    "link":"https:\/\/demo.wp-api.org\/category\/aut-architecto-nihil\/",
    "name":"Aut architecto nihil",
    "slug":"aut-architecto-nihil",
    "taxonomy":"category",
    "parent":0,
    ... 
```

Enter fullscreen mode Exit fullscreen mode

好了，让我们结束，用另一个`ListView` :
替换`post/post-page.xml`

```
 <Page 
        class="page bg"
        navigatedTo="pageNavigatedTo">

        <Page.actionBar>
            <ActionBar title="WordPress Posts" icon="" class="action-bar">
                <NavigationButton text="Back" android.systemIcon="ic_menu_back" />
            </ActionBar>
        </Page.actionBar>

        <ListView id="listview" items="{{ items }}" class="list-group">
            <ListView.itemTemplate>
                <StackLayout class="list-group-item" link="{{ link }}" tap="loadWebSite">
                    <Label text="{{ title.rendered }}" class="wp-subtitle" />
                    </StackLayout>
            </ListView.itemTemplate>
        </ListView>

    </Page> 
```

Enter fullscreen mode Exit fullscreen mode

...同样，我们的`post-page.js`代码为视图提供了动力——并包含另外两个函数:`pageNavigatedTo`和`loadWebSite`,它们分别执行一个`fetch`请求来加载我们的帖子，并启动一个[本地脚本 WebView](https://docs.nativescript.org/cookbook/ui/web-view) ,在应用内的 web 浏览器中显示帖子内容的 HTML 输出。

```
 var frameModule = require('ui/frame');
    var pageModule = require('ui/page');
    var webViewModule = require('ui/web-view');
    var Observable = require('data/observable').Observable;
    var ObservableArray = require('data/observable-array').ObservableArray;

    var page;
    var items = new ObservableArray([]);
    var pageData = new Observable();

    exports.pageNavigatedTo = function(args) {
      page = args.object;
      page.bindingContext = pageData;

      var id = page.navigationContext.id;

      fetch('https://demo.wp-api.org/wp-json/wp/v2/posts?categories=' + id)
        .then(response => {
          return response.json();
        })
        .then(function(r) {
          pageData.set('items', r);
        });
    };

    exports.loadWebSite = function(args) {
      var link = args.view.link;

      var factoryFunc = function() {
        var webView = new webViewModule.WebView();
        webView.src = link;
        var page = new pageModule.Page();
        page.content = webView;
        return page;
      };

      var topmost = frameModule.topmost();
      topmost.navigate(factoryFunc);
    }; 
```

Enter fullscreen mode Exit fullscreen mode

> 为了节省空间，我忽略了一些细节，但提醒一下，所有这些代码都可以在 Github 上找到。

**我们完成了！**嗯，如果你照原样运行这个应用程序，它可能看起来不完全像这些截图。直到你从 Github 中抓取完成的`app.css`img/bg.png`背景图片和`/fonts`、[字体文件，并将它们添加到你的应用程序中。](https://github.com/rdlauer/tns-wordpress)

## 部署您的应用

回到 NativeScript Sidekick，进入**运行**菜单，选择**在设备**上运行。选择要在其上运行应用的连接设备，并使用我们的云服务器构建应用(或者在本地构建，如果您设置了适当的 SDK)。

> 部署应用程序有困难吗？查阅[本地脚本助手文档](https://docs.nativescript.org/sidekick/intro/introduction)！

[![nativescript sidekick run on device](img/94c4088e11cefd27b6a2a573acb9d158.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pRcJ8n8z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/wordpress/sidekick-run-on-device.png)

在网络和移动平台之间共享网站内容是一回事。**与移动端共享实际网站代码怎么样？**虽然与 WordPress 无关，但如果你是 Angular 开发人员，并且对 NativeScript + Angular web/mobile 代码共享故事感兴趣，请务必[查看我们在 YouTube 上的代码共享网络研讨会](https://www.youtube.com/watch?v=lumqZlnDs_I)。

## 总结

今天，我们看了如何使用 WordPress REST API 来消费现有的 WordPress 内容，从而使用 NativeScript 开发一个真正的本地、跨平台的应用程序。通过使用一点 JavaScript 和 CSS，您可以重新利用多年的内容，并为您的用户提供一种全新的、引人入胜的用户体验。祝圣诞快乐！😁