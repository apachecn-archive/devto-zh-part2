# 在反应中传递数据与支柱钻孔

> 原文：<https://dev.to/mcapoz/passing-data-in-react-with-propdrilling-454p>

* * *

[![Drill](../Images/1d78561a4f6a818cf2cda331496f4273.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bNKSD4st--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m24vqp67i5vunluip6uy.jpg)

React 生态系统提供了在客户端应用程序中传递数据和管理状态的多种方式。开发人员可能会根据他们正在开发的应用程序的大小和复杂性，在 prop drilling、redux、MobX 和 React 16 中引入的新上下文 API 之间进行选择。

开发人员可以使用 prop drilling(或 threading)将数据从组件层次结构中较高的组件传递到较低的子组件。它允许开发人员在小型应用程序中访问组件层次结构的不同级别的状态，这些应用程序不够大或复杂，不足以保证第三方状态管理解决方案或上下文。

* * *

Dan Abramov 建议开发人员将有状态和无状态组件分开，以保持关注点的分离。他建议我们创建包含许多表示组件的顶级容器组件。容器组件管理应用程序的状态，并将其传递给表示组件进行呈现。

让我们先来看看在 React 中道具是如何从父组件传递到子组件的。

#### 基本支柱钻孔

```
// <Mother /> is a container component that holds the 
// application's state.
// In this case, <Mother /> holds the family's lastName.

class Mother extends React.Component {
  state = {
    lastName: 'Sanchez'
  }

  render () {
    // Mother passes the lastName down one
    // level to the <Child /> 
    <Child lastName={this.state.lastName} />
  }
}

// <Child /> is a presentational component that 
// displays the lastName.
const Child = ({ lastName }) => <p>{lastName}>/p> 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`<Mother />`是保存应用程序状态的容器组件。`<Mother />`将姓氏作为道具传递给`<Child />`。`<Child />`然后显示姓氏。当应用程序的规模和复杂性增加时，这种模式会变得更加复杂。

#### 更复杂的支柱钻孔

```
const App = () => <Grandmother />

class Grandmother extends React.Component {
  state = {
    lastName: "Sanchez"
  }

  render() {
    return <Mother lastName={this.state.lastName} />
  }
}

const Mother = ({ lastName }) => {
  return <Child lastName={lastName} />
}

const Child = ({ lastName }) => {
  return <p>{lastName}</p>
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，祖母组件管理应用程序的状态。它在其状态中保存了 lastName 属性。子组件显示姓氏。在这种情况下，孩子只知道姓氏，因为道具已经通过组件树从祖母传递(钻取)到母亲再到孩子。这就像一场电话游戏。

[![Family](../Images/a1fb2e588e5660d745af19c966e473f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SOIfjV4G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ztx3uqe9w44q12c7oytz.jpg)

正确钻取的主要好处是，即使状态发生变化，它也会用新的状态更新它的所有子节点。例如，如果祖母移民到美国，移民官把她的姓从桑切斯改成了史密斯。

#### 支柱钻孔有状态变化

```
class Grandmother extends React.Component {
  state = {
    lastName: 'Sanchez'
  }

  // When this function is called, the 
  // Grandmother's last name is updated
  immigrateTo = (country, newLastName) => {
    this.setState({ lastName: newLastName })
  }

  componentDidMount() {
    this.immigrateTo('US', 'Smith')
  }

  render () {
    // Even though the Grandmother's last name
    // was Sanchez, once her name is changed, 
    // the Mother inherits the name "Smith."
    <Mother lastName={this.state.lastName} />
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

随着您的应用程序继续增长，您可能会注意到，当您在层次结构中向下传递它们时，您开始更改您的道具的名称。确定数据来自哪里将变得更加困难，调试将变得更加困难。您还会发现，您正在通过没有理由知道该信息的组件传递数据！

当您不容易确定数据的来源时，您应该考虑在您的应用程序中引入一个替代的状态管理解决方案。如果你用的是 React 15，可以实现 redux 或者 MobX 之类的东西。在 React 16 中，还可以考虑使用上下文。