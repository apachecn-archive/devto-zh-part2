# 帮助您理解和创建 ReactJS 应用程序的快速指南

> 原文：<https://dev.to/adityasridhar/a-quick-guide-to-help-you-understand-and-create-reactjs-apps-1hjd>

**本帖原载于[medium.freecodecamp.org](https://medium.freecodecamp.org/quick-guide-to-understanding-and-creating-reactjs-apps-8457ee8f7123)T3】**

# 这篇文章分为两部分

1.  第一部分演示了如何使用“create-React-app”CLI 创建简单的 React 应用程序，并解释了项目结构。
2.  第二部分解释了我在 Github 上发布的一个现有代码。这段代码演示了组件的使用，组件之间的通信，进行 HTTP 调用和 React Bootstrap(为 React 编写的 Bootstrap)。

# 第一部分

## 如果不存在，请安装 NodeJS

需要 NodeJS，因为 React 所需的库是使用节点包管理器(npm)下载的。参照[https://nodejs.org/en/](https://nodejs.org/en/)安装节点。

## 安装 create-react-app 节点包

**create-react-app** 节点包帮助建立一个 react 项目。使用以下命令全局安装 create react-app 节点包。

```
npm install -g create-react-app 
```

## 创建项目

可以使用 **create-react-app** 创建项目。使用以下命令创建项目。

```
npx create-react-app first-react-app 
```

**first-react-app** 是应用程序的名称。上面的命令创建了一个名为 **first-react-app** 的文件夹，这是项目文件夹。为了测试是否一切都已正确设置，进入项目文件夹并使用以下命令启动应用程序。

```
cd first-react-app
npm start 
```

进入你的浏览器，点击以下网址 **localhost:3000** 。您应该能够看到您的应用程序正在运行。该应用程序在您的浏览器中将如下所示:

[![Output](../Images/d49981a3d2fa948c9ef1eb15ed8c89ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bVaOAHSs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gnpno7g8xjatppu9f3yd.jpg)

## 解释了基本文件夹结构

当您创建项目时，您会注意到它创建了一堆文件。在这里，我将列出一些你应该知道的重要文件和文件夹。

1.  **package.json** :这个文件有需要的节点依赖列表。
2.  **public/index.html** :当应用程序启动时，这是加载的第一个页面。这将是整个应用程序中唯一的 html 文件，因为 React 通常是用 **JSX** 编写的，我将在后面介绍。另外，这个文件有一行代码`<div id=”root”></div>`。这一行非常重要，因为所有的应用程序组件都被加载到这个 **div** 中。
3.  **src/index.js** :这是 index.html 对应的 javascript 文件。这个文件有下面一行非常重要的代码。`ReactDOM.render(<App />, document.getElementById(‘root’));`
4.  上面的代码行告诉我们 **App** 组件(很快将包含 App 组件)必须被加载到一个 id 为 **root** 的 html 元素中。这不过是出现在**index.html 中的 **div 元素**。**
5.  **src/index . css**:index . js 对应的 CSS 文件。
6.  **src/App.js** :这是 **App** 组件的文件。 **App** 组件是 React 中的主要组件，它充当所有其他组件的容器。
7.  **src/App.css** :这是 **App** 组件对应的 css 文件
8.  **build:** 这是存储构建文件的文件夹。React 应用程序可以使用 JSX 或普通的 JavaScript 本身来开发，但是使用 JSX 无疑会使开发人员更容易编写代码:)。但是浏览器不理解 JSX。所以 JSX 需要在部署前转换成 javascript。这些转换后的文件在捆绑和缩小后存储在构建文件夹中。为了查看构建文件夹，运行以下命令

```
npm run build 
```

## 创建组件

React 中的组件执行特定的功能。应用程序只不过是组件的集合。每个组件可以有多个子组件，并且这些组件可以相互通信。

现在让我们创建一个 React 组件。

在 **src** 文件夹中创建一个名为 **FirstComponent.js** 的文件，并将下面的代码复制到 **FirstComponent.js.**

```
import React, {Component} from 'react';

export default class FirstComponent extends Component {

constructor(props) {
    super(props)
    }

render() {
    const element = (<div>Text from Element</div>)
    return (<div className="comptext">
    <h3>First Component</h3>
        {this.props.displaytext}
        {element}
    </div>)
    }
} 
```

1.  组件名为**第一组件**，由文件名以及语句`export default class FirstComponent extends Component`表示
2.  构造函数中的 **props** 属性将包含作为输入传递给该组件的所有参数。
3.  **render():** 这个函数的返回值被渲染(显示)在屏幕上。每当 render()函数被调用时，屏幕被重新渲染。这通常由应用程序自动完成。这个函数中与 html 非常相似的代码就是 **JSX。**

## JSX

JSX 看起来非常类似于 HTML，但却拥有 javascript 的全部功能。在这里，我将解释 JSX 代码和它试图做什么。

```
render() {
    const element = (<div>Text from Element</div>)
    return (<div className="comptext">
    <h3>First Component</h3>
        {this.props.displaytext}
        {element}
    </div>)
    } 
```

第一行`const element = (<div>Text from Element</div>)`创建一个 div 元素，并将其赋给一个名为 element 的常量。你看到的这个奇怪的语法就是 **JSX。**

在 Return 语句中，您会看到以下代码语法。

```
<div className="comptext">
    <h3>First Component</h3>
        {this.props.displaytext}
        {element}
</div> 
```

这里 **className** 用于指向一个 CSS 类。`<h3>First Component</h3>`只是普通的 **html** 语法。`{this.props.displaytext}`用于从 props 中访问一个名为 displaytext 的属性(因此每当调用该组件时，displaytext 都作为输入传递)。这里 **displaytext** 只是我给的一个自定义名称。`{element}`是创建的常量，它包含 div 元素。

## 使用组件

第一个组件已经创建，但尚未在任何地方使用。让我们将**第一个组件**添加到 **App** 组件中。下面是 **App.js** 修改后的代码

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import FirstComponent from './FirstComponent'
class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">Welcome to React</h1>
        </header>
        <p className="App-intro">
          To get started, edit <code>src/App.js</code> and save to reload.
        </p>
        <FirstComponent displaytext="First Component Data"/>
      </div> );
  }
}
export default App; 
```

**FirstComponent** 需要先导入 App 组件，这是在`import FirstComponent from ‘./FirstComponent’`行完成的

然后使用行`<FirstComponent displaytext=”First Component Data”/>`将**第一组件**添加到 **App** 组件

这里 **displaytext** 作为属性传递给 FirstComponent。

现在您可以使用命令`npm start`运行应用程序

您应该会在浏览器中看到以下结果。

[![Output](../Images/9f5e7c19a09bcc306fd9ed38f074854e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pwVgVT6w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/af3i0qmhzpiah61uuhhk.jpg)

## 祝贺😄

现在您知道了如何创建 React 应用程序，以及如何创建和使用 React 组件。你对 JSX 也有所了解:)

下一部分将解释 Github 中现有的 React 代码。第 2 部分的代码不同于我们在第 1 部分中编写的代码。

# 第二部分

## 密码

下面的代码是在这里解释，所以克隆到你的电脑回购。回购有关于如何在你的电脑上克隆和设置代码的说明。

## [https://github.com/aditya-sridhar/simple-reactjs-app](https://github.com/aditya-sridhar/simple-reactjs-app)

## 应用程序 URL

要查看最终的应用程序，您可以点击以下 URL。这将给出一个关于应用程序试图做什么的好主意

## [https://aditya-sridhar.github.io/simple-reactjs-app](https://aditya-sridhar.github.io/simple-reactjs-app)

该应用程序在移动屏幕上会是这样的

[![Output](../Images/74e0eeb4c2a09c8cc8524d352e41cbe0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--huGrqBts--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/llipov2aypjlk2l6fxfz.jpg)

## 这个应用程序做什么

该应用程序显示客户列表。选择客户后，它会显示所选客户的详细信息。这是一个单页应用程序(SPA)。React 最适合单页面应用。单页应用程序在一个页面中显示所有内容。

## 解释了应用程序结构

## 客户组件

该组件显示客户列表。这对应于文件 **src/Customers.js** 。该组件具有以下构造函数。

```
constructor(props) {
    super(props)
    this.state = {
        selectedCustomer: 1
    }
} 
```

道具已经解释过了。但是在这里你也看到了**这个州**。每当状态改变时，组件被重新呈现。这里的**状态**有一个名为 **selectedCustomer** 的参数，用于跟踪哪个客户被选中。如果 **selectedCustomer** 发生变化，那么组件及其子组件将被重新渲染。该构造函数仅用于设置**道具**和**状态。**此外，**道具**永远不应该在组件内编辑**。**

您注意到的下一件事是下面的代码。

```
componentDidMount() {
    this.getCustomerData();
} 
```

**componentDidMount()** 是一个函数，组件一渲染就被调用。因此，这可以用来设置一些初始值以及加载数据。这里它调用了一个名为 **getCustomerData()** 的函数

你看到的下一段代码是

```
getCustomerData() {
    axios.get('assets/samplejson/customerlist.json').then(response => {
        this.setState({customerList: response})
    })
}; 
```

此函数 **getCustomerData()** 进行 HTTP 调用，从**assets/sample JSON/customer list . JSON 中读取包含客户列表的示例 JSON。**在成功获得响应后，通过将**响应**分配给 **customerList，系统的状态被更改。你可能想知道为什么我们从来没有在构造函数中添加 customerList。原因是您可以在代码的任何地方动态地将参数添加到状态中。唯一的要求是在构造函数中至少要定义一个空状态。**

这里的 **axios** 库用于进行 HTTP 调用。我在参考资料部分提供了 axios 的文档。

下一个函数是 **render()** 函数，它返回哪些元素必须呈现在屏幕上。render 函数的主要关注点是

```
<Button bsStyle="info" onClick={() => this.setState({selectedCustomer: customer.id})}>

