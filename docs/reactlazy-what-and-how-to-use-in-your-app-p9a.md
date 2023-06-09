# React.lazy，在你的应用中使用什么以及如何使用

> 原文：<https://dev.to/vigzmv/reactlazy-what-and-how-to-use-in-your-app-p9a>

React 16.6 发布了，随之而来的是新的 [Lazy API](https://reactjs.org/docs/code-splitting.html#reactlazy) 。Rreact.lazy 函数允许您将动态导入作为常规组件呈现。

React.lazy 采用一个必须调用动态导入()的函数。这必须返回一个承诺，该承诺解析为一个包含 React 组件的默认导出的模块。

#### 用法:

```
const LazyComponent = React.lazy(() => import('./Component')); 
```

#### 显示有悬念的退路

用悬念包装组件，并传递一个回退。在等待组件加载时，回退属性接受您想要呈现的任何 React 元素。

```
<Suspense fallback={<div>Loading Component...</div>}>
  <LazyComponent />
</Suspense> 
```

#### 我举了一个简单的例子来说明

app 显示一个**按我！！**按钮，当按下该按钮时，从 [randomuser](https://randomuser.me) 获取一个随机用户数据，然后加载并呈现用户组件。

```
const User = React.lazy(() => import('./User'));

....

<React.Suspense fallback={<div>Loading Component...</div>}>
  {user && <User user={user}/>}
  {loading ? (
    <div>Loading User...</div>
  ) : (
    !user && <button onClick={this.loadUser}>Press Me!!</button>
  )}
</React.Suspense> 
```

**Live**:[react-lazy-example](http://vigneshm.com/react-lazy-example/)
T5】Code:[github](https://github.com/vigzmv/react-lazy-example)

运行应用程序，在开发控制台中打开网络选项卡，按下按钮，看到延迟加载的 javascript 块。🎉🎉