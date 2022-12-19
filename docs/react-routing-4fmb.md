# 反应路由

> 原文：<https://dev.to/k_penguin_sato/react-routing-4fmb>

# 简介

在这篇文章中，我将讨论如何用 [react-router-dom](https://www.npmjs.com/package/react-router-dom) 在 react 应用中实现`routing`。我将解释如何在组件中实现路由以及传递数据。(我希望您的计算机上有 react-development-environment 作为先决条件。)

# 目录

1.  安装 react-router-dom
2.  [设置链接](#setting-up-the-links)
3.  [设置路由器和路由](#setting-up-router-and-route)
4.  [用道具传递数据](#passing-data-with-props)

# [T1】安装 react-router-dom](#install-reactrouterdom)

您可以通过运行下面的命令来安装`react-router-dom`。

```
$  npm install react-router-dom 
```

Enter fullscreen mode Exit fullscreen mode

# 设置链接

首先，从`react-router-dom`导入`Link`并创建与指定路径相连的链接。
例如，如果用户点击下面代码中的`Home`，用户将被转到“/”页面，如果用户点击`About`，用户将被转到`/About`页面。

```
import React from 'react'
import { Link } from 'react-router-dom'

class Navbar extends React.Component {
  render(){
    return(
      <div>
        <Link to="/">Home</Link>
        <Link to="/About">About</Link>
      </div>
    )
  }
}

export default Navbar; 
```

Enter fullscreen mode Exit fullscreen mode

# 设置路由器和路由

在 react 应用程序中将一个页面连接到另一个页面所要做的就是使用`Router`和`Route`连接路径和组件。
**真的只有两件事你应该牢记在心。**

*   `Routes`必须在`Routers`中定义。
*   在每个`Route`中写下路径和相应的组件

```
import React, { Component } from 'react';
import { BrowserRouter as Router, Route } from 'react-router-dom';
import Navbar from './Navbar';
import About from './About';
import Home from './Home';

class App extends Component {
  render() {
    return (
      <div className="App">
        <Router>
          <div>
            <Navbar /><hr/>
            <Route exact path='/' component={Home}/>
            <Route path='/About' component={About}/>
          </div>
        </Router>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

# 用道具传递数据

您可以通过编写类似下面代码的`Route`元素来轻松实现这一点。

```
class App extends Component {
  render() {
    return (
      <div className="App">
        <Router>
          <div>
            <Navbar /><hr/>
            <Route exact path='/' component={Home}/>
            <Route path='/About' render={ () => <About name={'Tom'}/> }/>
          </div>
        </Router>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

您可以像下面的代码一样获取`About`组件中的数据。

```
import React from 'react'

class About extends React.Component {
  render(){
    return(
      <div>
        <h1>About</h1>
        <h2>I am {this.props.name}</h2>
      </div>
    )
  }
}

export default About; 
```

Enter fullscreen mode Exit fullscreen mode

查看其他帖子[这里](https://k-blog0130.herokuapp.com/)