# 介绍 nanostyled:没有 CSS-in-JS 的 CSS-in-JS

> 原文：<https://dev.to/chrisfrank/introducing-nanostyled-2p6k>

Nanostyled 是一个用于构建风格化 React 组件的小型库(< 1 Kb 未混合)。它试图将 CSS-in-JS 库的灵活的、基于组件的 API 与普通 CSS 的极低开销结合起来:

|  | 低开销 | 灵活的基于组件的 API |
| --- | --- | --- |
| 普通 CSS | 981 号房 | -好的 |
| CSS-in-JS | -好的 | 981 号房 |
| 纳米风格 | 981 号房 | 981 号房 |

比如启发它的 CSS-in-JS 库💕to[styled-components](https://www.styled-components.com)-nano styled 允许你构建具有复杂默认样式的 UI 元素，然后通过 props 在整个应用中调整这些样式:

```
<Button>A nice-looking button</Button>
<Button color="blue">A nice-looking button that is blue.</Button> 
```

与 CSS-in-JS 库不同，nanostyled 不使用任何 CSS-in-JS。相反，它被设计成伴随一个**功能 CSS 框架**，像[超光速粒子](http://tachyons.io/)或[顺风](https://tailwindcss.com/)。Nanostyled 使功能性 CSS 不那么冗长，并且更容易提取到 props 控制的组件中。

查看 npm 上的 [nanostyled，了解安装和使用说明，或者继续阅读了解更多内容。](https://www.npmjs.com/package/nanostyled)

* * *

## 功能 CSS？

功能性 CSS 框架的基本前提是，您可以通过编写微小的 CSS 实用程序类来构建复杂的样式。

用超光速粒子设计的按钮在标记中可能看起来像这样:

```
<button class="bg-blue white br2 pa2">Button</button> 
```

这是一个蓝色背景、白色文本、圆角(`br2`)和所有边都有一些填充(`pa2`)的按钮。

> 神圣的地狱这是我见过的最糟糕的事情
> ——《顺风. css 的作者亚当·瓦森

是真的。函数式 CSS 是丑陋的，违背了几十年来的最佳实践:将内容与样式分离。

另一方面，使用函数式 CSS 进行样式设计可以很好地适应大型项目，增强视觉一致性，并且可以轻松地构建新的 UI 元素，而无需编写任何新的 CSS。《顺风》的创作者亚当·瓦森在这里优雅地为这种方式辩护。

Nanostyled 使功能性 CSS 更容易抽象成组件，而不会失去它的任何优势。

## 为什么在 React 中用功能 CSS 构建灵活的组件很难

为了减少使用函数式 CSS 的麻烦，您可以将长类字符串提取到自包含的 React 组件中:

```
const Button = ({ className = '', ...rest }) => (
  <button className={`bg-blue white br3 pa2 fw7 ${className}`} {...rest} />
) 
```

在这种情况下，问题是没有好的方法用不同的背景颜色来呈现我们的`<Button>`。即使它接受一个`className`道具，写`<Button className="bg-red" />` *也不一定会渲染一个红色按钮。*

马克斯·斯托伊伯最近的推特投票很好地说明了为什么:

> 你对 CSS 了解多少？给定这些类:
> `.red { color: red; }`
> `.blue { color: blue; }`
> 这些 div 会是什么颜色？
> `<div class="red blue">`
> `<div class="blue red">`

正确答案是两个 div 都是蓝色的，57%的受访者回答错误。

光看 HTML 是无法知道答案的。您需要查看 CSS，因为当两个冲突的 CSS 类具有相同的特性时，它们在标记中的顺序*是不相关的。哪个类胜出取决于样式表*中最后定义的*是哪个。*

所以要用功能性 CSS 构建一个健壮的`<Button>`,我们需要能够

1.  声明一些样式化它的普通 CSS 类
2.  公开一个方便的 API，用于用替代品替换一些股票类

这第二个要求对于避免像 Max 的投票中那样违反直觉的类冲突是关键的，这是 nanostyled 使之变得容易的事情。

## 用纳米风格和风格道具构建柔性组件

Nanostyled 的工作原理是将**样式属性**映射到您选择的功能性 CSS 框架的类名上。

**Style props** 可以随意命名，每个可以容纳任意数量的 CSS 类:

### 一个纳米样式的按钮

```
import nanostyled from 'nanostyled';
// This example uses CSS classes from Tachyons
import 'tachyons/css/tachyons.css';

// A Button with three style props:
const Button = nanostyled('button', {
  color: 'white',
  bg: 'bg-blue',
  base: 'fw7 br3 pa2 sans-serif f4 bn input-reset'
});

const App = () => (
  <div>
    <Button>Base Button</Button>
    <Button bg="bg-yellow">Yellow Button</Button>
  </div>
);

/* rendering <App /> produces this markup:
<div>
  <button class="white bg-blue fw7 br3 pa2 sans-serif f4 bn input-reset">Base Button</button>
  <button class="white bg-yellow fw7 br3 pa2 sans-serif f4 bn input-reset">Yellow Button</button>
</div>
*/ 
```

当一个`nanostyled(element)`渲染时，它使用它的样式属性并将它们合并成一个 HTML 类字符串，如上所述。

使用哪种风格的道具完全由你决定。上面的`<Button>`有一个 API，通过`color`和`bg`道具可以很容易地改变颜色或背景色的样式，但是如果不完全重写`base`道具就很难改变其他的样式。

### 更灵活的纳米按钮

通过使用更多的样式道具，我们可以制作出更灵活的按钮:

```
import nanostyled from 'nanostyled';
import 'tachyons/css/tachyons.css';

const FlexibleButton = nanostyled('button', {
  color: 'white', // white text
  bg: 'bg-blue', // blue background
  weight: 'fw7', // bold font
  radius: 'br3', // round corners
  padding: 'pa2', // some padding
  typeface: 'sans-serif', // sans-serif font
  fontSize: 'f4', // font size #4 in the Tachyons font scale
  base: 'bn input-reset', // remove border and appearance artifacts
}); 
```

渲染一个股票`<FlexibleButton />`将会产生与其相对简单的股票相同的标记。但是呈现替代样式要容易得多:

```
<FlexibleButton
  bg="bg-light-green"
  color="black"
  weight="fw9"
  radius="br4"
>
  Button with a green background, black text, heavier font, and rounder corners
</FlexibleButton> 
```

当你需要在你的风格道具中没有计划的变化时，你仍然可以使用`className`道具:

```
<FlexibleButton className="dim pointer">
  A button that dims on hover and sets the cursor to 'pointer'
</FlexibleButton> 
```

## 跨多个组件共享风格道具

如果你正在用 nanostyled 构建多组件 UI 包，我建议在你的所有组件中至少共享一些基本的风格道具。否则很难记住哪些组件支持，比如说一个`color`道具，哪些不支持。

我通常从这里开始:

```
import React from "react";
import ReactDOM from "react-dom";
import nanostyled from "nanostyled";
import "tachyons/css/tachyons.css";

// The keys in this styleProps will determine which style props
// our nanostyled elements will accept:
const styleProps = {
  bg: null,
  color: null,
  margin: null,
  padding: null,
  font: null,
  css: null
};

/* 
Why choose those keys, in particular? For everything except `css`, 
it's because the elements in the UI kit probably will have some default 
bg, color, margin, padding, or font we'll want to be able to easily override via props.

The `css` prop is an exception. I just like being able to use it instead of `className`.
*/

// Box will support all styleProps, but only use them when we explicitly pass values
const Box = nanostyled("div", styleProps);
/*
<Box>Hi!</Box>
renders <div>Hi!</div>

<Box color="red">Hi!</Box>
renders <div class="red">Hi!</div>
*/

// Button will also support all styleProps, and will use some of them by default
const Button = nanostyled("button", {
  ...styleProps,
  bg: "bg-blue",
  color: "white",
  padding: "pa2",
  font: "fw7",
  // I use a 'base' prop to declare essential component styles that I'm unlikely to override
  base: "input-reset br3 dim pointer bn"
});
/*
<Button>Hi!</Button>
renders
<button class="bg-blue white pa2 dim pointer bn input-reset>Hi!</button>
*/

// Heading uses styleProps, plus some extra props for fine-grained control over typography
const Heading = nanostyled("h1", {
  ...styleProps,
  size: "f1",
  weight: "fw7",
  tracking: "tracked-tight",
  leading: "lh-title"
});

// Putting them all together....
const App = () => (
  <Box padding="pa3" font="sans-serif">
    <Heading>Styling with Nanostyled</Heading>
    <Heading tracking={null} tag="h2" size="f3" weight="fw6">
      A brief overview
    </Heading>
    <Heading tag="h3" weight="fw4" size="f5" tracking={null} css="bt pv3 b--light-gray">
      Here are some buttons:
    </Heading>
    <Button>Base Button</Button>
    <Button css="w-100 mv3" padding="pa3" bg="bg-green">
      Wide Green Padded Button
    </Button>
    <Box css="flex">
      <Button css="w-50" margin="mr2" bg="bg-gold">
        50% Wide, Gold
      </Button>
      <Button css="w-50" margin="ml2" bg="bg-red">
        50% wide, Red
      </Button>
    </Box>
  </Box>
);

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

这个完整的例子在 CodeSandbox 上有[供你试验。](https://codesandbox.io/s/3r8l4rr8p1)

Nanostyled 在 npm 上[可用，你可以在](https://www.npmjs.com/package/nanostyled) [GitHub](https://www.github.com/chrisfrank/nanostyled) 上贡献给库。