Click to View Details

</Button> 
```

列表中的每个客户都有一个名为**的按钮，点击查看详情**。上面的代码片段告诉我们，每当点击按钮时，就会将 **selectedCustomer** 的状态更改为所选客户的 id。由于这里的状态发生了变化，组件及其子组件将被重新呈现。

另一个重要的代码片段是

```
<CustomerDetails val={this.state.selectedCustomer}/> 
```

这个代码片段告诉我们 **CustomerDetails** 是 **Customers** 组件的子组件，并且还将 **selectedCustomer** id 作为输入传递给 **CustomerDetails** 组件

## 客户详细信息组件

该组件显示所选客户的详细信息。此处将解释该组件的一些重要代码片段:

```
componentDidUpdate(prevProps) {

//get Customer Details only if props has changed
//(props is the input) 
    if (this.props.val !== prevProps.val) {
        this.getCustomerDetails(this.props.val)
    }
} 
```

**componentDidUpdate()** 每当组件被重新渲染时，函数被调用。在这里，如果在组件重新呈现时该组件的输入发生了变化，我们将调用 **getCustomerDetails()** 函数。传递给 **getCustomerDetails()** 函数的输入是 **this.props.val** 。 **this.props.val** 依次从 **Customers** 组件获取其值(selectedCustomer 作为输入传递给 this)。要知道输入是否已经改变，使用的代码片段是`this.props.val !== prevProps.val`

```
getCustomerDetails(id) {
    axios.get('assets/samplejson/customer' + id +     '.json').then(response => {
        this.setState({customerDetails: response})
    })
}; 
```

**getCustomerDetails()** 函数调用 HTTP 来获取包含客户详细信息的示例 json。 **id** 参数用于知道需要哪些客户的详细信息。 **id** 无非是 **this.props.val.** 当成功接收到响应时，通过将**响应**分配给**客户详细信息**来改变该组件的状态。

这个组件的 **render()** 函数非常简单明了，所以这里不再赘述

# 参考

**创建-反应-应用:**参考 https://github.com/facebook/create-react-app了解使用创建-反应-应用可以做什么

**ReactJS:** 参考 https://reactjs.org/了解 ReactJS 的概念。文档非常好。

**React Bootstrap:** 参考【https://react-bootstrap.github.io/getting】启动/简介/ 了解如何使用 React Bootstrap

**axios:** 参考 https://www.npmjs.com/package/axios[了解更多关于如何使用 axios 库进行 HTTP 请求](https://www.npmjs.com/package/axios)

# 再次祝贺😄

现在你知道了如何使用组件，如何从一个父组件到一个子组件进行通信，以及一些关于渲染的知识

基本概念已经涵盖在这个职位，希望这是有帮助的😄

### 请随时在 [LinkedIn](https://www.linkedin.com/in/aditya1811/) 与我联系，或者在 [twitter](https://twitter.com/adityasridhar18) 关注我。

### 如果你喜欢这篇文章，你可以去我的网站[https://adityasridhar.com](https://adityasridhar.com)看看其他类似的文章