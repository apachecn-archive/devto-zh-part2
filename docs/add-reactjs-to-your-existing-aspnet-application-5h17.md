# 将 React.js 添加到您现有的 ASP.NET 应用程序中

> 原文：<https://dev.to/jonhilt/add-reactjs-to-your-existing-aspnet-application-5h17>

> 世上本无事，庸人自扰之。
> <cite>孔子</cite>

曾经有一段时间，如果您想让您的 web 应用程序变得更“响应性”一些，并且减少与后端的对话，您只需添加一个对 jQuery 的引用，就可以运行了。

然后 Angular 1 出现了，同样的交易也存在。

之后呢？事情变得有点复杂了。

Angular 2 给 table 带来了很多新功能，有了它，一艘船装载了更多的复杂性。

Typescript，服务器端预呈现，webpack...

如果你大部分时间都在使用 MVC/WebForms(或者实际上是桌面开发)，突然你会发现自己很难立足。

[![](img/b4ebdd79bc8b2077e5b6918ae0ac3440.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LQbk7Md_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/2018-09-14-add-react-to-your-existing-asp-net-project/2018-09-17-08-00-01.png)

那你怎么爬上。当你从底层开始的时候？

# 您可以将 React 添加到您现有的站点中

一种选择是像 2006 年(JQuery 的第一版)一样狂欢，然后回去添加简单的脚本引用。

将 React 添加到您现有的应用程序中是完全可能的，事实上您可以...

## 参考 react 脚本

在您现有的 web 应用程序中，您需要引用两个脚本...

```
<script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script> 
```

这些通常会放在你的 html `<head>`中。

在我的示例 ASP.NET 核心应用程序中，我将它放在`Views/Shared/Layout.cshtml`中。

```
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    @ViewData["Title"] - WebApplication1

    <!-- other code here -->

    <script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
</head> 
```

现在，如果您在 wwwroot 中的某个地方添加一个. js 文件，您就可以开始构建您的第一个 React 组件了！

这里是你好世界。

```
var e = React.createElement;

class HelloWorld extends React.Component {
    render() {
        return e(
            "div",
            null,
            "Hello World"
        );
    }
}

const containerElement = document.getElementById('content');
ReactDOM.render(e(HelloWorld), containerElement); 
```

如果你是第一次反应，这可能看起来有点陌生，但本质上我们正在创建一个会说“Hello World”的 div。

这个`HelloWorld`组件准备好了，我们需要渲染它。

最后两行找到页面上现有的`#content`元素，并将`HelloWorld`组件呈现到其中。

现在你可以参考这个。js 文件，测试页面，看看 Hello World 的辉煌。

对于这个例子，我将把它添加到应用程序中一个视图的底部。

```
<h1>This is an exciting page in my existing application</h1>

<div id="content" />

<script src="~/js/hello.js"></script> 
```

## 但这不像我在网上看到的反应？！

你说得对，不是。

如今，大多数关于 React 的文章都会有一个类似这样的 HelloWorld。

```
class HelloWorld extends React.Component {
    render() {
        return <div>Hello World</div>;
    }
}

const containerElement = document.getElementById('content');
ReactDOM.render(<HelloWorld />, containerElement); 
```

那个`<div></div>`标签有个东西**叫 JSX** 。

看起来很像 html，对吗？但事实并非如此。

它实际上是 javascript，将创建一个 html div 标签并将其添加到页面中。

事实上，它将创建我们前面看到的确切代码。

```
React.createElement(
    "div",
    null,
    "Hello World"
); 
```

对我们大多数人来说，有 JSX 比没有它更容易构建组件。

但是，将 JSX 转换成 javascript 不会自动发生，它需要一个额外的步骤。

如果您试图在页面上包含组件的 jsx 版本，您会得到一个错误。

[![](img/6e3c6abcc2cddbe31edc669f03a30728.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2ZQi9aNd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/2018-09-14-add-react-to-your-existing-asp-net-project/2018-09-14-11-35-46.png)

## 浏览器无法解释 jsx

JSX 不是有效的 javascript。因此，浏览器不知道如何处理这个问题。

休闲...

```
<div>Hello World</div>; 
```

到…里面...

```
React.createElement(
    "div",
    null,
    "Hello World"
); 
```

我们需要用一种叫做巴别塔的东西。

Babel 是一个 javascript 编译器。它的主要焦点是获取使用最新版本的 javascript 编写的“现代”javascript，并将其编译成可以在当前和旧版本的浏览器中运行的 javascript。

巴别塔也被用来把 JSX 变成 JS。

你可以在这里在线自己尝试[。试试粘贴这个...](https://babeljs.io/en/repl)

```
<div>Hello World</div>; 
```

...来看看最终的 javascript 是什么样的。

## 自动把你的 JSX 文件转换成 JS

很明显**你不会想要把每个 jsx 文件都粘贴到 Babel 网站**中来为你的网站获取 javascript 代码！

为了自动编译，你需要在你的机器上下载并运行 babel。

你需要安装最新版本的 node.js 才能运行，所以如果你还没有的话[可以下载一个](https://nodejs.org/en/)。

现在，在现有 web 应用程序的根目录下打开一个命令行，您需要输入几个命令来将 babel 引入到您的应用程序中。

`npm`是 Node 的包管理器，它提供了一种将 javascript 依赖项引入应用程序的便捷方式。

```
npm init -y
npm install babel-cli@6 babel-preset-react-app@3 
```

这些**依赖项只在将 JSX 编译成 js** 时需要。我们不会将任何节点包用作已部署网站的一部分。

我们只需要在开发期间运行 babel 来编译 js 文件。

我们之前在布局页面中包含的 react 脚本仍然需要，我们的应用程序将使用这些脚本来运行 React 组件，我们选择从现有的视图/页面中引用这些组件。

最后，我们可以运行一个方便的小命令来“关注”我们的 jsx 文件，并在它们发生变化时将它们编译成 javascript。

```
npx babel --watch src --out-dir wwwroot/dist --presets react-app/prod 
```

npx 是一个包运行工具，它使得调用包含在节点包中的可执行文件变得容易。

不要输入到`babel.exe`的完整路径(你可以在你的项目的`node_modules`中找到)，你可以输入`npx babel`来节省你的时间和精力！

在这个例子中，我告诉 babel 查看一个名为`src`的文件夹，并将编译后的输出保存到`wwwroot/dist`。

(我的示例项目是使用 ASP.NET 核心项目构建的，因此任何需要提供给浏览器的内容都需要放入`wwwroot`)。

[![](img/d31952a8a69df0da4fff712aaa742126.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VIfoDPbh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/2018-09-14-add-react-to-your-existing-asp-net-project/2018-09-15-22-16-07.png)

现在，当我对`src`中的任何文件进行更改时，编译后的版本将出现在`wwwroot/dist`中。

[![](img/9b0767f5c9bcbeee71af77b6a9a521b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vl2bkn_a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/2018-09-14-add-react-to-your-existing-asp-net-project/2018-09-14-12-36-00.png)

我可以在我现有的视图中引用这些(编译的)js 文件。

```
<h1>This is an exciting page in my existing application</h1>

<div id="content" />

<script src="~/dist/hello.js"></script> 
```

# 总结和后续步骤

您可以创建 React 组件，使用 JSX 编写它们，并将其包含在您现有应用程序的常规视图/页面中。

这种方法的一个明显的缺点是，每当您在处理应用程序时，都要记住运行`npx watch`命令。

在下一篇文章中，我们将探索自动化这一步骤的各种选项，并确保它作为构建/发布过程的一部分运行。

[**想先拿到这些文章？这里报名:-)**](https://jonhilton.net/devto/next-react-post)

*图片来源:jim.choate59 [花园果园 Mt Hood 7714 A](http://www.flickr.com/photos/137864562@N06/43781073315)via[photo pin](http://photopin.com)[(牌照)](https://creativecommons.org/licenses/by-nc-nd/2.0/)*