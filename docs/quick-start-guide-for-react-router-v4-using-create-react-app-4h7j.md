# 使用 create-react-app 的 react 路由器 v4 快速入门指南

> 原文：<https://dev.to/nodefiend/quick-start-guide-for-react-router-v4-using-create-react-app-4h7j>

# 让我们直入主题:

> 为了简洁起见，我将把所有的部分都构建到`app.js`中，但是很自然地你会想要把它们分开。

```
npm install create-react-app /folder-location-of-my-app 
```

Enter fullscreen mode Exit fullscreen mode

导航到文件夹并将`react-router-dom`安装到您的`package.json`中

`cd ./folder-location-of-my-app`

`npm install --save react-router-dom`

现在您已经有了 react 路由器 dom，让我们打开默认的 app.js 文件:

## **app.js**

```
import { BrowserRouter as Router, Route, Link } from 'react-router-dom'; 
```

Enter fullscreen mode Exit fullscreen mode

转到渲染函数，将最外面的 div 放入`<Router>` :

```
 class App extends Component {
  render() {
    return (
      <Router>
        <div className="App">
          <header className="App-header">
            ...
        </div>
      </Router>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们有了合适的`Router`,让我们定义简单的路线:

react router 将在 JSX 中渲染我们的组件视图，无论您决定将它们放在哪里。

让我们把它们放在标题下面

```
 ...
          <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <h1 className="App-title">Welcome to React</h1>
          </header>
          <div>
            <Route exact path="/" component={Home} />
            <Route exact path="/about" component={About} />
            <Route exact path="/code" component={Code} />
            <Route exact path="/contact" component={Contact} />
            <Route exact path="/presence" component={info} />
          </div>
        </div>
      </Router>
     ... 
```

Enter fullscreen mode Exit fullscreen mode

传入一串您想要路由到的确切路径，并传入一个组件来设置每个路由。

现在我们已经设置了路线，我们需要设置将“链接”到每条路线的按钮。

让我们创建一个纯函数组件，并将其命名为`<MainMenu/>`

我们可以将菜单按钮添加到标题中，这样它们会更漂亮、更明显:

```
 ...
     <header className="App-header">
       <img src={logo} className="App-logo" alt="logo" />
         <h1 className="App-title">Welcome to React</h1>
            <MainMenu/>
     </header>
    ... 
```

Enter fullscreen mode Exit fullscreen mode

我们的`<MainMenu/>`组件将由 react-routers `<Link/>`组件包装的一系列按钮组成，这将把它的孩子变成一个链接，该链接将根据你用`to=`属性传入的内容改变 react 路由器的“状态”。

```
 const MainMenu = () => {

    <div>
      <Link to="/">
        <button>home</button>
      </Link>
      <Link to="/about">
        <button>About</button>
      </Link>
      <Link to="/code">
        <button>code</button>
      </Link>
      <Link to="/code">
        <button>contact</button>
      </Link>
      <Link to="/info">
        <button>info</button>
      </Link>
    </div> 
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们已经有了`<Router/>`、`<Route/>`和`<Link/>`，剩下唯一要做的就是设置我们的每个视图:

我们可以用一些更纯粹的函数来快速设置它们

```
 const Home = () => (
  <div>
    Home
  </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们完整的`app.js`文件将如下所示:

```
 import React, { Component } from 'react';
    import logo from './logo.svg';
    import './App.css';
    import { BrowserRouter as Router, Route, Link } from 'react-router-dom';

    const Home = () => (
      <div>
        Home
      </div>
    )

    const About = () => (
      <div>
        About
      </div>
    )

    const Code = () => (
      <div>
        Code
      </div>
    )

    const Contact = () => (
      <div>
        Contact
      </div>
    )

    const info = () => (
      <div>
        info
      </div>
    )

const MainMenu = () => {
  return (
    <div>
      <Link to="/">
        <button>home</button>
      </Link>
      <Link to="/about">
        <button>About</button>
      </Link>
      <Link to="/code">
        <button>code</button>
      </Link>
      <Link to="/contact">
        <button>contact</button>
      </Link>
      <Link to="/info">
        <button>info</button>
      </Link>
    </div>
  );
};

    class App extends Component {
      render() {
        return (
          <Router>
            <div className="App">
              <header className="App-header">
                <img src={logo} className="App-logo" alt="logo" />
                <h1 className="App-title">Welcome to React</h1>
                <MainMenu />
              </header>
              <div>
                <Route exact path="/" component={AppViews.Home} />
                <Route exact path="/about" component={AppViews.About} />
                <Route exact path="/code" component={AppViews.Code} />
                <Route exact path="/contact" component={AppViews.Contact} />
                <Route exact path="/presence" component={AppViews.Presence} />
              </div>
            </div>
          </Router>
        );
      }
    }

    export default App; 
```

Enter fullscreen mode Exit fullscreen mode

您的应用程序现在应该是这样的:

[![alt text](img/2066a139b577ce96e7b1c3369ac9024e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LbN2TzEX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mzt5oyshn1ltqlyk2ezs.gif)

参考资料:

*   [react-router-docs](https://reacttraining.com/react-router/web/guides/quick-start)

## 快乐大厦！