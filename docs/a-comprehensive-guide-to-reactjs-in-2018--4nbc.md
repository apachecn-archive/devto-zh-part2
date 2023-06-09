# 2018 年 React.js 综合指南

> 原文：<https://dev.to/tylermcginnis/a-comprehensive-guide-to-reactjs-in-2018--4nbc>

*这篇文章最初发表于 2015 年 1 月，但最近更新到 React 16.3 及其包含的所有优点。*

#### **React.js 基本面:**

组件是 React 的构建块。如果你来自一个有角度的背景，组件非常类似于指令。如果您来自不同的背景，它们本质上是小部件或模块。你可以把一个组件想象成 HTML、CSS、JS 和一些特定于该组件的内部数据的集合。我喜欢把 React 组件想象成 web 的 [Kolaches](https://www.google.com/search?q=kolache&rlz=1C5CHFA_enUS719US719&source=lnms&sa=X&ved=0ahUKEwi_lamUkObZAhVJeawKHSgmAIQQ_AUICSgA&biw=1280&bih=703&dpr=2) 。他们有你需要的一切，包在一个美味的可组合包里。这些组件要么用纯 JavaScript 定义，要么用 React 团队所谓的“JSX”定义。如果您决定使用 JSX(您很可能会使用，它非常标准，也是我们在本教程中使用的)，您将需要一些编译阶段来将您的 JSX 转换为 JavaScript，我们将在后面讨论。

React 构建用户界面如此方便的原因是，数据要么是从组件的父组件接收的，要么是包含在组件本身中的。在我们开始编写代码之前，让我们确保对组件有一个高层次的理解。

[![](img/c94006717a9c945f713bafe3c6b0fa9b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2b3EY2Co--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tylermcginnis.com/static/2df47642bc13bef365e96086d5b117c7-efdf2.png)

上面有一张我推特资料的照片。如果我们要在 React 中重新创建这个页面，我们将把不同的部分分解成不同的组件(突出显示)。请注意，组件内部可以有嵌套组件。我们可以将左边的组件(粉色)命名为`UserInfo`组件。在`UserInfo`组件中，我们有另一个组件(橙色)，我们可以称之为`UserImages`组件。这种父/子关系的工作方式是我们的`UserInfo`组件，或者说父组件，是它自己和`UserImages`组件(子组件)的数据的“状态”所在。如果我们想在子组件中使用父组件数据的任何部分，我们会将该数据作为属性传递给子组件。在这个例子中，我们将用户拥有的所有图像(当前存在于`UserInfo`组件中)传递给`UserImages`组件。我们稍后会更深入地讨论代码的细节，但我希望您能更全面地了解这里发生了什么。这种父/子层次结构使得管理我们的数据相对简单，因为我们确切地知道我们的数据位于何处，并且我们不应该在其他任何地方操纵这些数据。

下面的主题是我认为 React 的基本方面。如果你理解了它们和它们的目的，在阅读完这篇教程后，你将会处于一个很好的位置。

```
JSX — Allows us to write HTML like syntax which gets
transformed to lightweightJavaScript objects.

Virtual DOM — A JavaScript representation of the actual
DOM.

React.Component — The way in which you create a new component.

render (method) — Describes what the UI will look like for
the particular component.

ReactDOM.render — Renders a React component to a DOM node.

state — The internal data store (object) of a component.

constructor (this.state) - The way in which you establish
the initial state of a component.

setState — A helper method used for updating the state of a
component and re-rendering the UI

props — The data which is passed to the child component
from the parent component.

propTypes — Allows you to control the presence, or types of
certain props passed to the child component.

defaultProps — Allows you to set default props for your component.

Component LifeCycle
  - componentDidMount — Fired after the component mounted
  - componentWillUnmount — Fired before the component will unmount
  - getDerivedStateFromProps - Fired when the component mounts and
whenever the props change. Used to update the state of a
component when its props change

Events
  - onClick
  - onSubmit
  - onChange 
```

Enter fullscreen mode Exit fullscreen mode

我知道这看起来很多，但是您很快就会看到每一部分对于使用 React 构建健壮的应用程序是多么重要(当我说我希望这是一个全面的指南时，我也不是在开玩笑)。

此时，您应该在很高的层次上理解 React 是如何工作的。现在，让我们进入一些代码。

#### 创建你的第一个组件(JSX，虚拟 DOM，render，ReactDOM.render)

让我们继续构建我们的第一个 React 组件。

要创建一个 React 组件，您将使用一个 ES6 类。如果你不熟悉这些课程，你可以继续阅读下面的内容，或者你可以在这里阅读更多关于它们的内容

```
import React from 'react'
import ReactDOM from 'react-dom'

class HelloWorld extends React.Component {
  render() {
    return (
      <div>Hello World!</div>
    )
  }
}

ReactDOM.render(<HelloWorld />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们类中唯一的方法是`render`。每个组件都需要有一个呈现方法。渲染的原因是描述我们组件的 UI(用户界面)。所以在这个例子中，这个组件在屏幕上显示的文本是 Hello World！现在让我们看看 ReactDOM 在做什么。ReactDOM.render 接受两个参数。第一个参数是要呈现的组件，第二个参数是要呈现组件的 DOM 节点。(注意，我们使用 ReactDOM.render 而不是 React.render。这是 React .14 中的一个变化，使 React 更加模块化。当您认为 React 可以呈现比 DOM 元素更多的东西时，这是有意义的。在上面的例子中，我们告诉 React 获取 HelloWorld 组件，并将其呈现给 ID 为`root`的元素。由于我们前面谈到的 React 的父/子关系，您通常只需在应用程序中使用 ReactDOM.render 一次，因为通过呈现最父的组件，所有子组件也将被呈现。

现在，在这一点上，您可能会觉得将“HTML”放入 JavaScript 有点奇怪。自从你开始学习 web 开发，就有人告诉你应该把逻辑放在视图之外，也就是把 JavaScript 和 HTML 分开。这个范例很强大，但是它也有一些弱点。我不想让这个教程变得更长，试图说服你这个想法是朝着正确方向迈出的一步，所以如果这个想法仍然困扰着你，你可以查看[这个链接](https://www.slideshare.net/floydophone/react-preso-v2)。随着您对 React 了解越来越多，这种不安应该会很快平息。你在 render 方法中写的“HTML”实际上不是 HTML，而是 React 所说的“JSX”。JSX 只是允许我们编写类似 HTML 的语法，这些语法(最终)被转换成轻量级的 JavaScript 对象。React 然后能够获取这些 JavaScript 对象，并从中形成一个“虚拟 DOM”或实际 DOM 的 JavaScript 表示。这创造了一个双赢的局面，您可以利用 JavaScript 的强大功能获得模板的可访问性。

看看下面的例子，这就是你的 JSX 最终将被编译成的样子。

```
class HelloWorld extends React.Component {
  render() {
    return React.createElement("div", null, "Hello World");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 现在，您可以放弃 JSX -> JS 转换阶段，像上面的代码一样编写您的 React 组件
> ,但是正如您可以想象的，这将是相当棘手的。我不知道有谁不使用 JSX。关于 JSX 编译的更多信息，
> 查看 [React 元素 vs React 组件](https://tylermcginnis.com/react-elements-vs-react-components/)

到目前为止，我们还没有真正强调我们即将进入的这个新的虚拟 DOM 范例的重要性。React 团队采用这种方法的原因是，由于虚拟 DOM 是实际 DOM 的 JavaScript 表示，React 可以跟踪当前虚拟 DOM(在一些数据更改后计算)与先前虚拟 DOM(在一些数据更改前计算)之间的差异。 [React 然后隔离新旧虚拟 DOM 之间的变化，然后仅用必要的变化更新真实 DOM](https://www.youtube.com/watch?v=-DX3vJiqxm4)[。](https://www.youtube.com/watch?v=-DX3vJiqxm4)用更通俗的话来说，因为操作实际的 DOM 很慢，React 能够通过跟踪虚拟 DOM 并仅在必要时更新实际 DOM 来最小化对实际 DOM 的操作。([更多信息在此](https://www.youtube.com/watch?v=-DX3vJiqxm4))。通常 UI 有很多状态，这使得管理状态很困难。通过在每次发生任何状态变化时重新呈现虚拟 DOM，React 使得考虑应用程序处于什么状态变得更加容易。这个过程看起来像这样，

**某个用户事件改变了你的应用程序的状态→重新渲染虚拟 DOM - >用新的虚拟 DOM 来区别以前的虚拟 DOM***DOM***->只更新真实 DOM 进行必要的修改。**

因为有一个从 JSX 到 JS 的转换过程，你需要在开发过程中建立某种转换阶段。在本系列的第 2 部分中，我将介绍进行这种转换的 Webpack 和 Babel。

让我们回顾一下“React 最重要的部分”清单，看看我们现在在哪里。

```
<b>JSX — Allows us to write HTML like syntax which gets transformed
to lightweight JavaScript objects.</b>

<b>Virtual DOM — A JavaScript representation of the actual
DOM.</b>

<b>React.Component — The way in which you create a new component.</b>

<b>render (method) — Describes what the UI will look like for
the particular component.</b>

<b>ReactDOM.render — Renders a React component to a DOM node.</b>

state — The internal data store (object) of a component.

constructor (this.state) - The way in which you establish
the initial state of a component.

setState — A helper method used for updating the state of a
component and re-rendering the UI

props — The data which is passed to the child component
from the parent component.

propTypes — Allows you to control the presence, or types of
certain props passed to the child component.

defaultProps — Allows you to set default props for your component.

Component LifeCycle
  - componentDidMount — Fired after the component mounted
  - componentWillUnmount — Fired before the component will unmount
  - getDerivedStateFromProps - Fired when the component mounts and
whenever the props change. Used to update the state of a
component when its props change

Events
  - onClick
  - onSubmit
  - onChange 
```

Enter fullscreen mode Exit fullscreen mode

我们进展顺利。粗体部分是我们已经介绍过的内容，你
至少应该能够解释这些组件如何适应
React 生态系统。

**向组件添加状态(state)**

名单上的下一个是`state`。前面我们谈到了管理用户
界面是多么困难，因为它们通常有许多不同的状态。
这个领域是 React 真正开始大放异彩的地方。每个组件都能够管理自己的状态，并在需要时将其状态传递给子组件。回到前面的 Twitter 示例，组件`UserInfo`(上面用粉色突出显示)负责管理用户信息的状态(或数据)。如果另一个组件也需要这个状态/数据，但是这个状态不是`UserInfo`组件的直接子组件，那么你可以创建另一个组件作为`UserInfo`和另一个组件(或者两个组件都需要这个状态)的直接父组件，然后你可以把这个状态作为道具传递给子组件。换句话说，如果你有一个多组件层次结构，一个公共的父组件应该管理状态并通过 props 传递给它的子组件。

让我们来看一个使用自身内部状态的示例组件。

```
class HelloUser extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      username: 'tylermcginnis'
    }
  }
  render() {
    return (
      <div>
        Hello {this.state.username}
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这个例子中引入了一些新的语法。您将注意到的第一个方法是构造函数方法。根据上面的定义，构造函数方法是“设置组件状态的方式”。换句话说，你放在构造函数内部的任何数据都将是组件状态的一部分。在上面的代码中，我们告诉我们的组件，我们希望它跟踪一个`username`。这个`username`现在可以通过`{this.state.username}`在我们的组件中使用，这正是我们在渲染方法中所做的。

关于状态，最后要说的是，我们的组件需要能够修改它自己的内部状态。我们用一个叫做 **setState** 的方法来做这件事。还记得我们之前讨论的每当数据改变时虚拟 dom 的重新呈现吗？

**信号通知我们的应用程序一些数据已经改变→重新渲染虚拟 DOM - >用新的虚拟 DOM 区分以前的虚拟 DOM - >只更新有必要改变的真实 DOM。**

“通知我们的应用程序一些数据已经改变的信号”实际上只是 setState。每当调用 setState 时，虚拟 DOM 会重新呈现，diff 算法会运行，而真实 DOM 会根据必要的更改进行更新。

顺便说一下，当我们在下面的代码中引入 setState 时，我们也将引入列表中的一些事件。一石二鸟。

因此，在下一个代码示例中，我们现在将有一个输入框，每当有人在其中键入内容时，它将自动更新我们的状态并更改用户名。

```
class HelloUser extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      username: 'tylermcginnis'
    }

    this.handleChange = this.handleChange.bind(this)
  }
  handleChange (e) {
    this.setState({
      username: e.target.value
    })
  }
  render() {
    return (
      <div>
        Hello {this.state.username} <br />
        Change Name:
        <input
          type="text"
          value={this.state.username}
          onChange={this.handleChange}
        />
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们已经介绍了一些更多的东西。首先是`handleChange`法。每当用户在输入框中输入内容时，这个方法都会被调用。当调用`handleChange`时，它将调用`setState`用输入框中输入的内容重新定义我们的用户名。记住，无论何时调用`setState`，React 都会创建一个新的虚拟 DOM，执行 diff，然后更新真实 DOM。

现在让我们看看我们的渲染方法。我们添加了一个包含输入字段的新行。输入字段的类型显然是`text`。该值将是我们的用户名的值，它最初是在 getInitialState 方法中定义的，并将在`handleChange`方法中更新。请注意，有一个您可能从未见过的新属性，`onChange`。`onChange`是一个 React 东西，每当输入框中的值改变时，它都会调用你指定的任何方法，在本例中，我们指定的方法是`handleChange`。

上面代码的过程大概是这样的。

用户在输入框中输入内容→调用 handleChange 我们的组件的状态被设置为一个新值→ React 重新呈现虚拟 DOM → React 区分更改→更新真实 DOM。

稍后当我们讨论道具时，我们会看到一些更高级的处理状态的用例。

我们快到了。如果你不能解释下面用粗体显示的项目，请重新阅读该部分。真正学习的一个技巧是反应，不要让被动阅读给你一种错误的安全感，你实际上知道发生了什么，可以重新创建我们正在做的事情。前往 CodeSandbox，尝试重新创建(或创建自己的)组件，而不要看我已经做了什么。这是你真正开始学习如何用 React 构建的唯一方法。这适用于本教程和接下来的内容。

```
<b>JSX — Allows us to write HTML like syntax which gets transformed
to lightweight JavaScript objects.</b>

<b>Virtual DOM — A JavaScript representation of the actual
DOM.</b>

<b>React.Component — The way in which you create a new component.</b>

<b>render (method) — Describes what the UI will look like for the particular component.</b>

<b>ReactDOM.render — Renders a React component to a DOM node.</b>

<b>state — The internal data store (object) of a component.</b>

<b>constructor (this.state) - The way in which you establish the initial state of a component.</b>

<b>setState — A helper method used for updating the state of a
component and re-rendering the UI</b>

props — The data which is passed to the child component from the parent component.

propTypes — Allows you to control the presence, or types of certain props passed to the child component.

defaultProps — Allows you to set default props for your component.

Component LifeCycle
  - componentDidMount — Fired after the component mounted
  - componentWillUnmount — Fired before the component will unmount
  - getDerivedStateFromProps - Fired when the component mounts and
whenever the props change. Used to update the state of a
component when its props change

Events
  - onClick
  - onSubmit
  - onChange 
```

Enter fullscreen mode Exit fullscreen mode

**从父组件接收状态(props，propTypes，getDefaultProps)**

我们已经讨论过几次道具了，因为没有它们很难真正做很多事情。根据我们上面的定义，props 是从父组件传递给子组件的数据。这使得我们的 React 架构非常简单。处理需要使用特定数据的最高父组件中的状态，如果您有一个也需要该数据的子组件，则将该数据作为 props 向下传递。

这里有一个非常基本的使用道具的例子。

```
class HelloUser extends React.Component {
  render() {
    return (
      <div> Hello, {this.props.name}</div>
    )
  }
}

ReactDOM.render(<HelloUser name="Tyler"/>, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在第 9 行，我们有一个名为 name 的属性，其值为“Tyler”。现在在我们的组件中，我们可以使用`{this.props.name}`来获取“Tyler”。

让我们看一个更高级的例子。我们现在有两个部分。一个父母，一个孩子。父母将记录状态，并将状态的一部分作为道具传递给孩子。让我们首先来看看父组件。

父组件:

```
class FriendsContainer extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      name: 'Tyler McGinnis',
      friends: ['Jake Lingwall', 'Sarah Drasner', 'Merrick Christensen']
    }
  }
  render() {
    return (
      <div>
        <h3> Name: {this.state.name} </h3>
        <ShowList names={this.state.friends} />
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个组件中，真的没有太多我们以前没有见过的东西。我们有一个初始状态，我们把这个初始状态的一部分传递给另一个组件。大部分新代码将来自这个子组件，所以让我们仔细看看。

子组件:

```
class ShowList extends React.Component {
  render() {
    return (
      <div>
        <h3> Friends </h3>
        <ul>
          {this.props.names.map((friend) => <li>{friend}</li>)}
        </ul>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，从我们的 render 方法返回的代码代表了真正的 DOM 应该是什么样子。如果你不熟悉 **Array.prototype.map** ，这段代码可能看起来有点不可靠。map 所做的就是创建一个新数组，对数组中的每一项调用回调函数，并用对每一项调用回调函数的结果填充新数组。例如，

```
const friends = ['Jake Lingwall', 'Sarah Drasner', 'Merrick Christensen'];
const listItems = friends.map((friend) => {
  return "<li> " + friend + "</li>";
});

console.log(listItems);
// ["<li> Jake Lingwall</li>", "<li> Sarah Drasner</li>", "<li> Merrick Christensen</li>"]; 
```

Enter fullscreen mode Exit fullscreen mode

上面的 console.log 返回

`["<li> Jake Lingwall</li>", "<li> Murphy
Randall</li>", "<li> Merrick Christensen</li>"]`

。

请注意，所发生的是我们
创建了一个新数组，并在原始数组中的每一项上添加了

`<li> </li>`

。

map 的伟大之处在于它非常适合 React(并且内置于 JavaScript 中)。所以在上面的子组件中，我们映射了名字，将每个名字包装在一对
中

```
 tags, and saving that to our listItems variable. Then, our render method returns an unordered list with all of our friends.

Let’s look at one more example before we stop talking about props. It’s important to understand that wherever the data lives, is the exact place you want to manipulate that data. This keeps it simple to reason about your data. All getter/setter method for a certain piece of data will always be in the same component where that data was defined. If you needed to manipulate some piece of data outside where the data lives, you’d pass the getter/setter method into that component as props. Let’s take a look at an example like that.

```javascript
class FriendsContainer extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      name: 'Tyler McGinnis',
      friends: [
        'Jake Lingwall',
        'Sarah Drasner',
        'Merrick Christensen'
      ],
    }

    this.addFriend = this.addFriend.bind(this)
  }
  addFriend(friend) {
    this.setState((state) => ({
      friends: state.friends.concat([friend])
    }))
  }
  render() {
    return (
      <div>
        <h3> Name: {this.state.name} </h3>
        <AddFriend addNew={this.addFriend} />
        <ShowList names={this.state.friends} />
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 🚨。注意，在我们的 addFriend 方法中，我们引入了一种新的方法来调用 setState。我们不是传递给它一个对象，而是传递给它一个函数，然后传递给`state`。每当您基于以前的状态设置组件的新状态时(就像我们对 friends 数组所做的那样)，您都希望向 setState 传递一个函数，该函数接收当前状态并返回数据以与新状态合并。

```
class AddFriend extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      newFriend: ''
    }

    this.updateNewFriend = this.updateNewFriend.bind(this)
    this.handleAddNew = this.handleAddNew.bind(this)
  }
  updateNewFriend(e) {
    this.setState({
      newFriend: e.target.value
    })
  }
  handleAddNew() {
    this.props.addNew(this.state.newFriend)
    this.setState({
      newFriend: ''
    })
  }
  render() {
    return (
      <div>
        <input
          type="text"
          value={this.state.newFriend}
          onChange={this.updateNewFriend}
        />
        <button onClick={this.handleAddNew}> Add Friend </button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class ShowList extends React.Component {
  render() {
    return (
      <div>
        <h3> Friends </h3>
        <ul>
          {this.props.names.map((friend) => {
            return <li> {friend} </li>
          })}
        </ul>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到上面的代码与前面的例子基本相同，除了现在我们可以在我们的朋友列表中添加一个名字。注意我是如何创建一个新的 AddFriend 组件来管理我们将要添加的新朋友的。这样做的原因是因为父组件(FriendContainer)不关心您添加的新朋友，它只关心您所有的朋友作为一个整体(friends 数组)。然而，因为我们坚持只从关心它的组件操纵数据的规则，所以我们已经将 AddFriend 方法作为 from 传递到我们的 addFriend 组件中，并且一旦 handleAddNew 方法被调用，我们就用 new friend 调用它。

在这一点上，我建议你在卡住 3-4 分钟后，使用上面的代码作为指导，尝试自己重新创建同样的功能。

在我们离开道具之前，我想再介绍两个关于道具的反应特性。它们是**proptype**和 **defaultProps。**我不会在此赘述，因为两者都很简单。

**prop-types** 允许你控制传递给子组件的某些属性的存在或类型。使用属性类型，您可以指定某些属性是必需的，或者某些属性是特定的类型。

> 从 React 15 开始，React 包中不再包含 PropTypes。您需要通过运行`npm install prop-types`来单独安装它。

**defaultProps** 允许你为某些属性指定一个缺省(或备份)值，以防那些属性永远不会被传递到组件中。

从早些时候到现在，我已经修改了我们的组件，使用 propTypes，要求 AddFriend 是一个函数，并且它被传递到 addFriend 组件中。我还使用 defaultProps 指定，如果 ShowList 组件没有朋友数组，它将默认为一个空数组。

```
import React from 'react'
import PropTypes from 'prop-types'

class AddFriend extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      newFriend: ''
    }
  }
  updateNewFriend(e) {
    this.setState({
      newFriend: e.target.value
    })
  }
  handleAddNew() {
    this.props.addNew(this.state.newFriend)
    this.setState({
      newFriend: ''
    })
  }
  render() {
    return (
      <div>
        <input type="text" value={this.state.newFriend} onChange={this.updateNewFriend} />
        <button onClick={this.handleAddNew}> Add Friend </button>
      </div>
    )
  }
}

AddFriend.propTypes: {
  addNew: PropTypes.func.isRequired
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class ShowList extends React.Component {
  render() {
    return (
      <div>
        <h3> Friends </h3>
        <ul>
          {this.props.names.map((friend) => {
            return <li> {friend} </li>
          })}
        </ul>
      </div>
    )
  }
}

ShowList.defaultProps = {
  names: []
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们已经到了第一节课的最后阶段。让我们看看我们的指南，看看我们还剩下什么。

```
<b>JSX — Allows us to write HTML like syntax which gets transformed
to lightweight JavaScript objects.</b>

<b>Virtual DOM — A JavaScript representation of the actual
DOM.</b>

<b>React.Component — The way in which you create a new component.</b>

<b>render (method) — Describes what the UI will look like for
the particular component.</b>

<b>ReactDOM.render — Renders a React component to a DOM node.</b>

<b>state — The internal data store (object) of a component.</b>

<b>constructor (this.state) - The way in which you establish
the initial state of a component.</b>

<b>setState — A helper method used for updating the state of a
component and re-rendering the UI</b>

<b>props — The data which is passed to the child component
from the parent component.</b>

<b>prop-types — Allows you to control the presence, or types of
certain props passed to the child component.</b>

<b>defaultProps — Allows you to set default props for your component.</b>

Component LifeCycle
  - componentDidMount — Fired after the component mounted
  - componentWillUnmount — Fired before the component will unmount
  - getDerivedStateFromProps - Fired when the component mounts and
whenever the props change. Used to update the state of a
component when its props change

<b>Events
  - onClick
  - onSubmit
  - onChange
</b> 
```

Enter fullscreen mode Exit fullscreen mode

我们很接近了！

**组件生命周期**

您制作的每个组件都有自己的生命周期事件，这些事件对各种事情都很有用。例如，如果我们想在初始渲染时发出一个 ajax 请求并获取一些数据，我们应该在哪里做呢？或者，如果我们想在道具改变时运行一些逻辑，我们该怎么做呢？不同的生命周期事件是这两个问题的答案。让我们把它们分解开来。

```
class App extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      name: 'Tyler McGinnis'
    }
  }
  componentDidMount(){
    // Invoked once the component is mounted to the DOM
    // Good for making AJAX requests
  }
  static getDerivedStateFromProps(nextProps, prevState) {
    // The object you return from this function will
    // be merged with the current state.
  }
  componentWillUnmount(){
    // Called IMMEDIATELY before a component is unmounted
    // Good for cleaning up listeners
  }
  render() {
    return (
      <div>
        Hello, {this.state.name}
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**componentDidMount** -在初始渲染后调用一次。因为在调用该方法时已经调用了组件，所以如果需要的话，您可以访问虚拟 DOM。你可以通过调用 **this.getDOMNode()** 来实现。这是一个生命周期事件，您将发出 AJAX 请求来获取一些数据。*

**component will unmount**——这个生命周期在组件从 DOM 中卸载之前立即被调用。这是你可以做必要清理的地方。

getDerivedStateFromProps -有时你需要根据传入的属性更新组件的状态。这是一种生命周期方法。它将被传递道具和状态，你返回的对象将与当前状态合并。

如果你能坚持到现在，干得好。我希望本教程对您有所帮助，并且您现在至少对 React 感到比较舒服了。

> 要更深入地了解 React 的基础知识，请查看我们的 [React 基础知识](https://tylermcginnis.com/courses/react-fundamentals/)课程。