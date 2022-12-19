# React 片段——包装 div 的结尾

> 原文：<https://dev.to/getstream_io/react-fragments--the-end-of-the-wrapper-div-pp2>

你好。我是肯。我是 GetStream.io 的一名开发人员，我们在那里创建个性化和可扩展的活动源。

在过去的几个月里，我一直在开发 Winds 2.0，这是一款开源的 RSS 阅读器和播客收听应用。它内置在 Node.js、Electron、Redux 和 React 中，截至本文撰写时，在 GitHub 上已有超过 5000 颗星。如果你想看看，去 https://getstream.io/winds/的[看看，或者去 https://github.com/GetStream/winds](https://getstream.io/winds/)[的](https://github.com/GetStream/winds)看看源代码。

在 Winds 中，我们有几个独特的前端情况需要使用 **React 片段**。React 片段是去年年底与 [React v16.2.0](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html) 一起发布的一个整洁的小功能——这是一个非常小的功能，但是当遇到一些非常具体的布局和样式情况时，仅仅知道它们的存在就可以让开发人员省去一个巨大的头痛。

## 好了，那么什么是 React 片段呢？

让我们回顾一下——我确信每个 React 开发人员都会在他们职业生涯的某个时候遇到这种情况(或者很快就会遇到):

```
class App extends React.Component {
    render() {
        return (
            <p>I would</p>
            <p>really like</p>
            <p>to render</p>
            <p>an array</p>
        );
    }
} 
```

在我看来很好！但是当我们把它通过 JSX 运输管道时…

```
 Failed to compile.

    ./src/App.js
    Syntax error: Adjacent JSX elements must be wrapped in an enclosing tag (6:8)

        4 |         return (<p>I would
        5 |         </p>
        6 |         <p>
          |         ^
        7 |             really like
        8 |         </p>
        9 |         <p> 
```

我们的 JSX 运输公司不喜欢这样🙁

