# 你应该知道的 26 个 React+Redux 面试问题(2018 年)

> 原文：<https://dev.to/fullstackcafe/26-reactredux-interview-questions-you-should-know-in-2018-41je>

[![26 React Interview Questions and Answers in 2018](img/66633faaf7f4b8d5a15ee436fab78ceb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jeJKHYnJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/3702/graffiti-door-closed-greece.jpg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D350) 
“首席 JavaScript 开发者(React JS)——13 万澳元/年”。这是澳大利亚悉尼一位经验丰富的全栈 React Dev 的典型职位空缺描述。来吧，跟随 [FullStack 最常见的 React 和 Redux 面试问题和答案。咖啡馆](https://www.fullstack.cafe)让你在下一次面试中脱颖而出。

> 🔴最初发表于 [FullStack。咖啡馆-杀死你的技术&编码面试](https://www.fullstack.cafe/?utm_source=dev&utm_medium=blog)

### Q1:什么是反应？

> 题目:**反应**
> 难度:⭐

React 是一个由脸书创建的开源 JavaScript 库，用于在 web 和移动应用程序中构建复杂的交互式 ui。React 的核心目的是构建 UI 组件；它通常被称为“MVC”架构中的“V”(视图)。

🔗**来源:** [codementor.io](https://www.codementor.io/blog/5-essential-reactjs-interview-questions-du1084ym1)

### Q2:什么是 Redux？

> 题目:**redux**T2】难度:⭐

Redux 是一个基于 Flux 设计模式的 JavaScript 应用程序的可预测状态容器。Redux 可以与 ReactJS 一起使用，或者与任何其他视图库一起使用。它很小(大约 2kB)，并且没有依赖关系。

🔗**来源:**【github.com/sudheerj】T2

### Q3:什么是虚拟 DOM？

> 题目:**反应**
> 难度:⭐

虚拟 DOM (VDOM) 是真实 DOM 的内存表示。UI 的表示保存在内存中，并与“真正的”DOM 同步。这是调用渲染函数和在屏幕上显示元素之间的一个步骤。这整个过程被称为和解。

🔗**来源:**【github.com/sudheerj】T2

### Q4:什么是通量？

> 题目:**redux**T2】难度:⭐

**Flux** 是一种应用程序设计范例，用于替代更传统的 mvc 模式。它不是一个框架或库，而是一种新的体系结构，补充了 React 和单向数据流的概念。脸书在使用 React 时，在内部使用了这种模式。在 dispatcher、stores 和 views 组件之间的工作流具有不同的输入和输出，如下所示:

[![](img/9e4c46bb167d191286a351bb167aedf1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7e-0f9Fb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sudheerj/reactjs-interview-questions/raw/mastimg/flux.png)

🔗**来源:**【github.com/sudheerj】T2

### Q5:ReactJS 有什么优势？

> 题目:**反应**
> 难度:⭐

ReactJS 的优势如下:

1.  使用虚拟 DOM 提高应用程序的性能
2.  JSX 使代码易于读写
3.  它在客户端和服务器端都呈现
4.  易于与其他框架(Angular，BackboneJS)集成，因为它只是一个视图库
5.  易于编写 UI 测试用例以及与 JEST 等工具的集成。

🔗**来源:**【github.com/sudheerj】T2

### Q6:ReactJS 的主要特点是什么？

> 题目:**反应**
> 难度:⭐

ReactJS 的主要特点如下:

*   它使用 **VirtualDOM** 而不是 RealDOM，因为考虑到 RealDOM 操作是昂贵的。
*   支持**服务器端渲染**
*   遵循**单向**数据流或数据绑定
*   使用**可重用/可组合的** UI 组件来开发视图

🔗**来源:**【https://github.com/sudheerj】T2

### Q7:表象组件和容器组件有什么区别？

> 题目:**反应**
> 难度:⭐⭐

*   **表象成分**关注*事物看起来如何*。它们通常通过 props 专门接收数据和回调。这些组件很少有自己的状态，但是当它们有状态时，通常关注 UI 状态，而不是数据状态。

*   **容器组件**更关心*事情如何工作*。这些组件向表示组件或其他容器组件提供数据和行为。它们调用 Flux 动作，并将这些动作作为回调提供给表示组件。它们通常也是有状态的，因为它们充当数据源。

🔗**来源:**【github.com/Pau1fitz】T2

### Q8:描述如何在 React 中处理事件。

> 题目:**反应**
> 难度:⭐⭐

为了解决跨浏览器兼容性问题，React 中的事件处理程序将被传递 SyntheticEvent 的实例，这是 React 对浏览器本机事件的跨浏览器包装。这些合成事件与您习惯的原生事件具有相同的界面，只是它们在所有浏览器中的工作方式相同。

稍微有趣的是，React 实际上并不将事件附加到子节点本身。React 将使用单个事件监听器监听顶级的所有事件。这对性能有好处，这也意味着 React 在更新 DOM 时不需要担心跟踪事件侦听器。

🔗**来源:**【tylermcginnis.com】T2

### Q9:ReactJS 中的 state 是什么？

> 题目:**反应**
> 难度:⭐⭐

组件的状态是一个对象，它保存了一些信息，这些信息可能会随着组件的生命周期而改变。我们应该总是尽量使我们的状态简单，并尽量减少有状态组件的数量。

让我们用消息状态创建用户组件，

```
 class User extends React.Component {
    constructor(props) {
       super(props);

       this.state = {
          message: "Welcome to React world",
       }
    }
    render() {
       return (
          <div>
             <h1>{this.state.message}</h1>
          </div>
       );
    }
 } 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【https://github.com/sudheerj】T2

### Q10:状态和道具有什么区别？

> 题目:**反应**
> 难度:⭐⭐

**道具**和**状态**都是普通的 JavaScript 对象。虽然它们都包含影响渲染输出的信息，但它们在组件方面的功能是不同的。即，

*   类似于函数参数，属性被传递给组件
*   状态在组件中被管理，类似于在函数中声明的变量。

🔗**来源:**【https://github.com/sudheerj】T2

### Q11:react js 有哪些局限性？

> 题目:**反应**
> 难度:⭐⭐

以下是限制列表:

1.  React 只是一个视图库，而不是一个成熟的框架
2.  对于刚接触 web 开发的初学者来说，有一个学习曲线。
3.  将 React.js 集成到传统的 MVC 框架需要一些额外的配置
4.  代码复杂性随着内联模板和 JSX 而增加。
5.  过多的小组件导致过度工程化或样板文件

🔗**来源:**【github.com/sudheerj】T2

### Q12:React 中的“元素”和“组件”有什么区别？

> 题目:**反应**
> 难度:⭐⭐

简单地说，React 元素描述了您希望在屏幕上看到的内容。简单地说，React 元素是某些 UI 的对象表示。

React 组件是一个函数或类，它可选地接受输入并返回 React 元素(通常通过 JSX，该元素被转换为 createElement 调用)。

🔗**来源:**【medium.freecodecamp.org/】T2

### Q13:React 与 AngularJS (1.x)有何不同？

> 题目:**反应**
> 难度:⭐⭐

例如，AngularJS (1.x)通过扩展 HTML 标记并在运行时注入各种结构(如指令、控制器、服务)来构建应用程序。因此，AngularJS 对应用程序的更大架构非常固执——这些抽象在某些情况下当然有用，但它们是以灵活性为代价的。

相比之下，React 只关注组件的创建，对应用程序的架构没有什么看法。这允许开发人员在选择他们认为“最好”的架构时有难以置信的灵活性——尽管这也将选择(或构建)那些部分的责任放在了开发人员身上。

🔗**来源:** [codementor.io](https://www.codementor.io/blog/5-essential-reactjs-interview-questions-du1084ym1)

### Q14:Redux over Flux 有哪些缺点？

> 题目:**redux**T2】难度:⭐⭐⭐

我们可以说，使用 Redux 而不是 Flux 几乎没有什么不利之处。这些建议如下:

1.  你将需要学习避免突变: Flux 对突变数据没有主见，但是 Redux 不喜欢突变，并且许多补充 Redux 的包假设你从不改变状态。您可以使用开发专用包(如 redux-immutable-state-invariant、Immutable.js)或您的团队来编写非可变代码，从而强制实现这一点。
2.  **你将不得不小心挑选你的包:**虽然 Flux 明确地不试图解决诸如撤销/重做、持久性或表单之类的问题，但 Redux 有诸如中间件和存储增强器之类的扩展点，并且它已经产生了一个年轻但丰富的生态系统。这意味着大多数软件包都是新的想法，还没有被大量使用
3.  **还没有很好的流集成:** Flux 目前允许你做非常令人印象深刻的静态类型检查，Redux 还不支持。

🔗**来源:**【github.com/sudheerj】T2

### Q15:react js(16)中有哪些错误边界？

> 题目:**反应**
> 难度:⭐⭐⭐

错误边界是 React 组件，它捕捉子组件树中任何地方的 JavaScript 错误，记录这些错误，并显示一个回退 UI，而不是崩溃的组件树。

如果一个类组件定义了一个名为`componentDidCatch(error, info)`
的新的生命周期方法，它就会成为一个错误边界

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  componentDidCatch(error, info) {
    // Display fallback UI
    this.setState({ hasError: true });
    // You can also log the error to an error reporting service
    logErrorToMyService(error, info);
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

之后将其作为常规组件使用

```
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary> 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【github.com/sudheerj】T2

### Q16:react js 为什么使用 className 而不是 class 属性？

> 题目:**反应**
> 难度:⭐⭐⭐

**class** 是 javascript 中的关键字，JSX 是 javascript 的扩展。这就是 React 使用`className`而不是 class 的主要原因。

```
render() {
  return <span className="menu navigation-menu">Menu</span> } 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【github.com/sudheerj】T2

### Q17:如何访问 react 组件外部的 redux store？

> 题目:**redux**T2】难度:⭐⭐⭐

是的。您只需要从使用`createStore`创建商店的模块中导出商店。同样，它不应该污染全局窗口对象

```
store = createStore(myReducer);
export default store; 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【github.com/sudheerj】T2

### 你能告诉我一些关于 JSX 的事吗？

> 题目:**反应**
> 难度:⭐⭐⭐

当脸书第一次发布 React to the world 时，他们还引入了一种新的 JavaScript 方言，称为 JSX，在 JavaScript 代码中嵌入原始 HTML 模板。浏览器无法读取 JSX 代码本身；它必须使用像 Babel 和 webpack 这样的工具转换成传统的 JavaScript。虽然许多开发人员最初会本能地反对它，但 JSX(与 ES2015 一起)已经成为定义 React 组件的事实方法。

```
class MyComponent extends React.Component {
  render() {
    let props = this.props;  
    return (
      <div className="my-component">
      <a href={props.url}>{props.name}</a>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:** [codementor.io](https://www.codementor.io/blog/5-essential-reactjs-interview-questions-du1084ym1)

### Q19:为什么我们不应该直接更新状态？

> 题目:**反应**
> 难度:⭐⭐⭐

如果您尝试直接更新状态，那么它不会重新呈现组件。

```
 //Wrong
    This.state.message =”Hello world”; 
```

Enter fullscreen mode Exit fullscreen mode

而是用`setState()`的方法。它计划更新组件的状态对象。当状态改变时，组件通过重新渲染
做出响应

```
 //Correct
    This.setState({message: ‘Hello World’}); 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**唯一可以分配状态的地方是构造函数。

🔗**来源:**【https://github.com/sudheerj】T2

### Q20:react js 组件生命周期的不同阶段是什么？

> 题目:**反应**
> 难度:⭐⭐⭐

React 组件生命周期有四个不同的阶段:

1.  **初始化:**在这个阶段，react 组件准备设置初始状态和默认属性。
2.  **挂载:**react 组件已经准备好挂载到浏览器 DOM 中。这个阶段涵盖了**组件安装**和**组件安装**生命周期方法。
3.  **更新:**在这个阶段，组件以两种方式更新，发送新的道具和更新状态。这个阶段涵盖了**shouldcomponentdupdate、componentWillUpdate 和 componentDidUpdate** 生命周期方法。
4.  **卸载:**在最后一个阶段，组件不再需要，并从浏览器 DOM 中卸载。这个阶段包括**组件将卸载**生命周期方法。

[![](img/2664936c99962d32f92e5959c2c396df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JddQdxAF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sudheerj/reactjs-interview-questions/raw/mastimg/phases.png)

🔗**来源:**【github.com/sudheerj】T2

### Q21:描述一下通量 vs MVC？

> 题目:**反应**
> 难度:⭐⭐⭐⭐

传统的 MVC 模式很好地分离了数据(模型)、UI(视图)和逻辑(控制器)的关注点，但是 MVC 架构经常遇到两个主要问题:

*   **定义不良的数据流:**视图间发生的级联更新通常会导致难以调试的错综复杂的事件网络。

*   **缺乏数据完整性:**模型数据可以从任何地方变异，在整个 UI 中产生不可预测的结果。

有了流量模式，复杂的用户界面不再遭受级联更新；任何给定的 React 组件都能够基于存储提供的数据来重建其状态。通量模式还通过限制对共享数据的直接访问来加强数据完整性。

🔗**来源:** [codementor.io](https://www.codementor.io/blog/5-essential-reactjs-interview-questions-du1084ym1)

### Q22:为什么要在 React 组件中使用 forceUpdate？

> 题目:**反应**
> 难度:⭐⭐⭐⭐

为了在某些情况下强制重新渲染，React 没有检测到需要更新 UI 的情况。通常情况下，这不需要调用。

🔗**来源:**【github.com/WebPredict】T2

### Q23:这段代码怎么了？

> 题目:**反应**
> 难度:⭐⭐⭐⭐

**问题:**

这段代码有什么问题？

```
this.setState((prevState, props) => {
  return {
    streak: prevState.streak + props.count
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

**答案:**

没毛病。这种方法很少使用，也不广为人知，但是您也可以向`setState`传递一个函数，该函数接收前一个状态和道具并返回一个新状态，就像我们在上面所做的一样。这不仅没有错，而且如果你是基于以前的状态来设置状态的话，这也是非常值得推荐的。

🔗**来源:**【tylermcginnis.com】T2

### Q24:受控组件和非受控组件有什么区别？

> 题目:**反应**
> 难度:⭐⭐⭐⭐

*   一个**受控组件**是一个由 React 控制的组件，并且是表单数据的唯一来源。
*   一个**不受控制的组件**是 DOM 处理表单数据的地方，而不是在 React 组件内部。

虽然不受控制的组件通常更容易实现，因为您只需使用 refs 从 DOM 中获取值，但是通常建议您优先使用受控制的组件，而不是不受控制的组件。其主要原因是受控组件支持即时字段验证，允许您有条件地禁用/启用按钮，强制输入格式，并且更像“反应方式”。

🔗**来源:**【github.com/Pau1fitz】T2

### Q25:解释 Flux 和 AngularJS (1.x)方法之间的一些区别

> 题目:**反应**
> 难度:⭐⭐⭐⭐⭐

AngularJS 中的 UI 组件通常依赖一些内部的`$scope`来存储它们的数据。这些数据可以从 UI 组件或任何可以访问`$scope`的地方直接变异——对于依赖这些数据的组件或更大的应用程序的任何部分来说，这都是一种危险的情况。

相比之下，流动模式鼓励使用不可变的数据。因为存储是所有数据的中央权威，所以数据的任何变化都必须发生在存储中。数据污染的风险大大降低。

🔗**来源:** [codementor.io](https://www.codementor.io/blog/5-essential-reactjs-interview-questions-du1084ym1)

### Q26:React 中副作用是什么意思？提供一些例子。

> 题目:**反应**
> 难度:⭐⭐⭐⭐⭐

一个**“副作用”**是任何影响正在执行的功能范围之外的事情。比方说，这可能是一个网络请求，它让您的代码与第三方进行通信(从而发出请求，导致记录日志、保存或更新缓存，以及函数之外的各种影响。

还有更微妙的副作用。改变闭包作用域变量的值是一个副作用。将一个新项推送到作为参数传入的数组上是一个副作用。执行起来没有副作用的函数被称为“纯”函数:它们接受参数，并返回值。执行该函数时不会发生任何其他事情。这使得易于测试，易于推理，并且当涉及到优化或重构时，满足这种描述的函数具有各种有用的属性。

纯函数是确定性的(意味着给定一个输入，它们总是返回相同的输出)，但这并不意味着所有不纯的函数都有副作用。例如，在函数中生成随机值会使函数不纯，但这不是副作用。React 是关于纯函数的，并且要求你保持几个生命周期方法的纯净。

🔗**来源:**【reddit.com】T2

> 谢谢🙌阅读，祝你面试好运！
> *如果你喜欢这篇文章，请分享给你的开发者伙伴！*
> *查看更多全栈面试问题&答案上👉[www . full stack . cafe](https://www.fullstack.cafe)T9】*