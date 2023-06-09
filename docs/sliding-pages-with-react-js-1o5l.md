# 用 React.js 滑动页面

> 原文：<https://dev.to/asaf_g6/sliding-pages-with-react-js-1o5l>

## 让他们滑行！

React 中实现路由的流行方式是使用 [React 路由器 v4](https://github.com/ReactTraining/react-router) 。React Router 很好用，但是不会让页面滑动。当然，你可以使用像 [React 路由器页面转换](https://www.npmjs.com/package/react-router-page-transition)这样的包来实现。

我们在本教程中不打算使用这两个包。相反，我们将推出自己的产品，创建一个滑动页面路由器。本教程是为了学习的目的，如果你想在产品中使用最终的代码，这将需要更多的工作。

完整的解决方案在 github 上，你可以在这里找到它

## 先决条件

你应该熟悉 HTML、Javascript、css 和 React。

## 步骤 1 -设置

我们将使用创建-反应-应用程序。如果你没有安装 React，[安装它](https://reactjs.org/docs/add-react-to-a-new-app.html)。

让我们创建我们的新应用:

```
$ create-react-app react-sliding-pages 
```

Enter fullscreen mode Exit fullscreen mode

好吧！第一步完成！
请随意删除 logo.svg，我们不会使用它。

你可以用 cd 进入项目的目录并运行 **npm start** ，这样你就可以看到你的进度。

## 第二步-创建页面元素

在 src 目录下创建名为 page.js 的新文件。

**页. js**

```
 import React, { Component } from 'react';

class Page extends Component {

    constructor(props) {
        super(props);
    }

    render() {
        return (
            <div style={{
                width: '100vw', 
                height: '100%',
                position: 'fixed',
                top: 0,
                left: 0,
                transform: 'translateX('+ this.props.left + 'px)',
                animationTimingFunction: 'ease-in',
                zIndex: -20,
                transition: 'transform .8s ease-in-out'
            }}>
                {this.props.children}
            </div>
        );

    }

}

export default Page; 
```

Enter fullscreen mode Exit fullscreen mode

在这个组件中有一些事情需要注意。

首先，这是一个容器组件——我们使用 **this.props.children** 来呈现所有子组件。

另外两个在 css 中:

**transformX**属性由 **left** 道具决定。

过渡和**动画计时功能**控制动画并使滑动发生。

现在我们有了自己的页面组件，我们应该试着使用它。

## 第三步-滑动页面

让我们来关注一些重要的造型。将 App.css 的内容更改为:

**App.css**

```
 .App {
  text-align: center;
}

.page {
  width: 300px;
  height: 100%;
  min-height: 5em;
  margin-right: auto;
  margin-left: auto;
  margin-top: 5em;
  padding: 2em;
  z-index: 15000;
} 
```

Enter fullscreen mode Exit fullscreen mode

将 App.js 的内容更改为:

**app . js**T2】

```
 import React, { Component } from 'react';
import './App.css';
import Page from './page';

class App extends Component {

  constructor(props) {
    super(props);
    this.state = {left: 0}
  }

  render () {
    return (
      <div className='App'>
        <div>
          <input type="text" onChange={(e) => this.setState({left: parseInt(e.target.value)})} />
        </div>
        <Page left={this.state.left}>
          <div className='page' style={{ backgroundColor: 'green' }}>
            bla1
          </div>
        </Page>
      </div>
    )
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

试着输入 1000 或-500，看看会发生什么。如果一切顺利，页面应该会滑过屏幕。

那么我们在这里做了什么？

我们创建了一个名为 **left** 的状态变量，并将其传递给我们的页面组件。当输入改变时，它触发 **setState** 方法并改变页面的 left 属性。

很好看，但只有一页。我们需要一个元素来包装我们的页面组件并切换它们。

## 步骤 4 -创建导航元素

这是我们程序的主要部分，也是最有逻辑的部分。让我们用小部件来创建我们的导航元素。

我们将从一个空组件开始。在 src 目录下创建名为 nav.js 的新文件。

```
 import React, { Component } from 'react';

class Nav extends Component {

}

export default Nav; 
```

Enter fullscreen mode Exit fullscreen mode

计划是将当前页面放在屏幕中间，隐藏屏幕两侧的其他页面。为了实现这一点，我们需要知道屏幕的宽度。**左边的**道具以前很好用，所以我们在这里也用它。

让我们为 Nav 组件创建构造函数:

**nav.js**

```
 ...

    constructor(props) {
        super(props);
        this.width = window.innerWidth || document.documentElement.clientWidth || document.body.clientWidth;
        let left = props.children.map(o => this.width);
        if (left.length > 0 ) {
            left[0] = 0;
        }
        this.state = {page: 0, left: left};        
    }

... 
```

Enter fullscreen mode Exit fullscreen mode

我们现在在 **this.width** 中有了屏幕的宽度，一个表示页面 left 属性的整数数组和一个表示当前页码的页面。

现在让我们添加**移动**方法:

**nav.js**

```
...

    move(page) {
        const left = this.state.left.slice();
        if (page >= left.length) {
          page = 0;
        }
        for (let i = 0; i < left.length; i++ ) {
          if (i < page) {
            left[i] = -this.width;
          } else if (i === page) {
            left[i] = 0;
          } else {
            left[i] = this.width;
          }
        }    
        this.setState({left: left, page: page})
      }

... 
```

Enter fullscreen mode Exit fullscreen mode

花点时间阅读代码并理解它的作用。

基本上，代码迭代左侧数组的副本，并根据页码设置每页的左侧位置。

让我们继续**渲染**方法。

**nav.js**

```
...

    render() {
        const pageElements = React.Children.map(this.props.children, (page, idx) =>
            React.cloneElement(page, { left: this.state.left[idx] })); 
        return (
            <div>
                {pageElements}
            </div>
        );
    }

... 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们使用 **React。Children.map** 迭代页面元素并添加左属性。

现在让我们在 App.js 中使用新的 Nav 元素。

**app . js**T2】

```
 import React, { Component } from 'react';
import './App.css';
import Page from './page';
import Nav from './nav';

class App extends Component {

  constructor(props) {
    super(props);
  }

  render() {
    return (
      <div className="App">
        <Nav>
          <Page>
            <div className="page" style={{ backgroundColor: 'green' }}>
              bla1
            </div>
          </Page>
          <Page>
            <div className="page" style={{ backgroundColor: 'red' }}>
              bla1
            </div>
          </Page>
          <Page>
            <div className="page" style={{ backgroundColor: 'blue' }}>
              bla1
            </div>
          </Page>
          <Page>
            <div className="page" style={{ backgroundColor: 'brown' }}>
              bla1
            </div>
          </Page>
        </Nav> 
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，您应该会在浏览器中看到第一个页面。您可以检查 html 源代码(使用浏览器的开发工具),并看到所有的子元素都被渲染了。

好了，我们有了所有的页面，但是不能访问 **move** 方法。让我们暂时添加一些按钮，只是为了看看幻灯片的工作。

**nav.js**

```
...

    render() {
        const pageElements = React.Children.map(this.props.children, (page, idx) =>
            React.cloneElement(page, { left: this.state.left[idx] })); 
        const buttonElements = React.Children.map(this.props.buttons, (button, idx) => {
            let newButton = React.cloneElement(button , { onClick: () => this.move(idx), ...button.props});
            return newButton;
        });
        return (
            <div>
                <div>
                    {buttonElements}
                </div>
                <div>
                    {pageElements}
                </div>
            </div>
        );
    }

... 
```

Enter fullscreen mode Exit fullscreen mode

**app . js**T2】

```
...

  render() {
    let buttons = [];
    for (let i = 0; i < 4; i++) {
      let button = <button key={i.toString()}>{i}</button>;
      buttons.push(button);
    }
    return (
      <div className="App">
        <Nav buttons={buttons}>
          <Page>
            <div className="page" style={{ backgroundColor: 'green' }}>
              bla1
            </div>
          </Page>
          <Page>
            <div className="page" style={{ backgroundColor: 'red' }}>
              bla1
            </div>
          </Page>
          <Page>
            <div className="page" style={{ backgroundColor: 'blue' }}>
              bla1
            </div>
          </Page>
          <Page>
            <div className="page" style={{ backgroundColor: 'brown' }}>
              bla1
            </div>
          </Page>
        </Nav> 
      </div>
    );
  }

... 
```

Enter fullscreen mode Exit fullscreen mode

试试浏览器上的按钮，页面会滑动。

现在，基本的功能工作，让我们使我们的导航元素更像路由器。

## 第五步-实施路由

首先，移除我们用于测试的按钮。我们不需要他们。完成后，在 src 目录下创建一个名为 link.js 的新文件。

**link.js**

```
 import React, { Component } from 'react';

class Link extends Component {

    go() {
        window.location.hash = this.props.to.slice(1);
    }

    render() {
        return (
            <i onClick={() => this.go()}>
                {this.props.children}
            </i> 
        );
    }

}

export default Link; 
```

Enter fullscreen mode Exit fullscreen mode

这个组件非常简单，它呈现组件的子组件，并根据**到**属性在点击时改变散列。

让我们的 Nav 组件对哈希变化做出反应。

**nav.js**

```
...

    constructor(props) {
        super(props);
        this.width = window.innerWidth || document.documentElement.clientWidth || document.body.clientWidth;
        let left = props.children.map(o => this.width);
        if (left.length > 0 ) {
            left[0] = 0;
        }
        this.state = {page: 0, left: left, from: left};
        this.pages = {};
    }

...

    componentDidMount() {
        this.move(this.pages[window.location.hash.slice(1)]);
    }

    componentWillMount() {
        window.onhashchange = (e) => {
            const i = e.newURL.indexOf('#');
            const hash = e.newURL.slice(i + 1);
            this.move(this.pages[hash]);
        };
    }

    render() {
        const pageElements = React.Children.map(this.props.children, (page, idx) => {
            const element = React.cloneElement(page, { left: this.state.left[idx], from: this.state.from[idx], eventer:this.eventer }); 
            this.pages[element.props.path.slice(1)] = idx;
            return element;
        });
        return (
            <div>
                {pageElements}
            </div>
        );
    }

... 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们正在将页面注册到**页面**属性，因此我们可以知道哪个路由属于哪个页面。

最后，在 App.js 中使用我们的新链接。

**app . js**T2】

```
 import React, { Component } from 'react';
import './App.css';
import Page from './page';
import Nav from './nav';
import Link from './link';

class App extends Component {

  render() {
    return (
      <div className="App">
        <div>
          <Link to="/green" >
            <button>green</button>
          </Link>
          <Link to="/red" >
            <button>red</button>
          </Link>
          <Link to="/blue" >
            <button>blue</button>
          </Link>
          <Link to="/brown" >
            <button>brown</button>
          </Link>
        </div>
        <Nav>
          <Page path="/green">
            <div className="page" style={{ backgroundColor: 'green' }}>
              bla1
            </div>
          </Page>
          <Page path="/red">
            <div className="page" style={{ backgroundColor: 'red' }}>
              bla1
            </div>
          </Page>
          <Page path="/blue">
            <div className="page" style={{ backgroundColor: 'blue' }}>
              bla1
            </div>
          </Page>
          <Page path="/brown">
            <div className="page" style={{ backgroundColor: 'brown' }}>
              bla1
            </div>
          </Page>
        </Nav> 
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

好吧！我们有一个滑动路由器！但是等等...

屏幕外的页面依然呈现。如果你的应用几乎是静态的，这可能对你有好处。但是我们知道，React 组件可以做很多事情..使用网络，做一些计算，动画...等等。我们需要一些方法来禁用屏幕外的组件。

## 第 6 步——只渲染可见的东西

这部分有点棘手，我们需要页面可见的滑动效果，但我们不希望它们在屏幕外呈现。例如，如果用户从第 5 页转到第 1 页，我们需要渲染所有页面的滑动效果，然后只渲染第 1 页。

我们将使用一个事件机制，它将在每次页面改变时触发。

给 nav.js 再加一个类。

**nav.js**

```
...

class MoveEvent {

    constructor() {
        this.listeners = [];
    }

    register(f) {
        this.listeners.push(f);
    }

    unregister(f) {
        this.listeners.splice(this.listeners.indexOf(f), 1);
    }

    fire(params) {
        this.listeners.forEach((listener) => {
            listener(params);
        });

    }
}

... 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常标准的事件类。我们还将从 prop 中添加一个**，这样页面元素将能够判断它是否处于过渡状态。
让我们将道具从导航组件传递到页面组件。**

**nav.js**

```
...

class Nav extends Component {

    constructor(props) {
        super(props);
        this.width = window.innerWidth || document.documentElement.clientWidth || document.body.clientWidth;
        let left = props.children.map(o => this.width);
        if (left.length > 0 ) {
            left[0] = 0;
        }
        this.state = {page: 0, left: left, from: left};
        this.pages = {};
        this.eventer = new MoveEvent();
    }

    move(page) {
        const left = this.state.left.slice();
        const from = left.slice();
        if (page >= left.length) {
          page = 0;
        }
        for (let i = 0; i < left.length; i++ ) {
          if (i < page) {
            left[i] = -this.width;
          } else if (i === page) {
            left[i] = 0;
          } else {
            left[i] = this.width;
          }
        }    
        this.setState({left: left, page: page, from: from});
        this.eventer.fire();
      }

    componentDidMount() {
        this.move(this.pages[window.location.hash.slice(1)]);
    }

    componentWillMount() {
        window.onhashchange = (e) => {
            const i = e.newURL.indexOf('#');
            const hash = e.newURL.slice(i + 1);
            this.move(this.pages[hash]);
            this.eventer.fire();
        };
    }

    render() {
        const pageElements = React.Children.map(this.props.children, (page, idx) => {
            const element = React.cloneElement(page, { left: this.state.left[idx], from: this.state.from[idx], eventer:this.eventer }); 
            this.pages[element.props.path.slice(1)] = idx;
            return element;
        });
        return (
            <div>
                {pageElements}
            </div>
        );
    }

}

... 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在让我们让页面明白它是否需要渲染孩子。

**页. js**

```
 import React, { Component } from 'react';

function sleep(ms) { // just a sleep util
    return new Promise(resolve => setTimeout(resolve, ms));
}

class Page extends Component {

    constructor(props) {
        super(props);
        this.state = {shouldRenderChildren: true};
    }

    isVisible() {
        if (this.props.left < 0) {
            return false;
        }
        if (this.props.left >= window.innerWidth) {
            return false;
        }
        return true;
    }

    move() {
        if (this.props.left !== this.props.from) {
            this.setState({ shouldRenderChildren: true });
        }
        this.reRender();
    }

    async reRender() {
        await sleep(1000);
        this.setState({shouldRenderChildren: this.isVisible()});
    }

    render() {
        return (
            <div
             style={{
                width: '100vw', 
                height: '100%',
                position: 'fixed',
                top: 0,
                left: 0,
                transform: 'translateX('+ this.props.left + 'px)',
                animationTimingFunction: 'ease-in',
                zIndex: -20,
                transition: 'transform .8s ease-in-out'
            }}> {this.state.shouldRenderChildren &&
                    this.props.children
                 }
            </div>
        );

    }

}

export default Page;

... 
```

Enter fullscreen mode Exit fullscreen mode

**isVisible** 方法返回页面是否离开屏幕。 **reRender** 方法休眠 1 秒并更新**shouldrenderchilds**状态变量。当调用 move 时，元素将被完全渲染 1 秒钟，然后如果它不在屏幕上，它将消失。

现在我们只需要注册到移动事件。

**页. js**

```
...

    componentDidMount() {
        this.props.eventer.register(() => this.move());
    }

    componentWillUnmount() {
        this.props.eventer.unregister(() => this.move());
    }

... 
```

Enter fullscreen mode Exit fullscreen mode

厉害！检查您的浏览器开发工具，并看到屏幕外的页面没有呈现任何孩子。

## 步骤 7(可选)-定制和扩展代码

您的应用程序可能需要页面表现不同。例如，你可以渲染每个页面的子组件，并给它们传递一个属性，告诉它们是否可见。想到什么就说什么。

那部分完全取决于你！

如果你正在扩展或定制代码，我很想听听你的想法和实现。

## 总结

我们用滑动页面创建了自己的路由器，这很酷。当然，我们的路由器不是 React 路由器的对手，React 路由器更花哨，也经过了实战考验。滚动你自己的有时可能是一个巨大的痛苦(没有人喜欢重新发明轮子)，但它有一个很大的优势-你可以准确地编码你的应用程序需要什么。

希望你喜欢，当然也可以随意评论或者分享。

感谢阅读。**