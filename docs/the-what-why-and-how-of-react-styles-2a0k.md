# 反应的内容、原因和方式(风格)

> 原文：<https://dev.to/mangel0111/the-what-why-and-how-of-react-styles-2a0k>

你好，这是我关于 React 生态系统的第三篇文章，今天是任何 FE 发展中最重要的部分之一的转折点。每次我不得不用甚至五岁的男孩或女孩都能理解的语言解释前端开发人员的工作时，我总是开始说一个网站就像一个人的身体，`HTML`可能是骨骼，`javascript`可能是大脑，`CSS`可能是外表。所以你需要一个健康的骨骼系统，精神上也要稳定，显然，你需要看起来不错，听起来自恋，但最终，所有的前端都是小自恋者。

在 2018 年，你可以用 javascript 做一些令人惊讶的事情，自从 HTML 5 成为 HTML 的最终标准以来，我们已经有将近 10 年的时间了，很难找到有人试图使用不同于`CSS`的东西来实现网站风格的替代物，风格是丑陋网站和现代网站之间的区别。在这篇文章中，我们不会谈论用户界面/UX、命名约定或当你试图给你的页面赋予样式时的最佳实践(或者至少不会详细讨论)，今天我们将重点关注如何在我们的 React 应用程序中包含这个大的样式世界。

# React 里是什么风格？

React 允许您以多种方式将 CSS 包含在组件中，因此当您尝试对 React 组件进行样式化时，您可以有两种方法，每种方法都完全不同，基本上您可以创建组件，组件可以知道或处理自己的样式，或者完全不知道它们看起来会是什么样子，我不是在说`Components vs Containers`斗争，这种方法是关于我们如何将样式包含在 React 组件中的方法，基于我们有这两种方法:

*   `SelfStyled Components`(酷的那种):组件`should include his own definition of whom they should look`，用这个组件的人不应该关心定义一个样式，你也不需要有 CSS 的大模块，只需要把样式(我们稍后将讨论如何制作)直接包含在组件中，让每个人决定。

*   这些组件`don’t know how they should look`，他们不在乎，他们会使用一个引用来指示它是什么组件，并且相信不管组件在哪里使用，一些全局样式会使它看起来基于那个引用，那可以是一个类名，但是你不用写任何一行 CSS。

