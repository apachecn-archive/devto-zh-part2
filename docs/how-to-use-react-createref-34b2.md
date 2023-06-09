# 如何使用 React createRef

> 原文：<https://dev.to/bnevilleoneill/how-to-use-react-createref-34b2>

[![Alt Text](img/0024a2460c101d00dc7f140fd40faabc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hB3IBARz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ggrkrilkhnxm1elihsc.jpeg)

如果你已经开发 web 应用足够长的时间，那么很可能在某个时候你已经使用过 JavaScript [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) 库，比如 [jQuery](http://jquery.com/) 、 [Mootools](https://mootools.net/) 、 [Prototype.js](http://prototypejs.org/learn/) 等。这些库的出现带来了交互式 web 应用程序构建方式的重大转变。有了 DOM 抽象 API，操纵 web 应用程序的内容变得容易多了。

例如，您会发现自己在用 jQuery:
做这样的事情

```
$('#button').on('click', function(evt) {
  evt.preventDefault();
  var content = $('<h1>Random Post Title</h1><p>Random post text.</p>');

  $('#element').append(content);
}); 
```

跟随过去几年的趋势，你也会注意到现在的焦点是 JavaScript 框架，如 [React](https://reactjs.org/) 、 [Angular](https://angular.io/) 、 [Ember.js](https://www.emberjs.com/) 、 [VueJS](https://vuejs.org/) 等，用于构建现代应用程序。

这些框架之间的一个相似之处是它们都是用基于组件的架构构建的。所以现在，听到组件比听到基于 DOM 的交互更常见，因为大多数交互都封装在组件中。

虽然通过利用这些现代 JavaScript 框架的内置功能，您可以做很多事情，但有时您需要与实际的 DOM 交互来实现一些本机行为。大多数现代框架都提供 API，通过这些 API 可以访问应用程序的原生 DOM 表示，React 也不例外。

在本教程中，我们将考虑如何在 React 应用程序中与 DOM 交互。我们还将看到如何使用 React 16.3 中引入的 React.createRef()特性。

[![](img/184dfcc3649f17c50808cfdfb9409f6a.png)T2】](https://logrocket.com/?cid=banner_b)

### 参与 DOM

React 提供了一个称为 **refs** 的特性，允许从组件访问 DOM。您只需将一个**引用**附加到应用程序中的一个元素上，就可以从组件中的任何地方访问该元素的 DOM。

Refs 还可以用来提供对 React 元素的直接访问，而不仅仅是 DOM 节点。下面是 [React 文档](https://reactjs.org/docs/refs-and-the-dom.html)关于引用的内容:

> Refs 提供了一种方法来访问在 render 方法中创建的 DOM 节点或 React 元素。

一般来说，只有当使用**状态**和**道具**的机制无法实现所需的交互时，才应考虑使用参。

但是，在一些情况下，使用 ref 是合适的。其中之一是在与第三方 DOM 库集成时。此外，处理文本选择或管理媒体回放行为等深层交互也需要在相应的元素上使用 refs。

### 创建参照

React 提供了三种创建引用的主要方法。以下是从最古老的方法开始的不同方法的列表:

1.  字符串引用*(遗留方法)*
2.  回调参考
3.  React.createRef *(来自 React 16.3)*

### 参串

在 React 应用程序中创建引用的传统方式是使用字符串引用。这是最古老的方法，被认为是遗留的或已废弃的，因为它将在 React 框架的未来版本中被删除。

字符串引用的创建非常简单，只需向所需的元素添加一个 ref 属性，并传递 ref 的字符串名称作为其值。这里有一个简单的例子:

```
class MyComponent extends React.Component {

  constructor(props) {
    super(props);
    this.toggleInputCase = this.toggleInputCase.bind(this);
    this.state = { uppercase: false };
  }

  toggleInputCase() {
    const isUpper = this.state.uppercase;

    // Accessing the ref using this.refs.inputField
    const value = this.refs.inputField.value;

    this.refs.inputField.value =
      isUpper
        ? value.toLowerCase()
        : value.toUpperCase();

    this.setState({ uppercase: !isUpper });
  }

  render() {
    return (
      <div>
        {/* Creating a string ref named: inputField */}
        <input type="text" ref="inputField" />

        <button type="button" onClick={this.toggleInputCase}>
          Toggle Case
        </button>
      </div>
    );
  }

} 
```

这里我们创建了一个非常简单的 React 组件，它呈现一个`<input>`元素和一个`<button>`元素，允许我们在大写和小写之间切换输入的大小写。

我们用设置为 false 的大写属性来初始化组件的状态。这个属性允许我们确定输入的当前大小写。

重点是我们在`<input>`元素上创建的字符串 ref。这里我们创建了一个对名为 inputField 的`<input>`元素的引用。

稍后在`<button>`的 click 事件处理程序中，我们通过`this.refs.inputField`访问 ref。并操纵`<input>`元素的 DOM 来更改输入的值。

这是一个交互的示例演示:

[![](img/cc04a638fcec696a6ad0cff8302b640d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vB5RFEyv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AgQcZppwPPsdZnNQFcydk0g.gif) 

<figcaption>样本演示截图</figcaption>

尽管这是一个关于如何使用 refs 的非常简单的例子，但是我们已经能够看到如何在 React 组件中使用字符串 refs。如前所述，不鼓励在 React 应用程序中使用字符串引用。

下面是 [React 文档](https://reactjs.org/docs/refs-and-the-dom.html)关于字符串引用的内容:

> 如果你目前正在使用 *this.refs.textInput* 来访问引用，我们建议使用[回调模式](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)或者 [createRef API](https://reactjs.org/docs/refs-and-the-dom.html#creating-refs) 来代替。

### 回调引用

回调引用使用回调函数来创建引用，而不是将引用的名称作为字符串传递。如果您使用 React 之前的版本，那么这应该是您创建引用的首选方法。

回调函数接收 React 组件实例或 HTML DOM 元素作为其参数，可以在其他地方存储和访问。使用回调 ref，我们之前的代码片段将变成如下所示。

```
class MyComponent extends React.Component {

  constructor(props) {
    super(props);
    this.toggleInputCase = this.toggleInputCase.bind(this);
    this.state = { uppercase: false };
  }

  toggleInputCase() {
    const isUpper = this.state.uppercase;

    // Accessing the ref using this.inputField
    const value = this.inputField.value;

    this.inputField.value =
      isUpper
        ? value.toLowerCase()
        : value.toUpperCase();

    this.setState({ uppercase: !isUpper });
  }

  render() {
    return (
      <div>
        {/* Creating a callback ref and storing it in this.inputField */}
        <input type="text" ref={elem => this.inputField = elem} />

        <button type="button" onClick={this.toggleInputCase}>
          Toggle Case
        </button>
      </div>
    );
  }

} 
```

这里我们做了两个主要的改变。首先，我们使用回调函数定义了 ref，并将其存储在 this.inputField 字段中，如下所示:

```
<input type="text" ref={elem => this.inputField = elem} /> 
```

然后在事件处理程序中，我们使用 this.inputField 而不是 this.refs.inputField 来访问 ref。

当像我们在例子中那样使用内联回调引用时，重要的是要知道对于组件的每次更新，回调函数被调用两次。首先使用 null，然后再次使用 DOM 元素。

但是，将回调函数创建为组件类的绑定方法可以用来避免这种行为。

使用回调函数创建引用可以让您更好地控制如何创建、设置和取消设置引用。

### 使用 React.createRef

从 React 16.3 开始，React API 包含了一个新的 createRef()方法，该方法可以用来创建引用，就像我们使用回调函数一样。您只需通过调用 React.createRef()创建一个 Ref，并将结果 ref 赋给一个元素。

使用 React.createRef()，我们之前的例子看起来是这样的:

```
class MyComponent extends React.Component {

  constructor(props) {
    super(props);
    this.inputField = React.createRef();
    this.toggleInputCase = this.toggleInputCase.bind(this);
    this.state = { uppercase: false };
  }

  toggleInputCase() {
    const isUpper = this.state.uppercase;

    // Accessing the ref using this.inputField.current
    const value = this.inputField.current.value;

    this.inputField.current.value =
      isUpper
        ? value.toLowerCase()
        : value.toUpperCase();

    this.setState({ uppercase: !isUpper });
  }

  render() {
    return (
      <div>
        {/* Referencing the ref from this.inputField */}
        <input type="text" ref={this.inputField} />

        <button type="button" onClick={this.toggleInputCase}>
          Toggle Case
        </button>
      </div>
    );
  }

} 
```

这里我们看到一些变化。首先，在构造函数()中，我们使用 React.createRef()创建了一个 ref，并将其存储在 this.inputField 中，如下:

```
this.inputField = React.createRef(); 
```

接下来，在事件处理程序中，我们使用 this.inputField . current 而不是 This . input field 来访问 ref。对节点的引用在 ref 的当前属性处变得可访问。

最后，我们将 ref 传递给`<input>`组件，如下所示:

```
<input type="text" ref={this.inputField} /> 
```

我们已经探索了在 React 应用程序中创建 refs 的各种方法。在接下来的几节中，我们将继续深入了解 React.createRef 更有趣的特性。

### 组件参考

在上一节中，我们看到了如何使用新的 React.createRef API 创建引用。实际引用存储在 ref 的当前属性中。

到目前为止，在我们的示例中，我们只在应用程序中创建了对 DOM 节点的引用。但是也可以创建 refs 来反应组件，这将使我们能够访问这些组件的实例方法。

> 请注意，我们只能在类组件上创建引用，因为它们在挂载时会创建类的一个实例。不能在功能元件上使用参照。

让我们考虑一个非常简单的例子来演示在 React 组件上使用 refs。在本例中，我们将创建两个组件:

*   FormInput 组件简单地包装了一个`<input>`元素并提供了两个方法。一个用于知道输入何时包含某个值，另一个用于选择输入文本。
*   MyComponent 只是包装了 FormInput 组件和一个按钮，以便在单击时选择输入中的文本。

以下是组件的代码片段:

```
class FormInput extends React.Component {

  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  hasText() {
    return this.textInput.current.value.length > 0;
  }

  selectInputText() {
    this.textInput.current.select();
  }

  render() {
    return (
      <div>
        {/* Creating a string ref named: textInput */}
        <input type="text" ref={this.textInput} />
      </div>
    );
  }

} 
```

像以前一样，我们使用 React.createRef()创建了一个 Ref，并将该 ref 添加到 render 函数中的`<input>`元素。我们创造了两种方法:

*   hasText()，返回一个布尔值，表明输入元素的值不为空。因此，如果输入为空，则返回 false，否则返回 true。
*   selectInputText()，用于选择输入元素中的整个文本。

注意，通过访问我们创建的 ref 的 current 属性，我们在方法中获得了对 input 元素的引用，即:this.textInput.current。

现在让我们继续创建 MyComponent。下面是代码片段:

```
const MyComponent = (props) => {

  const formInput = React.createRef();

  const inputSelection = () => {
    const input = formInput.current;

    if (input.hasText()) {
      input.selectInputText();
    }
  };

  return (
    <div>
      <button type="button" onClick={inputSelection}>
        Select Input
      </button>

      <FormInput ref={formInput} />
    </div>
  );

}; 
```

在这个代码片段中，我决定使用一个函数组件而不是类组件，因为这是一个无状态组件。我还想用这个组件来演示如何在函数组件中使用 refs。

这里我们使用 React.createRef()创建一个 ref，并将其存储在 formInputconstant 中。注意这里我们没有使用它，因为功能组件不是类，因此不会创建实例。

注意，在 render()方法中，我们将创建的 ref 添加到了前面创建的`<FormInput>`组件中。与前面向 DOM 节点添加引用的例子不同，在这里，我们向组件添加一个引用。

请注意，只能为类组件而不是功能组件创建 ref。FormInput 是一个类组件，因此我们可以创建对它的引用。然而，我们可以在函数组件中使用 ref，就像我们在这个例子中使用 formInput 一样。

最后，在 inputSelection()函数中，我们像以前一样使用 ref 的当前属性来访问对组件的引用。

注意，我们能够访问 FormInput 组件的 hasText()和 selectInputText()方法，因为引用指向 FormInput 组件的一个实例。这验证了为什么不能为功能元件创建参照。

这是一个交互的示例演示:

[![](img/b0ebe81c3c690fb1b29a1bfd96081429.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MQt-Uz8T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2A4ShHAxNZEK5Ehzz4gUTg-w.gif) 

<figcaption>样本演示截图</figcaption>

### 参考非受控组件

默认情况下，React 中的所有组件都是受控的，因为 React 负责在表单数据更改时处理组件的更新。

在 React 中处理不受控制的组件时，引用非常方便。这是因为当表单数据更改时，您不使用事件处理程序来更新状态，而是依靠 refs 从 DOM 中获取表单值。您可以在此了解更多关于[非受控组件的信息。](https://reactjs.org/docs/uncontrolled-components.html)

让我们创建一个简单的受控组件，然后创建一个非受控组件，来演示如何使用 refs 从 DOM 中获取表单值。

```
class ControlledFormInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = { value: "Glad" };
  }

  handleChange(evt) {
    this.setState({ value: evt.target.value });
  }

  render() {
    return (
      <div>
        <h1>Hello {this.state.value}!</h1>
        <input type="text" value={this.state.value} onChange={this.handleChange} placeholder="Enter your name" />
      </div>
    )
  }
} 
```

上面的代码片段显示了一个包含`<input>`元素的受控组件。注意，`<input>`元素的值是从状态的 value 属性中获得的。我们将 state 上的值初始化为“Glad”(反正这是我的名字)。

另请注意，我们使用 handleChange()事件处理程序，用从访问 evt.target.value 获得的 input 元素的值来更新状态中的 value 属性。

这是一个交互的示例演示:

[![](img/f079be548b503cb2801eca54afffc200.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8JviwcJn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2A2x_IokLKX8ode_9jfI3avg.gif) 

<figcaption>受控组件演示</figcaption>

下面是组件的非受控版本:

```
class UncontrolledFormInput extends React.Component {
  constructor(props) {
    super(props);
    this.inputField = React.createRef();
    this.handleChange = this.handleChange.bind(this);
    this.state = { value: "Glad" };
  }

  handleChange(evt) {
    this.setState({ value: this.inputField.current.value });
  }

  render() {
    return (
      <div>
        <h1>Hello {this.state.value}!</h1>
        {/* Attach the created ref: this.inputField */}
        <input type="text" ref={this.inputField} defaultValue={this.state.value} onChange={this.handleChange} placeholder="Enter your name" />
      </div>
    )
  }
} 
```

我们对之前组件的非受控版本做了一些改动。首先，我们使用 React.createRef()创建一个 ref，并将其存储在 this.inputField 实例属性中。我们还将 ref 附加到 render()方法中的`<input>`元素。

我们还使用 defaultValue 属性将 this.state.value 指定为`<input>`元素的默认值——这只在输入值第一次改变时有用。

如果我们使用 value prop 为输入指定一个默认值，那么我们将不再从输入中获得更新的值。这是因为在 React 中，值属性会自动覆盖 DOM 中的值。

最后，在我们的事件处理程序中，我们使用 ref 而不是 event 对象来访问输入元素的值。所以我们这样做:

```
this.setState({ value: this.inputField.current.value }); 
```

而不是这样做:

```
this.setState({ value: evt.target.value }); 
```

演示与受控版本相同。这是一个交互的示例演示:

[![](img/9b503baecb25360c11ad5cc0edeb5c59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fTVUCYcX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AcxY0_-IhqfcsIe3ATYzCfg.gif) 

<figcaption>非受控组件演示</figcaption>

### 结论

在本教程中，我们考虑了在 React 应用程序中与 DOM 交互的各种方法。我们已经看到了如何使用 React 16.3 中引入的新 React.createRef()方法来简化创建引用。

我们还考虑了如何在不受控制的组件中使用引用。你可以参考 [React 文档](https://reactjs.org/docs/refs-and-the-dom.html)来了解更多关于你可以用 refs 做什么的信息。

* * *

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[如何使用 React createRef](https://blog.logrocket.com/how-to-use-react-createref-ea014ad09dba/) 最先出现在[日志博客](https://blog.logrocket.com)上。