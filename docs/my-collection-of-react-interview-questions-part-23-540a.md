# 我收集的 React 面试问题(第二部分)

> 原文：<https://dev.to/migueloop/my-collection-of-react-interview-questions-part-23-540a>

[第一部分](https://dev.to/migueloop/my-collection-of-react-interview-questions-part-13-1d0b)
[第二部分](https://dev.to/migueloop/my-collection-of-react-interview-questions-part-23-540a)
第三部分

# +10 面试问题在 react

让我们再深入一点

> ### 11。 **What's your understanding of the referee's reaction?**

引用是一种让您获得已创建组件的句柄的方式

它使得存储对由 component render()配置函数返回的特定 React 元素或组件的引用成为可能。当您需要从组件内部引用 render()函数中包含的某个元素或组件时，这可能很有价值。

```
<Text ref={(component)=>{this.textComponent = component}} > Here is some Text</Text> 
```

Enter fullscreen mode Exit fullscreen mode

然后在代码的后面，你可以通过做:
来访问你的文本

```
this.textComponent 
```

Enter fullscreen mode Exit fullscreen mode

这将使您能够以面向对象的方式调用组件上的函数。

> ### 12。 **When you need a referee instead of a referee?**

但是使用 ref 属性并不总是一个好主意。一般的经验法则是避免它。React 官方文档提到了三种可以使用它的情况，因为你没有其他选择。

*   管理焦点、文本选择或媒体播放。
*   与第三方 DOM 库集成。
*   触发命令式动画。

> ### 13。 **When was T3】 ref value first set?**

ref 首先在第一次 render()之后，但在 componentDidMount()之前设置。

> ### 14。 **Use ref only when necessary, otherwise don't use it ... Why?**

*   它阻碍了 Babel 内嵌插件的优化工作。
*   使用 refs 有点偏离 react 的思维方式，也就是一旦状态改变，你就要重新渲染依赖于该状态的所有 UI 组件。React 将负责确保只更新 DOM 中正确的部分，使整个事情变得高效。您最终可能会以 Jquery 的方式使用 refs，但这并不是我们的目标。

> ### 15。 **What is an event in React?**

在 React 中，事件是对鼠标悬停、鼠标点击、按键等特定动作的触发反应。处理这些事件类似于处理 DOM 元素中的事件。但是有一些语法上的不同，例如:

*   事件使用 camel case 命名，而不是仅仅使用小写字母。
*   事件作为函数而不是字符串传递。

事件参数包含一组特定于事件的属性。每个事件类型都包含自己的属性和行为，只能通过其事件处理程序进行访问。

> ### 16。 **How do you create an event in React? .**

```
 class Display extends React.Component({    
    show(evt) {
        // code 
    },   
    render() {      
        // Render the div with an onClick prop (value is a function) 
        return (            
          <div onClick={this.show}>Click Me!</div>
        );    
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

> ### 17。 **What are the composite events in React?**

在 React 中，当你像我们用 onClick 那样在 JSX 中指定一个事件时，你不是直接处理常规的 DOM 事件。相反，您正在处理一种称为合成事件的 React 特定事件类型。您的事件处理程序不会获取 MouseEvent、KeyboardEvent 等类型的本机事件参数。它们总是获得 SyntheticEvent 类型的事件参数，这些参数包装了浏览器的本机事件。

> ### 18。 **Why React sometimes needs to specify a key attribute.**

因为这是 React 处理最小 DOM 变化的方式。我们应该为每个孩子以及孩子内部的每个元素添加一个键。

> ### 19。 **What are the two types of components T3】 React 【T4]**

React 组件可以是两种类型之一。它可以是函数组件，也可以是类组件。

*   功能组件是反应组件的最简单形式。这是一个具有简单契约的简单函数:

```
const Hello = ({ name }) => (<div>Hello, {name}!</div>); 
```

Enter fullscreen mode Exit fullscreen mode

函数组件接收一个属性对象，该对象通常被命名为 props。它返回看起来像 HTML，但实际上是 JSX。

类组件是定义 React 组件的一种更具特色的方式。它也像一个接收 props 的函数，但是该函数也将私有内部状态视为控制返回的 JSX 的附加输入。

```
class Hello extends React.Component {
    constructor(props) {
        super(props);
    }

    render() {
        return(
            <div>
                Hello {props}
            </div>
        )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> ### 18。 **What's the difference between containers and components?**

*   组件是 React API 的一部分。组件是描述 React UI 一部分的类或函数。

*   容器是连接到 redux 存储的 React 组件的非正式术语。容器接收 Redux 状态更新和调度动作，它们通常不呈现 DOM 元素；他们将渲染委托给表示性的子组件。

> ### 19。 **What is the higher-order component? For example,**

高阶组件是重用组件逻辑的一种高级方式。基本上，这是一种源自 React 的组合性质的模式。HOC 是将另一个组件包装在其中的定制组件。它们可以接受任何动态提供的子组件，但不会修改或复制输入组件的任何行为。你可以说 HOC 是“纯”组件。

```
//it's a function that accepts ComponentToDebug and explicitly returns a Functional component 
let DebugComponent = (ComponentToDebug) => {
  return (props) => (
    <div className="debug">
      <ComponentToDebug {...props}/>
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

> ### 20。 **What can you do with HOC?**

*   代码重用、逻辑和引导抽象
*   渲染高劫持
*   状态抽象和操作
*   道具操作