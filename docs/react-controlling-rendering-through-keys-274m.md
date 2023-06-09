# 反应:通过关键点控制渲染

> 原文：<https://dev.to/ganderzz/react-controlling-rendering-through-keys-274m>

*注意:*这应该谨慎使用，因为它会导致性能下降。

`key`，我们将一些值放入其中以抑制警告的属性。React 使用该属性来确定何时删除、更新或创建组件。我们通常不怎么与`key`道具互动——除了在循环中。

我的团队最近遇到了一个问题——当状态改变时，我们如何重新呈现所有子组件？最简单的方法...你猜对了，`key`！

让我们来看看。

```
class Parent extends Component {
  state = {
    key: true,
    count: 0
  };

  handleChildUnmount = () => {
    this.setState(prevProps => ({ count: prevProps.count + 1 }));
  };

  toggleKey = () => {
    this.setState(prevProps => ({ key: !prevProps.key }));
  };

  render() {
    const { key, count } = this.state;

    return (
      <div>
        <button onClick={this.toggleKey}>Toggle Child Key</button>
        <Child key={key} count={count} onUnmount={this.handleChildUnmount} />
      </div>
    );
  }
}

class Child extends Component {
  componentWillUnmount() {
    this.props.onUnmount();
  }

  render() {
    return <div>Total Renders: {this.props.count}</div>;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Edit react-scroll-to](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/48zl4zoyv0)

在这个例子中，我们在*真*和*假*之间切换子组件的`key`道具。然后，我们通过回调跟踪子节点的每次卸载，以增加父节点的状态。当我们切换`key`道具时，我们会看到计数器每次都递增。这是因为 React 使用密钥来确定组件是否已经更改。因为我们每次都在更改密钥，所以 React 会看到我们的组件有更新，并重新呈现子组件。

这是导致子组件(以及子组件的所有子组件)重新呈现的最简单的方法，并且显示了理解`key`属性的力量！

*注意:*即使一个子组件在一个`shouldComponentUpdate`中返回 *false* ，改变 key prop 无论如何都会导致它重新呈现(在上面的 CodeSandbox 中试试吧！).

*实际用法:*【reactjs.org】你可能不需要派生状态