> 这里的幕后发生了什么？ JSX 把我们所有的`<div>`和`<MyComponent>`都变成了`React.createElement()`调用——当 JSX transpiler 看到多个元素而不是单个元素时，它不知道用什么标签名来呈现。参见 React 文档中的 [React.createElement。)](https://reactjs.org/docs/react-api.html#createelement)

那么，我们该怎么办？每次我们需要将几个元素包装在一起时，Pinky 都会做同样的事情——用`<div>`包装它！就像 web 开发人员自`<div>`标签发明以来一直在做的那样，在 DOM 中再嵌套一个`<div>`(可能)不会有什么坏处。

```
class App extends React.Component {
    render() {
        return (
            <div>
                <p>I would</p>
                <p>really like</p>
                <p>to render</p>
                <p>an array</p>
            </div>
        );
    }
} 
```

好了，问题解决了。但事实证明，在单个 React 组件中，还有一种方法可以在页面上呈现这组内容——通过让`render`方法返回节点的*数组*。

```
class App extends React.Component {
    render() {
        return [
            <p>I would</p>,
            <p>really like</p>,
            <p>to render</p>,
            <p>an array</p>
        ];
    }
} 
```

如果我们返回一个由元素组成的*数组*，那么 React 将会转换并呈现它，而*没有包装器`<div>`* 。整洁！

> (还记得 JSX transpiler 是如何把`<div>`和`<MyComponent>`标签变成`React.createElement()`呼叫的吗？在这种情况下，transpiler 只是将这些调用组合成一个数组，并将它们作为子元素直接附加到父元素上，而不是一个未包含元素的数组，因为它找不到父元素。React v16.0.0 引入了这个特性。)

看，事情是这样的——丹·阿布拉莫夫和 React 团队中超级聪明的人看到这种情况后说:

“好的，所以你可以用两种不同的方式来呈现一个元素数组——要么在 DOM 中引入一个额外的`<div>`,要么使用一些笨拙的非 JSX 语法。这不利于开发人员的良好体验！”

所以，在 v16.2.0 中，他们[发布了对 React 片段](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html)的支持。

## 好了，**现在**什么是 React 片段？

下面是使用 React 片段的正确方法:

```
class App extends React.Component {
    render() {
        return (
            <React.Fragment>
                <p>I would</p>
                <p>really like</p>
                <p>to render</p>
                <p>an array</p>
            </React.Fragment>
        );
    }
} 
```

看看这个——我们写这个就像我们写`<div>` -wrapper 方法一样，但是它在功能上等同于 array-render 方法，只是用了一些漂亮的 JSX 语法。这将把这些段落元素呈现为一个数组，没有任何包装器`<div>`。

> 使用 React 片段还有另一种更简洁的语法:

```
class App extends React.Component {
    render() {
        return (
            <>
                <p>I would</p>
                <p>really like</p>
                <p>to render</p>
                <p>an array</p>
            </>
        );
    }
} 
```

> 根据你的工具、linters、构建管道等，这可能不适合你——发行说明说更广泛的支持正在进行中，但是我注意到`create-react-app`还不支持它。

## 好吧，但是我实际上什么时候使用它们呢？

**每当你需要扔掉包装纸的时候`<div>`。**

就是这样——如果你发现自己处于包装器`<div>`搞乱了 React 组件布局的情况，使用 React 片段。

所以，无论何时你想转动这个:

```
<div class="app">

    (...a bunch of other elements)

    <div> (my react component)
        <ComponentA></ComponentA>
        <ComponentB></ComponentB>
        <ComponentC></ComponentC>
    </div>

    (...a bunch more elements)

</div> 
```

变成这样:

```
<div class="app">

    (...a bunch of other elements)

    <ComponentA></ComponentA>
    <ComponentB></ComponentB>
    <ComponentC></ComponentC>

    (...a bunch more elements)

</div> 
```

## 示例:2×2 CSS 网格

在 Winds 2.0 中，我们大量使用了 CSS 网格。这是您在浏览播客或 RSS 源时会看到的一般布局之一:

[![](../Images/bfd5924041a30945694ff151ae805f79.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tpKZbNPS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/06/Screen-Shot-2018-06-15-at-10.49.13-AM.png%3Fw%3D300%26h%3D300)

如果你还不知道 CSS Grid，不要担心——这段 CSS 将让你快速了解事情是如何安排的:

```
.grid {
    display: grid;
    grid-template-areas: 
        'topnav header' 
        'subnav content';
    grid-gap: 1em;
} 
```

好吧，让我们打开这个:

*   在左上角，我们有我们的品牌/顶级导航位。
*   在左下角，我们有我们的“子导航”——这可以响应全局和局部状态的一些变化，如“活动”状态、选项卡或折叠导航。
*   在右侧，我们有我们想要在屏幕上显示的内容——在 Winds 中，这类似于 RSS 提要或文章标题，与文章列表或文章内容配对。**这两个部分将是一个单独的 React 组件—**这两个组件的属性会根据 URL 导航而改变。

所有这些组件与全局(redux + URL)和本地状态的交互略有不同。这个视图的结构是这样的，我们有三个 React 组件作为兄弟:

```
<div className="grid">
    <TopNav />
    <SubNav />
    <ContentComponent />
</div> 
```

但是，我们希望页面上实际呈现四个元素:

```
<div class="grid">
    <div class="topnav"  />
    <div class="subnav"  />
    <div class="header"  />
    <div class="content" />
</div> 
```

这...没有反应碎片会出现问题。假设我们正在创建包装 2×2 网格视图右半部分的组件，即`ContentComponent`:

```
class ContentComponent extends React.Component {
    render() {
        return (
            <div>
                <div className="header"/>
                <div className="content"/>
            </div>
        );
    }
} 
```

如果我们将呈现的内容包装在`<div>` s 中，那么我们将得到以下呈现的输出:

```
<div class="grid">
    <div class="topnav"  />
    <div class="subnav"  />
    <div>
        <div class="header"  />
        <div class="content" />
    </div>
</div> 
```

**这不行——**它会*完全*搞砸 CSS 网格。从浏览器的角度来看，网格中只有 3 个项目，其中一个没有设置`grid-area`样式。

还记得我们什么时候应该使用 React 片段吗？**每当我们想摆脱一个`<div>`。**如果我们用 React 片段来包装`ContentComponent`而不是`<div>`:

```
class ContentComponent extends React.Component {
    render() {
        return (
            <React.Fragment>
                <div className="header"/>
                <div className="content"/>
            </React.Fragment>
        );
    }
} 
```

然后，我们将看到一个非常不同的渲染输出:

```
<div class="grid">
    <div class="topnav"  />
    <div class="subnav"  />
    <div class="header"  />
    <div class="content" />
</div> 
```

这完全符合预期！没有呈现包装器`<div>`，我们的 4 个元素从 3 个 React 组件中呈现，浏览器看到所有元素都具有正确的`grid-area`样式，并且我们的 CSS 网格被正确呈现。

## 整齐！现在怎么办？

React 片段并不是 React 最近出现的最重要的特性，但是它们在一些特定的情况下非常有用。仅仅通过了解 React 片段的存在，你就可以省去很多 google-fu 带来的麻烦。它们让我们以 JSX 式的方式呈现元素/组件的数组，这可以解决很多表格、列表和 CSS 网格的布局和样式问题。

如果你有兴趣在一个生产应用程序中看到这些 React 片段，请在 https://github.com/GetStream/winds 查看 Winds 2.0 的源代码——或者，你可以在 https://getstream.io/winds/ T2 下载并使用 Winds 2.0。

下次再见——干杯！

*帖子[反应片段——包装 div](https://getstream.io/blog/react-fragments/) 的结尾最先出现在[流博客](https://getstream.io/blog)上。*