*注意*:在这个上下文中，纯组件术语与 [React API PureComponent](https://reactjs.org/docs/react-api.html#reactpurecomponent) 不同。

两种方法都可以，只是看你需要什么。第一种组件的想法是，任何人都不应该修改他的外观，他们拥有他们应该如何看，无论谁将使用它或在哪里看起来总是一样的，对于第二种，他们只是不在乎。

显然，你可以使用这两种方法，你可以为特定的组件定制组件，比如特定的输入、日期选择器或布局；也有一种全球风格，他们关心大局，默认重置一些值，甚至选择字体，*重要的*:如果你决定混合，非常重要的是要仔细区分你认为的全球，否则你将在一场战争中无法战胜 CSS 的特殊性。如果你打算使用 CSS，试着遵循一个命名惯例，line BEM 或者任何你喜欢的东西。

*注意:*如果你决定使用自封的组件，不要试图在你的全局 CSS 中覆盖特定的样式，因为你会发现自己编写的 CSS `solutions`将以一个大的选择器结束，并且到处使用`!important`。
*注 2:* 如果你决定有一个全局风格或者任何 CSS 框架，比如 bootstrap，在扩展风格的时候尝试遵循最佳实践，sass 和 less 有关于如何保持你的 CSS 可维护性的优秀文档。

# 为什么要在 React 中关心风格？

React 是一个强大的库，其中我们可以有一个组件，它将 HTML 和 js 混合到一个类或函数中。该类或函数处理呈现状态、如何初始化组件、如何在呈现、更新或消除前后表现。但不仅仅是业务逻辑，我们需要创建组件，这些组件应该看起来符合它们假装要做的事情。

所以你需要有一种方法在你的 react 组件中包含样式，真正的问题是我应该使用一个自封的组件还是一个纯组件？根据您真正需要，您可以两者都用。我会举一些例子来说明你应该在什么地方使用这种或那种方法。

这些是使用纯组件的最佳场景:
*如果你使用 bootstrap、material UI 或你自己的 CSS 框架，并且你想在你的 react 应用中使用它们，我想使用在我们的前端*中构建(或购买)的 CCS 框架。您需要使用纯组件方法。
*我想保持我所有 FE 之间的一致性*如果你不想为每个项目和可用性定义 CSS 框架，也有可能你已经在其他框架中构建了多个 FE。

这些是自封组件的原因:
*我不想有一个大的 CSS 整体框架*，有时大的整体并不是最好的，一个项目对另一个项目可能有特殊的差异或要求，可以将我们的 CSS 整体转换成一个可怕的意大利面条代码。
*我想使用最新的 css* ，如果你想为你的网站使用最新的功能，你应该使用这个，因为你可以确保在缩小的范围内实现你想要的，否则，在你的项目中完美工作的新 css 功能会在其他项目中产生无限的错误。

# 弈风格如何反应

关于什么和为什么我们已经谈得够多了，现在让我们开始行动，我将给出如何制作一个纯组件和一个自封组件的简单例子，让我们看看:

*纯组件*
纯组件，就像我们之前说的，没有他风格的定义，只有一个引用来表示我们的组件应该是什么样子，我们可以用类名来表示，在 React 中我们不能用`class`我们应该用`className`，在我们组件的属性中我们可以引用任何一种用于我们应用程序的 CSS 类，就像这样:

```
import { Component } from 'react';

class PureComponent extends Component {
    render() {
    return (
            <div className="container">
                <div className="child" />
            </div>
        );
    }
}

export default (PureComponent); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到我们有 2 个引用，一个类名叫做`container`和`child`，对于组件的上下文，他们不关心这意味着什么，那个类的规则定义完全独立于组件本身。

但是，这种风格是在哪里定义的？这一切都取决于我们的架构，以及在哪里运行我们的 React 应用程序。我会给你一些其他博客的链接，在那里你可以找到关于如何实现的具体信息，基于你可能有的:
*bootstrap*，允许使用像`col-lg-x`或任何其他引导类的类，你应该阅读这个[链接](https://react-bootstrap.github.io/getting-started/introduction/)
*材质 UI* ，使用[材质 UI 组件](https://material-ui.com/getting-started/usage/)
*Css 加载器来包含我自己的 CSS* ，如果你想包含你自己的 Css，你应该[阅读这个](https://javascriptplayground.com/css-modules-webpack-react/)

在大多数情况下，要使用 CSS 库，你需要做的是包含一个链接引用，然后你应该能够调用正确的类来使用它。

*自封组件*
我们现在要谈的是另一种做样式的方法，有时我们需要的是为我们的组件定义特定的样式，我们不想用任何种类的类名或引用来给我们的组件赋予样式，我们需要的是交付一个具有他的逻辑和样式的组件，我们可以用几种方法来做:

*用 json 包含样式*
我们可以定义一个 JSON，它包含样式然后直接注入到组件中，就像这样:

```
import { Component } from 'react';

const container = {
    margin: '30px',
    padding: '5px 10px',
    textAlign: 'center'
};

class SelfStyledComponent extends Component {
    render() {
        return (
            <div styles={container}>
                <div style={{color: blue}}/>
            </div>
        );
    }
}

export default (SelfStyledComponent); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要记住，对于像`text-align`这样的属性，我们应该将它传递给 camel case 并使用`textAlign`来代替。

*风格化组件*
我们有一个库，允许你以一种更友好的方式创建一个自封的组件，而不仅仅是注入 JSONs，我们可以使用[风格化组件](https://www.styled-components.com/)，用这个我们可以包含 SCSS 的小部分(或最大部分取决于你)，直接赋值给 React 组件，就像这样:

```
import React from 'react';
import styled from 'styled-components';

const Container= styled.div`
    margin: 40px 20px 15px;
    display: flex;
    margin-left: auto;

    div {
        padding: 10px;

        &::after {
            backgroung: gray;
        }
    }
`;

const Child = styled.div`
    color: ‘blue’
`;

class SelfStyledComponent extends Component {
    render() {
        return (
                <Container>
                    <Child />
                </Container>
        );
    }
}

export default (SelfStyledComponent); 
```

Enter fullscreen mode Exit fullscreen mode

使用样式组件，你可以利用 CSS 和 SCSS 的所有最新功能创建自定义组件，你也可以使用变量处理组件的样式，这些变量在你处理主题时非常有用。_

*编辑 2018 年 9 月:*
Styled Component 并不是唯一一个允许你创建自定风格组件的库，评论中提到了这一点，还有很多 repos 会给你带来另一种选择来做同样的事情。你可以查看米歇尔·贝托里写的这个 [githup repo](https://github.com/MicheleBertoli/css-in-js) ，在那里你会找到一个详细的制作 CSS-in-JS 的库列表，以获得更多信息。

正如你所看到的，我们可以有几种方法来做同样的事情，最佳的选择取决于你，你的技术人员和你期望建立什么。每种方法都有很多好处，比如性能、可伸缩性、可重用性等等。希望这能帮助你理解在 React 中开发时如何处理风格。

# 密令信托