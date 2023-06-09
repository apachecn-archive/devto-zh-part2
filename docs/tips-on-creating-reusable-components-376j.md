# 创建可重用组件的技巧

> 原文：<https://dev.to/ganderzz/tips-on-creating-reusable-components-376j>

*原创发布:[dylanpaulus.com](https://www.dylanpaulus.com/react/2017/09/08/tips-on-creating-reusable-components.1/)T3】*

### 简介

React 的一个巨大卖点是它使用了可组合、可重用的组件。一切都是基于`V = F(d)`的思想构建的，或者说视图/UI 是由一些作用于数据/状态的函数创建的。我们如何创建更高级的用户界面？嗯，给聚会增加更多功能就行了(比如`V = G(F(E(S(d))))`)。真可爱，这有什么关系？将我们的组件视为功能，即使我们使用*类*，也会帮助我们创建更多可重用的组件。它甚至帮助我们编写超级有用的效用函数，称为[高阶分量](https://www.dylanpaulus.com/react/2017/08/17/higher-order-components/)。

我们将通过一直使用一个示例组件来研究改进组件的可重用性和组合的方法。在本文中，我们将使用一个向按钮添加图标的组件。

例如，

```
class IconButton extends React.Component {
    render() {
        <Button onClick={this.props.onClick}>
            <Icon />
            {"  "}
            {this.props.text}
        </Button>
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

和消费按钮，

```
<IconButton
    text="Click Me!"
    onClick={() => alert("Click!")}
/> 
```

Enter fullscreen mode Exit fullscreen mode

不错..易于使用，而且切中要害。但是，我觉得这样会更好。让我们浏览一些关于使这个组件更加可重用的建议。

### 保持简单，杰克

继续将组件视为功能的话题，保持组件简单。做太多事情的组件是危险的。它们很容易破碎，维护起来也很痛苦。相反，应该将功能分解成独立的组件，然后将这些组件组合起来，使之具有功能性。这使得维护特性和在需求变化时交换功能变得非常容易。

以 IconButton 为例，我们真的不需要 IconButton 组件。如果明天我们需要一个图标输入按钮呢？我们不仅要维护两个独立但非常相似的组件！我们可以通过使用合成来解决这个问题。我们真正想要的是给组件“添加图标”的东西。让我们制作一个名为 IconAdder 的新组件。

```
class IconAdder extends React.Component {
    render() {
        return (
            <div>
                <Icon />
                {"  "}
                {this.props.component}
            </div>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

消费它

```
<IconAdder
    component={
        <Button onClick={() => alert("Click!")}>
            Click Me!
        </Button>
    }
/> 
```

Enter fullscreen mode Exit fullscreen mode

IconAdder 允许我们将任何 ol '组件放入其中，它会给它添加一个图标。很好。

### 你好，孩子们！

我经常遇到的一件事是组件属性的过度使用。组件几乎不应该被用作属性。它最终使组件难以使用，最终，我们在与 React 作战。在 React 中，每个组件都有一个名为 children ( `props.children`)的属性。这表示，无论我们在这个组件的标签之间传递什么，我们都将在这里插入。让我们看看 IconAdder 组件中的一个例子`props.children`。

```
class IconAdder extends React.Component {
    render() {
        return (
            <div>
                <Icon />
                {"  "}
                {this.props.children}
            </div>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

消费它

```
<IconAdder>
    <Button onClick={() => alert("Click!")}>
        Click Me!
    </Button>
</IconAdder> 
```

Enter fullscreen mode Exit fullscreen mode

`<Button>`组件将被插入到 IconAdder 的`{this.props.children}`中！好多了！

### 注入 HTML 类型

我已经在这里写了一篇关于这个主题的文章，所以我会很简短。

作为一个使用组件的人，我希望我的代码受到尽可能少的污染。在这种情况下，污染可以是任何东西:事件处理程序、代码大小，甚至 HTML 元素。在我们的 IconAdder 组件中，一个父`<div>`包装器被添加到使用它的每个组件中。如果我们能去掉 div，让它成为我们的子组件，那就太好了。良好的...我们很幸运。我们可以指定一个标签属性，然后使用该属性。这让最终用户可以控制他们的 DOM 结构。

```
class IconAdder extends React.Component {
    render() {
        const { tag, onClick, children } = this.props; 
        const Tag = tag;

        return (
            <Tag onClick={onClick}>
                <Icon />
                {"  "}
                {children}
            </Tag>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

消费它

```
<IconAdder
    tag={Button}
    onClick={() => alert("Click!")}
>
    Click Me!
</IconAdder> 
```

Enter fullscreen mode Exit fullscreen mode

使用“标签”属性的另一个好处是，假设我们想要支持 React Router 的`<Link>`组件，但是我们也需要支持经典的 HTML `<a>`标签。我们可以很容易地交换这两个只是改变'标签'道具！

### 允许分机

没有什么比得到一个全新的组件更能磨坏我的齿轮了，但是意识到我想让字体变得加粗。所以，我给组件添加了一个快速的`style={ { fontWeight: 800 } }`，刷新，没有任何变化。(警告:将要显示的内容需要一个 transpiler - [Babel 插件](https://babeljs.io/docs/plugins/transform-object-rest-spread/))。

我们应该允许我们的组件具有合理的可编辑性，同时保护我们需要的东西。这可以由[展开操作员](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Rest_in_Object_Destructuring)来完成。

现在，我们的 IconAdder 组件不接受样式、类名或标题属性。哦，对了，如果将来我们需要在标签上使用 IconAdder 怎么办？我们也需要支持 href。让我们使用 rest 操作符来支持所有这些属性！

```
class IconAdder extends React.Component {
    render() {
        const { tag, onClick, children, ...rest } = this.props; 
        const Tag = tag;

        return (
            <Tag onClick={onClick} {...rest}>
                <Icon />
                {"  "}
                {children}
            </Tag>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

消费它

```
<IconAdder
    tag={Button}
    onClick={() => alert("Click!")}
    style={ { fontWeight: "800" } }
    title="A button for clicking"
>
    Click Me!
</IconAdder> 
```

Enter fullscreen mode Exit fullscreen mode

`...rest`将获取我们在析构(`const { a, b, c} = this.props;`)中没有去掉的任何属性，并把它赋给一个名为`rest`的变量。然后，我们通过将`rest`对象析构为单独的道具，将所有这些属性应用于我们的`<Tag>`组件。正如 IconAdder 的消耗所示，我们现在可以添加我们想要的任何其他属性！

### 结论

这是我在创建组件时使用的一些技巧，以使它们更容易使用和扩展。总之，把每一个组件都看作一个函数。它会消耗一些输入，并产生一个输出。孩子的财产是你的朋友，当它有意义的时候使用它！动态注入组件类型可以将多个相似的组件变成一个高度可重用的组件。最后，允许组件接收额外的属性，并尽可能让属性/样式覆盖基本实现。