# Stencil.js 组件库——您的第一个组件

> 原文：<https://dev.to/johnbwoodruff/component-libraries-with-stenciljs---your-first-component-3b7p>

*这是关于使用 Stencil.js 创建 web 组件库的系列文章的第三篇——请看第一篇文章*

到目前为止，我们已经做了很多设置，现在让我们创建我们的第一个组件。这是任何组件库的基础:按钮组件。让我们把`my-component`文件夹重命名为`button`，里面的所有文件把`my-component`替换为`button`。我们现在准备构建我们的按钮。

## 组件装饰器

我们要做的第一件事是改变`button.tsx`的内容。我们将把`@Component`装饰器中的`tag`改为我们实际的标记名，`styleUrl`指向我们新重命名的 SCSS 文件，在我的例子中是:

```
@Component({
  tag: 'mtn-button',
  styleUrl: 'button.scss',
  shadow: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

请注意`shadow`属性。这是在声明我们将为这个组件使用[影子 DOM](https://developers.google.com/web/fundamentals/web-components/shadowdom) 。这有很多好处，包括隔离的 DOM 和限定范围的 CSS 等等。我们肯定希望利用这一点，因为它是 web 组件的关键部分之一。

## 渲染方法/JSX

接下来我们要改变这个类来呈现一个只有一个道具的普通按钮。

```
export class Button {
  render() {
    return (
      <button>
        <slot />
      </button>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

查看 render 方法，您会注意到我们没有编写 HTML。我们正在使用一个叫做 [JSX](https://reactjs.org/docs/introducing-jsx.html) 或 JavaScript XML 的 JavaScript 语法扩展。注意，对于 Stencil，我们实际上使用的是 TSX，一个能够使用 TypeScript 编写 JSX 的文件。让我们更改标记来呈现一个标准的 HTML 按钮。我们还在按钮内放置了一个[槽](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot)。这是浏览器 web 组件 API 套件的一部分；它允许我们用组件的消费者定义的标记来填充那个槽。在我们的例子中，消费这个组件将如下所示:

```
<mtn-button>Click Me!</mtn-button> 
```

Enter fullscreen mode Exit fullscreen mode

文本“点击我！”向下投影到组件内的插槽。组件中的标记如下:

```
<button>
  <slot>Click Me!</slot>
</button> 
```

Enter fullscreen mode Exit fullscreen mode

这个按钮组件目前非常简单。对于更复杂的组件，我们显然会有更多的标记，并且偶尔会使用多个命名槽来将多个标记向下投射到组件。现在，我们将继续使用这个按钮。

## 组件道具

如果您熟悉 React，您现在应该已经注意到这些组件的外观和功能与 React 组件非常相似。沿着这些思路，我们现在要定义一个道具。按钮的一个重要功能是禁用该按钮。让我们使用`@Prop()`装饰器来定义一个`disabled`属性。我们还将把禁用的属性传递给实际的按钮。我们将这样更新我们的类:

```
export class Button {
  @Prop({ reflectToAttr: true })
  disabled: boolean;

  render() {
    return (
      <button disabled={this.disabled}>
        <slot />
      </button>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在名为`disabled`的类上定义了一个属性，并为其添加了一个`@Prop()`装饰器。我们还传入了一个带有我们定义为真的`reflectToAttr`键的对象。根据[模板道具文档](https://stenciljs.com/docs/properties#reflect-properties-values-to-attributes)，使用它可以确保我们禁用的道具与 HTML 属性保持同步。在这种情况下，我们肯定希望这样，因为我们使用了一个禁用的属性。

我们还将`disabled={this.disabled}`添加到组件中的按钮。这将根据是否定义了 disabled 属性，有条件地应用 disabled 属性。

## 造型我们的按钮

目前这是一个丑陋的 HTML 按钮。我们显然要改变这种状况。我鼓励你为你的组件想出你自己的风格指南和设计，但是当然欢迎你复制我正在做的。首先，我将在`src/styles/variables.scss`为我的颜色变量创建一个文件。如果我必须改变颜色，我希望它适用于所有的颜色，而不是一个一个地改变，所以我使用 SASS 变量来做到这一点。我将从我的字体开始，一些基本的颜色，和我的原色阴影，这是我现在关注的。

```
// Font Family
$font-family: 'Open Sans', 'Helvetica Neue', Arial, Helvetica, sans-serif;

// Basic Colors
$white: #ffffff;
$black: #000000;

// Brand Colors
$blue-steel: #4571c4;
$blue-steel-dark: #315db0; 
```

Enter fullscreen mode Exit fullscreen mode

现在我已经建立了我的字体和颜色，我要给我的按钮一些经典的样式。

```
@import '../../styles/variables.scss';

:host {
  box-sizing: border-box;
}

:host([disabled]) {
  pointer-events: none;
}

button {
  font-family: $font-family;
  cursor: pointer;
  border: none;
  background-color: $blue-steel;
  color: $white;
  line-height: 20px;
  font-size: 14px;
  padding: 4px 12px;
  border-radius: 3px;

  &:hover {
    background-color: $blue-steel-dark;
  }
  &:active {
    background-color: darken($blue-steel-dark, 5%);
  }
  &:disabled {
    opacity: 0.4;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我导入变量以供使用。然后我使用了`:host`选择器，它允许我们选择影子 DOM 的影子主机，用于一些不同的事情。首先，我将`box-sizing: border-box`设置为主机元素。我个人更喜欢使用边框大小，而不是考虑高度和宽度的边框。对我个人来说更有意义。接下来，我将使用主机选择器来*仅*选择应用了`disabled`属性的主机。在这种情况下，我将对元素应用`pointer-events: none`。这使得当按钮被禁用时，在元素上设置 click 处理程序不会触发。

接下来我要设计我的按钮本身。你会注意到并可能担心我对`button`元素的全局样式化。这不是问题，因为我们使用的是影子 DOM，这是它最大的好处之一。您的所有样式都在组件的影子 DOM 范围内，外部的样式不能穿透它，内部的样式也不能搞乱它之外的任何东西。挺牛逼的。

其余的风格都很简单。我有按钮样式，悬停时背景颜色变暗，点击时变得更暗。当它被禁用时，我应用`opacity: 0.4`让它看起来被禁用了。

如果你还没有，确保你运行`npm start`来启动你的开发服务器，它会自动在你的浏览器中打开。继续将您的按钮添加到您的`index.html`页面，这样您就可以测试您的组件。我添加了以下标记:

```
<mtn-button>Button</mtn-button>
<mtn-button disabled>Button</mtn-button> 
```

Enter fullscreen mode Exit fullscreen mode

当您的页面自动刷新时，您应该会看到您漂亮的新按钮组件处于禁用和非禁用状态！

[![new buttons](../Images/1265d90ce9e8afc9094241dccac6cd36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--atyfP9iz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mq6160q4l4v5cu7b0ode.png)

## 下一步

你有它！一个经典的按钮组件，看起来很棒，有基本的功能。很明显，一个按钮(和大多数组件)要实现更多的功能，但是我们将在下一篇文章中完成。那里见！

*单纯想看最终结果回购？来看看[这里](https://github.com/johnbwoodruff/mountain-ui)T3】*