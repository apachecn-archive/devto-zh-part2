# 5 分钟后开始使用 Next.js

> 原文：<https://dev.to/benhayehudi/getting-started-with-nextjs-in-5-minutes-19ah>

[![](../Images/0602fab51eddd17ef1324736e917dcff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sItCGY2B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/next.jslogo.png)

[Next.js](https://nextjs.org/) 是一个轻量级的开源 Javascript 框架，构建在 React 库之上，支持快速的服务器端渲染，并且是服务器不可知的(即使用其内置的 HTTP 服务器或使用任何 Node.js 服务器)。它是由[时代](https://zeit.co/about)的人们放出来的。路由只需通过页面完成，使网站的建立和运行变得简单快捷。事实上，我们将在 5 分钟内完成。

## 安装

我们将使用 NPM 安装 Next.js 及其依赖项。

首先，我们将创建一个目录来保存我们的 Next.js 项目，并进入其中:

```
mkdir my-portfolio-site
cd my-portfolio-site 
```

然后我们用一个`package.json`文件来初始化它，并使用`y`标志来继续并跳过问题:`npm init -y`。

现在我们准备安装 Next.js:

```
npm install react react-dom next 
```

Next.js 对你如何组织你的项目并不固执己见，除了*一个例外*。所有实际的网页都需要放在一个`pages`文件夹中，所以让我们继续创建它:`mkdir pages`。

最后，让我们继续用运行脚本语言更新`package.json`来初始化我们的 Next.js 应用程序。打开`package.json`文件，在`scripts`下添加以下内容:

```
"dev": "next",
"build": "next build",
"start": "next start" 
```

太好了，我们现在已经安装了 Next.js，可以继续了！

## 创建第一个组件

请记住，Next.js 只是 Javascript，基于 React 之上，所以我们需要做的只是构建一些组件。路由是通过组件的名称来完成的，例如，`mysite.com/blog`，将来自于`pages`目录中的一个`blog.js`命名的文件。

在您喜欢的代码编辑器中打开项目，并在`pages`目录中创建一个名为`index.js`的文件。

让我们创建一个返回 HTML 的组件！

```
const Index = () => (
    <div>
        <h1>My Portfolio Site</h1>
        <p>Welcome to my portfolio! This is designed with Next.js!</p>
    </div>
)

export default Index 
```

现在，如果您从命令行运行`npm run dev`,并从您的 web 浏览器导航到 [http://localhost:3000](http://localhost:3000) ,您将找到提供的内容。

## 介绍链接 API

React 里的页面渲染我们不都很喜欢吗？我们可以使用 Next.js [链接 API](https://github.com/zeit/next.js#with-link) 用 Next.js 完成相同的客户端导航。假设我们的投资组合网站有一个联系页面，那么我们将有一个更新的索引组件，如下所示:

```
import Link from 'next/link';

const Index = () => (
    <div>
        <h1>My Portfolio Site</h1>
        <p>
            Welcome to my portfolio! This is designed with Next.js!
            Please{' '}
                <Link href="/contact">
                    <a>contact me</a>
                </Link>{' '}
            to get more information.
        </p>
    </div>
)

export default Index 
```

首先，我们从 Next 导入了链接 API 模块。JS，然后我们通过用`{' ' }`语法为它制作一个占位符，在我们的内容中内联使用它。`<Link>`组件是一个高阶组件，只支持几个参数，比如用于 URL 屏蔽的`href`(而`href`参数本身支持查询字符串之类的参数)和`as`。底层组件，在本例中是一个`<a>`标签，支持其他道具，如`style`和`onClick`。

## 制作更小的可复用组件

现在我们有了一个很好的开始，但是你能想象我们必须为我们创建的每一页重写标题吗？这就是为什么我们把我们的网站分成小的可重复使用的组件！

Next.js 对你应该如何做没有意见。但是，请记住，如果您将它们放在`pages`目录中，外部世界将可以直接访问它们。你希望有人直接访问你的页眉、导航栏和页脚吗？如果没有，那就把它们放在外面。继续创建一个`components`顶级目录:`mkdir components`和`touch header.js`来创建一个`header.js`文件。

在代码编辑器中打开`header.js`文件并创建一个 header 组件！

```
const Header = () => (
    <div>
        <h1>My Portfolio Site</h1>
    </div>
)

export default Header 
```

然后回到你的`index.js`文件，加入你的新标题:

```
import Link from 'next/link';
import Header from '../components/header';

const Index = () => (
    <div>
        <Header />
        <p>
            Welcome to my portfolio! This is designed with Next.js!
            Please{' '}
                <Link href="/contact">
                    <a>contact me</a>
                </Link>{' '}
            to get more information.
        </p>
    </div>
)

export default Index 
```

现在，新的`<Header />`组件所做的只是为我们保存了一个`<h1>`标签，但不需要太多的想象力就可以理解，在现实世界的网站中，会有比一个 HTML 标签更多的标签。

我们现在已经成功地安装了 Next.js，初始化了一个新项目，创建了组件，使用 Next.js 链接 API 链接到它们，并在我们的项目中重用了组件。这是一个很好的基础。

Next.js 还有很多功能，比如 Javascript 中的 CSS、自定义服务器(例如，使用 Express)、在页面之间传递状态等等。请查看[文档](https://github.com/zeit/next.js)进行更深入的探索，并享受 Next.js 的*下一个*步骤！