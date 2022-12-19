# 了解 React 和 Gutenberg 中的高阶分量

> 原文：<https://dev.to/kball/understanding-higher-order-components-in-react-and-gutenberg-1mii>

前端生态系统的发展非常迅速，React、Vue 和 Angular 等框架的兴起导致了应用程序架构的巨大变化，以及 JavaScript 开发人员需要学习的一整套新模式。

随着 Wordpress 在新的 Gutenberg 编辑器中拥抱 React，数百万开发者突然被引入这个世界，并争先恐后地赶上。

在本帖中，我们将分解 React 中极其常见的架构模式之一——高阶组件。

一个朋友最近请求帮助理解古腾堡增加的一个用于处理颜色的新工具，即`withColors`高阶组件。这里可以看到一个例子[，转载如下:](https://github.com/WordPress/gutenberg/pull/7906/files#diff-1505756e64576b043ae80578a6d22c3eR74) 

```
edit: withColors( 'backgroundColor', { textColor: 'color' } )(
    function( props ) {
        // Props added by withColors HOC.
        var backgroundColor = props.backgroundColor;
        var setBackgroundColor = props.setBackgroundColor;
        var textColor = props.textColor;
        var setTextColor = props.setTextColor;

        // Class computation
        var paragraphClasses = (
            ( backgroundColor.class || '' ) + '' + ( textColor.class || '' )
        ).trim();

        return el( Fragment, {},
            el( 'p', {
                className: paragraphClasses,
                style: {
                    backgroundColor: backgroundColor.value,
                    color: textColor.value,
                }
            },
                'Hello world'
            ),
            el( InspectorControls, {},
                el( PanelColor, {
                    colorValue: backgroundColor.value,
                    title: __( 'Background Color' ),
                    onChange: setBackgroundColor,
                } ),
                el( PanelColor, {
                    colorValue: textColor.value,
                    title: __( 'Text Color' ),
                    onChange: setTextColor,
                } )
            )
        );
    }
), 
```

Enter fullscreen mode Exit fullscreen mode

这感觉有点吓人——到底发生了什么？`withColors`函数实现了一种叫做[高阶组件](https://reactjs.org/docs/higher-order-components.html)的模式。让我们来分析一下这意味着什么:

### 高阶分量

高阶组件(HOC)是一个 React 概念，它是一种以组合为中心的封装共享逻辑的方式，因此您可以在许多组件上使用它。

如 React 文档中所定义:

> 高阶分量是接受一个分量并返回一个新分量的函数。

这在许多方面类似于[装饰器](https://en.wikipedia.org/wiki/Decorator_pattern)，本质上允许你在一个“组件包装器”中封装一些可重用的功能(例如获取和设置颜色的逻辑)或数据。

这个包装器是一个函数，它接受一个组件并返回该组件的“包装”版本，该版本将接收这些函数和数据作为`props`。

因此，不是每个组件都需要从“有色”组件访问颜色信息，或者导入它们需要调用的颜色“库”模块，而是导入`withColors`高阶组件(HOC)，用该组件“包装”它们的组件，现在它们的组件将接收与颜色有关的属性以及它专门定义的任何其他属性。

### 裸露的最小高阶分量

为了尽可能简单，让我们看一个最简单的 HOC。

一个最小的 HOC 就是一个包装函数的函数，例如

```
import secretKeyHOC from 'secret-key';
const BareComponent = function(props) {
  //do something
}
const myComponent = secretKeyHOC(BareComponent);
export default myComponent; 
```

Enter fullscreen mode Exit fullscreen mode

其中 secretKeyHOC 是在其他地方定义的，可能看起来像:

```
const secretKeyHOC = function(component) {
  return function(props) {
    return component({... props, secretKey: 'mySecretKey'});
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

本质上，HOC 只是为你的组件合并了一些新的道具——在这个简单的例子中，它合并了一个密钥。

### 带自变量的高阶分量

大多数高阶组件不像上面的密钥那样简单...它们封装了可重用的逻辑，但是通常需要以某种方式进行配置。

例如，我们的 secretKey 组件可能包含用于查找几个秘密密钥之一的逻辑，但是需要进行配置以知道哪个密钥提供给哪个组件。

为此，高阶组件通常被实现为功能的*链*。首先用配置参数调用一个函数，然后它返回另一个可用于包装组件的函数。

例如，如果我们可以配置我们的`secretKeyHOC`来选择 github 密钥或 gitlab 密钥，它可能看起来像:

```
const secretKeyHOC = function(keyType) {
  const keys = {gitlab: 'key1', github: 'key2' }
  const key = keys[keyType];
  return function(component) {
    return function(props) {
      return component({... props, secretKey: key});
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们会像这样调用组件:

```
import secretKeyHOC from 'secret-key';
const BareComponent = function(props) {
  //do something
}
const myComponent = secretKeyHOC('gitlab')(BareComponent);
export default myComponent; 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们调用传递配置参数的函数，然后用我们包装的组件调用返回的函数。

### 回以颜色

回顾 Gutenberg 的`withColors`,我们可以在文档中看到，HOC 接受如下参数:

> withColors HOC 可以接收多个参数。withColors HOC 的每个参数可以是字符串或对象。
> 
> 如果参数是一个对象，它应该只包含一个带值的键。
> 
> 关键字应该是设置了预定义颜色的属性的名称，例如:“textColor”。
> 
> 该值应该是使用该颜色的上下文，例如:“颜色”。

回头看看在示例代码中是如何调用的，我们看到:

```
edit: withColors( 'backgroundColor', { textColor: 'color' } )(
    function( props ) {
        // Props added by withColors HOC.
        var backgroundColor = props.backgroundColor;
        var setBackgroundColor = props.setBackgroundColor;
        var textColor = props.textColor;
        var setTextColor = props.setTextColor;

        // some more stuff we'll ignore
    }
), 
```

Enter fullscreen mode Exit fullscreen mode

随着我们对高阶组件的理解，我们现在可以确切地看到这是在做什么。

首先，我们首先调用`withColors`，用几个参数指定我们需要`backgroundColor`和`textColor`。这将返回一个“包装函数”(HOC)，我们称之为传入我们的底层组件——将接收 props 的函数。

这确保了组件将总是接收 4 个道具:`backgroundColor`、`setBackgroundColor`、`textColor`和`setTextColor`，以及其父组件传入的道具。

这个“包装”的组件就是分配给`edit`的组件，它将用于编辑这个古腾堡块。

通过这种方式，Gutenberg 创建了一个干净的、孤立的、基于 props 的方法来修改和使用块内的局部颜色变化。

* * *

附:如果你对这类话题感兴趣，你或许应该[在 Twitter 上关注我](https://twitter.com/kbal11)或者加入我的邮件列表。我发出一份名为“[周五前端](https://zendev.com/friday-frontend.html)”的每周简讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T4】