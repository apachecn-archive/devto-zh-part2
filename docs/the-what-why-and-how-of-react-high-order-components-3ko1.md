# 反应的内容、原因和方式(高阶组件)

> 原文：<https://dev.to/mangel0111/the-what-why-and-how-of-react-high-order-components-3ko1>

你好，在这一系列文章中，我将谈论 React 概念，尝试解释这些概念的确切含义，为什么你可能需要它以及如何使用它，在这篇文章中，我们将谈论高阶组件(HOC)。简而言之，它是一种创建逻辑的模式，可以很容易地被其他组件重用，您将会看到为什么对于 hoc，您需要学习它来理解您总是需要它。

**什么是高阶分量？**
如果我们去看看 React 文档，上面是这样写的:*是一个函数，它接受一个组件并返回一个新组件。*。根据这个定义你可能会想。为什么不创建一个类并扩展它呢？我们可以拥有可以在父类中重用的核心逻辑，并为他的所有子类进行扩展，是的，但是使用 HOC 的优点是，这种模式的对象是返回一个组件，一个简单的事务，我给你我的组件，HOC 返回一个改进的新组件，其中包含我需要的逻辑。

因此，我们可以说，一个特设是一个函数，它接收一系列数据、属性和一个组件，包括他的逻辑、上下文或其他内容，并返回一个包含该逻辑的新组件，通过这种模式，您还可以确保您需要向您的组件提供的内容集中在一个地方，并将始终以相同的方式使用，如下例:

```
import React, { Component } from ’react’;

//Create your child component
const  ChildComponent = (props) => (<div> Hello Folks<div>);

// Create your HOC 
const higherOrderComponent = (ChildComponent) => {
    return class extends Component {
    render() {
        return (<ChildComponent props={}/>);
        }
    }
}

// Then You send your ChildComponent and receive a new one with some new props provided for the HOC
const newEnhancedComponent = higherOrderComponent(ChildComponent); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，你可以在你的 HOC 中发送或提供的内容是无限的。

**我为什么要用这个？**
当你在构建你的组件时，你应该总是试图用尽可能少的责任创建尽可能最简单的组件，但是有时你会发现你自己有一个很大的组件，有很多东西，最糟糕的是，有很多你认为是多余的逻辑。当你发现你需要应用一些模式来使你的代码更具可伸缩性和可重用性时，第一个原因是一个大的组件做了很多事情。

第二个也是更重要的原因是，当你看到许多组件(有时不止一个)将使用一些基本逻辑时。这两点将是你在项目中尝试应用这种模式的完美理由。

我应该如何使用？

在特设你可以添加，编辑，甚至删除一些道具，你会用在你的孩子或增强组件。您可以包含一个上下文，甚至可以发出一个调用，订阅一个服务，解析一个承诺，并在特设中处理响应，而不是在每个*componenddimount*中进行调度，并有许多重复的代码。我会给你一个最常见的例子列表，告诉你我们可以在哪里以及如何使用这个模式来处理问题和现实生活中的场景。

*   当您使用 *react-redux* 的“连接”时，您已经使用了一个。如果你在代码中使用 redux 来处理状态和调度动作，你已经在使用一个特设，这个*连接*是一个特设，它接收你的*子组件*和你的状态映射器，并返回给你一个*连接组件*。connect 不仅给你分派任务，还会给你道具，如果有变化会通知你。

```
 export const mapStateToProps = (state) => ({
   information: state.information
});

export default connect(mapStateToProps)(ChildComponent); 
```

Enter fullscreen mode Exit fullscreen mode

*   当您需要在子组件中包含 UI 组件或行为时。假设您有一个组件，您需要包含一个警告(这可以是一个模态，改变颜色，打开一个隐藏的文本或任何东西)。你需要你的所有组件都包含这个额外的 UI。你可以有一个特设机构将这两件事联系在一起，但每一件事都是独立的，各负其责，就像这样:

```
import React, { Component, Fragment } from ’react’;

//Create your child components
const  HelloComponent = (props) => (<div> Hello Folks<div>);
const  GoodBayComponent = (props) => (<div> And Goodbay<div>);
const  AlertComponent = (props) => (<div> I’m an alert <div>);

// Create your HOC 
const componentWithAlert = (ChildComponent) => {
    return class extends Component {
    render() {
        return (
        <Fragment>
            <AlertComponent  />
                        <ChildComponent props={}/>
                </Fragment>
            );
        }
    }
}

const HelloWithAlert = componentWithAlert(<HelloComponent />);
const GoodbayWithAlert = componentWithAlert(<GoodbayComponent />); 
```

Enter fullscreen mode Exit fullscreen mode

正如你在这里看到的，我们在一个中有两个独立的组件，你也可以看到我使用了*片段*而不是普通的 *div* ，但是片段不包括任何额外的标记或元素，让你分组没有问题，我更喜欢这样。

*   当你有了上下文。假设我们有一个重要的信息，比如带有所有品牌的主题、i18n 资源或任何类型的信息，您需要向所有组件提供这些信息。非常重要的一点是，尽量保持你的信息来源单一，每个部分都不应该是根据语言或主题决定使用哪种颜色或翻译的责任，要处理这种情况，你需要一个上下文

React 中的上下文是一个 API，它允许您通过组件树传递数据，而不必在每一层手动传递属性，当我们需要处理这类问题时，这是非常好和有用的。上下文需要一个提供者和一个消费者，提供者会有相关的信息，你需要所有的孩子的组件包装在消费者里面，所以，这是一个很好的例子，你需要一个在组件中包含主题消费者上下文逻辑的组件，不管是哪个组件，所以你不需要每次使用组件时都调用 ThemeContext。

```
import React, { Component } from ’react’;

const ThemeContext = React.createContext({});

class ThemeProvider extends Component {
    render() {
        const {theme } = this.props;
        return (
            <ThemeContext.Provider
                value={theme}
            >
                {Children.only(this.props.children)}
            </ThemeContext.Provider>
        );
    }
}

const withTheme = (ChildComponent) => {
    return class extends Component {
    render() {
        return (
        <ThemeContext.Consumer>
                    {
                        theme =>
                            <ChildComponent
                                theme={theme}
                                {props}
                            />
                    }
                </ThemeContext.Consumer>
            );
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我将在下一篇文章中更多地谈论上下文，因为今天真正重要的是特设。现在你有了一个例子，说明了特设委员会如何帮助你解决不同的问题。我希望这个博客能帮助你更好地理解这个概念，并且你的代码会越来越好。希望你喜欢。下期帖子再见！

# 密令信托

**接下来:** [该什么，为什么以及如何反应(路由器)](https://dev.to/mangel0111/the-what-why-and-how-of-react-routers-41b)