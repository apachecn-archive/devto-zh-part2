# 反应 onClick 事件处理程序指南

> 原文：<https://dev.to/bnevilleoneill/a-guide-to-react-onclick-event-handlers-517i>

[![](img/5a3e07e3e03bd0435216f820c9f36023.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lBg1IqRn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/700/1%2Ac1duI8rwtQygnJem9RyYUg.png)

事件处理程序用于确定每当触发事件时要采取的操作。这可能是鼠标点击或文本输入的改变。

在 React 应用程序中，事件以 camelCase 格式编写，这意味着 onclick 事件将在 React 应用程序中编写为 onClick。

React 实现了一个[合成事件](https://reactjs.org/docs/events.html)系统，为 React 应用程序和接口带来了一致性和高性能。它通过规范化事件来实现一致性，使它们在不同的浏览器和平台上具有相同的属性。

合成事件是围绕浏览器本地事件的跨浏览器包装器。它与浏览器的本机事件具有相同的接口，包括 stopPropagation()和 preventDefault()，只是这些事件在所有浏览器中的工作方式相同。

它通过自动使用事件委托来实现高性能。实际上，React 并不将事件处理程序附加到节点本身。相反，单个事件侦听器附加到文档的根；当事件被触发时，React 将其映射到适当的组件元素。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/?cid=banner_a)

### **听事件**

在 React 中监听事件可以像下面的例子一样简单。

[https://codesandbox.io/embed/84ol32ono9](https://codesandbox.io/embed/84ol32ono9)

在上面的示例中，onClick 属性是我们的事件处理程序，它被添加到目标元素中，以指定当该元素被单击时要执行的功能。onClick 属性被设置为 showAlert 函数，该函数对消息发出警报。

更简单地说，这意味着每当点击按钮时，showAlert 函数被调用，依次显示警告框。

### **装订方式**

在 JavaScript 中，默认情况下，类方法没有被[绑定](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind)。因此，将函数绑定到类实例非常重要。

**在 render()中绑定**

解决绑定问题的一种方法是在渲染函数中调用 bind。

[https://codesandbox.io/embed/yqy5m2v6rz](https://codesandbox.io/embed/yqy5m2v6rz)

在上面的例子中，我们使用 onChange 事件处理程序来监听文本输入中的输入事件。这是通过在 render 函数中绑定它来实现的。此方法需要调用。在 render()函数中绑定(this)。

为什么？

ES6 类的任何方法都是普通的 JavaScript 函数，因此它从[函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function)原型继承了 bind()。所以现在当我们在 JSX 内部调用 onChange()时，它将指向我们的类实例。很简单。

不过，使用这种方法意味着可能会有一些性能影响，因为该函数在每次渲染时都会重新分配。这种性能成本在小型 React 应用程序中可能根本看不到，但在较大的 React 应用程序中可能会很明显。

#### 在构造函数中绑定()

如果 render 中的绑定对您不起作用，您可以在构造函数中绑定。请参见下面的示例:

[https://codesandbox.io/embed/yj6rz861mx](https://codesandbox.io/embed/yj6rz861mx)

从上面可以看出，changeText 函数是绑定在构造函数中的。

```
this.changeText = this.changeText.bind() 
```

让我们回顾一下上面的代码行是如何工作的。

第一个 this.changeText 引用 changeText 方法。因为这是在构造函数中完成的，“this”指的是 ChangeInput 类组件。

第二个 this.changeText 也引用了同一个 changeText()方法，但我们现在调用的是。在上面绑定()。

最后一个“this”是我们要传递到的上下文。bind()并且它引用 ChangeInput 类组件。

还需要注意的是，如果 changeText 没有绑定到类实例，它将无法访问 this.setState，因为这将是未定义的。这是绑定事件处理函数的另一个重要原因。

**与箭头功能绑定**

处理事件的另一种方式是通过与粗箭头函数绑定。使用 ES7 类属性，我们可以在方法定义处进行绑定，如下例所示:

[https://codesandbox.io/embed/rmr84vw1ro](https://codesandbox.io/embed/rmr84vw1ro)

根据定义，**箭头函数表达式**的语法比函数表达式短，并且没有自己的 this、arguments、super 或 new.target。

在上面的示例中，一旦创建了组件，this.handleEvent 就不会再更改了。这反过来意味着，`<button>`不会被重新渲染。这是一种非常简单易懂的方法。

这种方法也有其性能成本，就像 render function 方法中的绑定一样。

### **自定义组件和事件**

当谈到 React 中的事件时，只允许 DOM 元素拥有事件处理程序。以一个名为 CustomButton 的组件为例，它带有一个 onClick 事件。由于上述原因，这不会对点击做出响应。

那么我们如何处理定制组件的事件处理呢？

通过在 CustomButton 组件中呈现一个 DOM 元素并将 onClick prop 传递给它。我们的 CustomButton 本质上是 click 事件的一个传递。

[https://codesandbox.io/embed/xvj242m92q](https://codesandbox.io/embed/xvj242m92q)

在上面的示例中，CustomButton 组件被传递了一个 onPress 属性，然后该属性被传递到按钮的 onClick 中。

### 结论

事件处理程序用于确定事件发生时要采取的操作。onClick 事件用于列出 DOM 元素上的点击事件。

当谈到事件处理时，绑定是一个非常重要的话题，有几种方法可以实现它。所以现在的问题是你用哪种绑定方法？

在大多数应用程序中，在 render 函数中绑定对性能的影响并不明显，因此为了可读性和维护性，您可以考虑使用该方法。

但是为了获得更好的性能，您可以考虑在构造函数方法中使用绑定。

然而，如果你已经在编写第二阶段的 JavaScript 代码，那么使用箭头函数是最安全的。

* * *

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[反应 onClick 事件处理程序指南](https://blog.logrocket.com/a-guide-to-react-onclick-event-handlers-d411943b14dd/)首先出现在[日志博客](https://blog.logrocket.com)上。