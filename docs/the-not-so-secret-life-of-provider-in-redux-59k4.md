# Redux 中不那么隐秘的供应商生活

> 原文：<https://dev.to/sadick/the-not-so-secret-life-of-provider-in-redux-59k4>

[![](img/0a24a126301addc6499033b74c06b092.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ri_Knf42--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.valentinog.com/blog/wp-content/uploads/2017/12/redux-react-tutorial-beginner-2018.png)

我总是问自己`<Provider>`是做什么的，为什么在使用 redux 时它是必要的。
react-redux 如何让我能够访问我的存储对象？

我好奇的天性让我找到了 react redux 代码库。我期望找到一个大型的 compiled`<Provider>`组件，但令我惊讶的是，事实并非如此。

为了可读性，我省略了一些代码。

```
import React from "react";
import PropTypes from "prop-types";

class Provider extends React.Component {
  constructor(props, context) {
    super(props, context);
    this.store = props.store;
  }
  getChildContext() {
    return { store: this.store };
  }
  render() {
    return this.props.children;
  }
}
Provider.childContextTypes = {
  store: PropTypes.object
};

export default Provider; 
```

Enter fullscreen mode Exit fullscreen mode

## 提供商在做什么

提供者使用 react 的上下文 api。上下文提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性。

提供者通过`getChildContext`方法使`store`可以被其子组件访问。因此它的孩子可以用`props.context.store`访问`store`。

```
const Child = ({props}) => {
  console.log(props.context.store)
  return <div>child</div> } 
```

Enter fullscreen mode Exit fullscreen mode

现在`Child`可以访问商店，而不需要我们明确地传递给它

```
<Provider store={store}>
  <Child/>
</Provider> 
```

Enter fullscreen mode Exit fullscreen mode

还要注意定义在 provider 类上的`childContextTypes`。这是必需的，以便 react 可以知道我们通过上下文共享的数据的形状。

这就是 react-redux `connect`函数能够获取我们的存储对象的方式。下面是连接的简化版本。

```
 function connect(mapStateToProps, mapDispatchToProps) {
  return function(Component) {
    class App extends React.Component {
      constructor(props, context) {
        super(props, context);
        this.state = context.store.getState();
      }
      render() {
        return <Component {...this.state}/>
      }
    }
    App.contextTypes = {
      state: PropTypes.object
    };
    return App;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode