# 在 ye good olde JS 中制作单页应用程序(ES6)

> 原文：<https://dev.to/rishavs/making-a-single-page-app-in-ye-good-olde-js-es6-3eng>

[![Imgur](../Images/6acfc29fa29f3f9ba4842b247ab20e18.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NMQU6rfQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Nh6IfFz.png)

演示:[https://src-brsetrrnrp . now . sh/](https://src-brsetrrnrp.now.sh/)
rest:[https://github . com/rishavs/vanillajs-spa](https://github.com/rishavs/vanillajs-spa)

如今，制作 SPA(单页应用程序)风靡一时。SPA 要快得多，能提供更好的 UX，而且开发和部署也简单得多。但是，要开发一个 SPA，通常需要大量语义庞大的框架，如 React、Vue 等，并且通常，为了正确使用这些框架，人们需要更多的关于这些框架的知识，而不是核心语言本身。

通过这篇文章，我想告诉你，你不需要理解多种生态系统和框架来制作简单的前端应用。简单的 JS 就是你所需要的。我向你保证，整个应用程序将会变得更加简单，更容易反驳。

几个月前，当我在 Crystal (love the language)中制作 weabpp 时，我从一个简单的基于服务器的 webapp 开始，但很快意识到我编写的 JS 代码量太多了。我的服务器代码库实际上要小得多。因此，当然，我告诉自己，我还不如用 JS 本身来制作整个应用程序。

(注意:这是一个有问题的逻辑，但我为了它的乐趣而编码，用普通的 JS 创建一个简单的 SPA 听起来像是有史以来最有趣的想法！)
(“旁白:不，不是的”)。

令人惊讶的是，关于用香草制作 spa 的文章并不多，一些指导读者的文章也使用了很多图书馆。为什么我强调普通的 JS？这[页](http://vanilla-js.com/)提供了一些令人信服的论点。

我的计划是不使用库，除非我清楚地知道
——我浪费了太多的时间重新发明轮子
——这超出了我的能力。

所以，先手工码香草 JS。仅在需要时使用库。您使用的每一位第三方代码都有自己的包袱，您应该经常检查包袱的重量是否小于使用它的收益。

这也有助于我制作一个简单的博客/黑客新闻类型的应用程序；一页是文章列表，另一页是详细的文章。新版本的 JS (ES6)非常适合处理这些问题。

如果你必须在需要实时更新 DOM 的 UX 上工作，你真的应该坚持像 React，Vue 或者(我个人最喜欢的)Mithril 这样的框架。

**TLDR**；简单明了就好。简单朴素并不总是*的*好。
了解你的需求以及实现这些需求的最佳工具。

在我们开始实现之前，让我们看看我们正在开发什么。

该应用程序将有以下页面:
-主页:包含所有帖子的列表。显示从 api 获取并在此呈现的数据。
-特定 id 的帖子:包含该帖子的详细信息。显示了动态 url 解析。
-关于:仅包含一些文本。这是为了展示路由器
-秘密:这一页不在我编写的路由中，将用于展示 404 处理。
-注册:包含一个表单，点击一个按钮，显示表单数据。

我的应用程序本身的每个页面将有以下结构:
-导航栏
-内容部分
-页脚

我的应用程序中的每条路线都有以下结构:
-资源
-标识符
-动词

例如，
如果 url 是“localhost:8080/#/users/rishav/edit”，那么
resource = "users "，identifier = "rishav "，verb = "edit "。

因为我有一个非常固定的 URL 结构，所以为它制作一个基于散列的路由器变得非常容易。在我的路由中，资源和动词字符串是预定义的，但是标识符是动态的。

你可以在
查看一下[演示应用](https://src-brsetrrnrp.now.sh/)请注意，我并没有把它做得很漂亮，因为这不是本文的目的。还有，我懒:)

* * *

既然你已经玩了一点，让我们进入本质。
目前，我在开发环境中使用[实时服务器](https://github.com/tapio/live-server)为我的 spa 服务。你可以使用任何你想要的服务器。在我的例子中，我将“localhost:8080”作为我的开发域。
我还使用 [https://www.mockapi.io](https://www.mockapi.io) 通过 REST API 用样本数据填充我的应用程序。

本文将关注 spa 的以下主要方面:
-路由器(使用 url 散列)和
-模板化(使用 ES6 模板文字)和
-项目架构(使用 ES6 模块)

### 路由器

路由器背后的核心思想是在 URL 中使用哈希“#”。每当浏览器在 URL 中遇到这个字符时，就会跳过其后的所有内容。因此，从浏览器的角度来看,“localhost:8080/#/nowhere”和“localhost:8080/#/something”是相同的，它不会发送服务器请求来获取整个服务器端路由。你可以在 https://en.wikipedia.org/wiki/Fragment_identifier 了解更多信息

我们代码的路由部分是:

```
// List of supported routes. Any url other than these routes will throw a 404 error
const routes = {
    '/'             : Home
    , '/about'      : About
    , '/p/:id'      : PostShow
    , '/register'   : Register
};

// The router code. Takes a URL, checks against the list of supported routes and then renders the corresponding content page.
const router = async () => {

    // Lazy load view element:
    const content = null || document.getElementById('page_container');

    // Get the parsed URl from the addressbar
    let request = Utils.parseRequestURL()

    // Parse the URL and if it has an id part, change it with the string ":id"
    let parsedURL = (request.resource ? '/' + request.resource : '/') + (request.id ? '/:id' : '') + (request.verb ? '/' + request.verb : '')

    // Get the page from our hash of supported routes.
    // If the parsed URL is not in our list of supported routes, select the 404 page instead
    let page = routes[parsedURL] ? routes[parsedURL] : Error404
    content.innerHTML = await page.render();
    await page.after_render();

}

// Listen on hash change:
window.addEventListener('hashchange', router);

// Listen on page load:
window.addEventListener('load', router); 
```

在这段代码中，我引用了另一个文件中的一个简单函数。

```
 parseRequestURL : () => {

        let url = location.hash.slice(1).toLowerCase() || '/';
        let r = url.split("/")
        let request = {
            resource    : null,
            id          : null,
            verb        : null
        }
        request.resource    = r[1]
        request.id          = r[2]
        request.verb        = r[3]

        return request
    } 
```

让我们看看当用户将下面的 url 放入地址栏并单击 enter 时会发生什么；
-localhost:8080/#/关于

首先，当使用浏览器加载事件时，这段代码`window.addEventListener('load', router);`被触发。
然后它调用路由器函数。
路由器函数首先从地址栏获取 url，并使用函数`parseRequestURL`，将其分解为资源、标识符和动词的路由模式。
然后，这个 url 通过连接每个 url 模式元素进行重组。
然后将最终的 url 字符串与我们支持的现有路线图进行比较。

```
const routes = {
    '/'             : Home
    , '/about'      : About
    , '/p/:id'      : PostShow
    , '/register'   : Register
}; 
```

这里，每个支持的路由都映射到一个内容页面。
`let page = routes[parsedURL] ? routes[parsedURL] : Error404`
然后，我们检查解析后的 url 字符串是否作为关键字存在于我们的路线图中。
如果是，变量`page`得到相应的值。否则，它将获取 error404 页的值。

在我们的例子中，资源是“About”，标识符是 nil，动词是 nil。因此，我们在路线图中查找“about ”,并使用；

```
 content.innerHTML = await page.render(); 
```

现在让我们考虑一个动态路由“localhost:8080/#/p/1234”。

这里 resource = "p "，identifier ="1234 "，动词是 nil。
由于我们已经检测到一个标识符(可以是任何字符串)，我们需要用一个固定的字符串来替换它。所以，我只是用一个固定的字符串“:id”替换任何标识符。
这允许我定义一个简单的路径为`"/p/1234" : PostShow`，其中 PostShow 页面将动态显示特定标识符的数据。

当然，我们还需要确保所有应用内的链接都包含“#”。例如，这是导航栏中“关于”页面链接的 html

```
<a class="navbar-item" href="/#/about">
    About
</a> 
```

当用户点击这个链接时，我们定义的第二个全局事件`window.addEventListener('hashchange', router);`被触发。然后路由器函数又被调用了等等。
因此，本质上，每当用户更改 url 并加载页面(输入、刷新等)或使用应用内超链接导航时，我们都会重新呈现应用的页面内容。

当然，我们的导航状态也保存在浏览器历史中，因此您可以使用后退/前进浏览器按钮在历史状态中导航。

如果用户尝试一个类似“localhost:1234/nowhere”的 URL(URL 中在原点后面根本没有#号)会怎么样？这不是我们纯客户端路由器能够处理的事情。在大多数像 Netifly 这样的 spa 托管解决方案中，您可以指定一个 404 页面(我们在前端应用程序中使用的页面的预渲染版本)，在这种情况下提供服务。

## 模板

让我们看一下我们正在生成的一个非常简单的页面，About 页面；

```
let About = {
    render : async () => {
        let view =  /*html*/`
            <section class="section">
                <h1> About </h1>
            </section>
        `
        return view
    }

}

export default About; 
```

我使用 ES6 模板文字来定义我的 html 模板。`/*html*/`位只是一个 pragma，我的 VSCode 扩展使用它在编辑器中正确格式化 html 内容。
async 位在这里没有多大意义，但是对于从远程数据源获取和显示数据的页面来说是必需的。我把我的“关于”页面视为一个页面模板，每次我需要在应用程序中添加新页面时，我只需要做一点复制-粘贴-重命名的操作。
如果你注意到了，模板字符串在函数内部。这意味着渲染函数只有在我们需要的时候才会被计算。
早些时候，我刚刚做过类似的事情；

```
let About = /*html*/`
            <section class="section">
                <h1> About </h1>
            </section>
            `

export default About; 
```

这是一个大问题，因为无论我在应用程序的哪个位置，它都会评估这个页面。如果我的应用程序中有 20 个页面，其中许多获取了一些数据，那么无论我渲染应用程序的哪个区域，它都会每次评估整个应用程序(并获取我从不需要的页面的所有数据)。

函数式方法还意味着，如果我们想在一个页面中包含一些小组件，可以使用函数 params 将数据传递给每个组件进行呈现。例如，如果我想让主页中的每个链接都变成一张卡片，我可以调用每个链接的 render 函数`card.render(data)`
并按程序读取数据来相应地呈现它。

现在下一步要解决的是为我们的页面添加交互控件。例如，如果我们的“关于”页面有一个按钮；当点击一个浏览器警告时，我们应该把与这个按钮相关的代码放在哪里？

之前，我在基于字符串的 html 中添加了一个脚本标签，但是无论我做什么，里面的代码都不会运行。最后，另一个论坛的一位先生帮助了我。原来通过 innerHTML 方法添加的脚本标签是不求值的！疯狂，我知道！-__-
由于安全隐患，Javascript 充满了这些小陷阱。

所以，这种选择是不可能的。我也不能只是在全局范围内添加实际的代码，然后通过使用按钮上的 onclick 属性来引用它。

为页面控件添加事件处理程序的方法是向页面对象添加另一个函数，如下所示；

```
let About = {
    render : async () => {
        let view =  /*html*/`
            <section class="section">
                <h1> About </h1>
                <button id="myBtn"> Button</button>
            </section>
        `
        return view
    },
    after_render: async () => {
        document.getElementById("myBtn").addEventListener ("click",  () => {
            console.log('Yo')
            alert('Yo')
        })
    }

}

export default About; 
```

并在路由器中作为消费；

```
 content.innerHTML = await page.render();
    await page.after_render(); 
```

这允许我定义按钮按下的代码，并且这些代码在我的 dom 呈现后立即被初始化。

## 应用程序结构

[![Imgur](../Images/fa7a8b2d0b5f61c4b253663a3abaa878.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GCliIVAo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/kzyHwsI.png) 
这是我如何构建这个应用程序的。views 文件夹包含我的动态内容[页面]和较小的组件[组件]。如果我添加可重复使用的组件，如卡片、评论等，我将在这里为它们添加组件。

如果你已经注意到，我们没有使用任何捆绑器，如 parceljs，webpack 等，因为 ES6 以后我真的不需要。我可以使用脚本标签类型=模块的简单 html 指令来告诉浏览器，我们的 JS 中有一个模块化的应用程序，它需要考虑 ES6 导入/导出命令，并相应地缝合模块。这就是我们如何在核心 html 文件中声明应用程序的根；

```
 <script type="module" src="/app.js"></script>
</head>

<body>
    <div id="header_container"></div>

    <div id="page_container" class="container pageEntry" >
        <article> Loading....</article>
    </div>

    <div id="footer_container"></div>
</body> 
```

如果你对“header _ container”/“footer _ container”感兴趣，这些是包含我的 Navbar 和页脚的 dom 元素。每条路线的导航栏和页脚将是相同的，内容部分将是动态的，并将根据当前选择的路线而变化。

我声明这些组件的方式与声明和使用我的页面的方式相同。

例如，这是一个页脚；

```
let Bottombar = {
    render: async () => {
        let view =  /*html*/`
        <footer class="footer">
            <div class="content has-text-centered">
                <p>
                    This is my foot. There are many like it, but this one is mine.
                </p>
            </div>
        </footer>
        `
        return view
    },
    after_render: async () => { }

}

export default Bottombar; 
```

这是我修改过的路由器，我在这里使用这个页面；

```
const router = async () => {

    // Lazy load view element:
    const header = null || document.getElementById('header_container');
    const content = null || document.getElementById('page_container');
    const footer = null || document.getElementById('footer_container');

    // Render the Header and footer of the page
    header.innerHTML = await Navbar.render();
    await Navbar.after_render();
    footer.innerHTML = await Bottombar.render();
    await Bottombar.after_render(); 
```

所以不管通过 url 输入什么路径，我都会显示导航栏和页脚。

当然，你可能仍然想使用捆绑器来做一些事情，比如缩小，压缩你的 js 文件，它们会是一个很好的解决方案。我个人现在并不关心这样的优化，从整个 webpack config > babel rabbit hole 中获得的纯粹自由是非常令人宽慰的。

咻。看看时间！我们现在已经完成了简单的博客风格 spa！

* *我强烈建议您在[https://github.com/rishavs/vanillajs-spa](https://github.com/rishavs/vanillajs-spa)**仔细阅读实际代码

代码很简单，注释良好，符合我在本指南中所写的内容。希望它能帮助你在混乱的 JS 框架丛林中踏上一条干净的道路。

### 信用:

我自己也参考了很多指南和文章才能够做出这个 app。
对于路由器来说，这一次是帮了大忙；
[https://medium . com/@ bryanmanuele/how-I-implemented-my-own-spa-routing-system-in-vanilla-js-49942 e3c 4573](https://medium.com/@bryanmanuele/how-i-implemented-my-own-spa-routing-system-in-vanilla-js-49942e3c4573)