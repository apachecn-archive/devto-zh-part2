# 为什么不直接修改反应状态

> 原文：<https://dev.to/dceddia/why-not-to-modify-react-state-directly-4p5e>

大家都说 ***不干*** 。*从不直接突变状态*，一直调用`setState`。

但是为什么呢？

如果你尝试过，你可能会注意到没有什么不好的事情发生。如果你直接修改状态，调用`this.setState({})`甚至`this.forceUpdate()`，那么一切可能*出现*就好了。

```
this.state.cart.push(item.id);
this.setState({ cart: this.state.cart });
// renders like normal! maybe? 
```

Enter fullscreen mode Exit fullscreen mode

这是一个坏主意，原因有二(尽管它在这个例子和许多其他例子中是可行的)。

(其他需要避免的模式有`this.state.something = x`和`this.state = x`)

直接改变状态会导致奇怪的错误和难以优化的组件。这里有一个例子。

正如您可能已经知道的，调整 React 组件性能的一种常见方法是使其“纯净”，这导致它仅在其道具更改时才重新渲染(而不是每次其父级重新渲染)。这可以通过扩展`React.PureComponent`而不是`React.Component`来自动完成，或者通过实现`shouldComponentUpdate`生命周期方法将`nextProps`与当前道具进行比较来手动完成。如果道具看起来一样，它会跳过渲染，节省一些时间。

下面是一个简单的组件，它呈现了一个条目列表(注意它扩展了`React.PureComponent` ):

```
class ItemList extends React.PureComponent {
  render() {
    return (
      <ul>
        {this.props.items.map(item => <li key={item.id}>{item.value}</li>)}
      </ul>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，这里有一个很小的应用程序，它可以呈现`ItemList`，并允许你向列表中添加项目——好的方式(不变的)和坏的方式(通过改变状态)。看看会发生什么。

```
class App extends Component {
  // Initialize items to an empty array
  state = {
    items: []
  };

  // Initialize a counter that will increment
  // for each item ID
  nextItemId = 0;

  makeItem() {
    // Create a new ID and use
    // a random number as the value
    return {
      id: this.nextItemId++,
      value: Math.random()
    };
  }

  // The Right Way:
  // copy the existing items and add a new one
  addItemImmutably = () => {
    this.setState({
      items: [...this.state.items, this.makeItem()]
    });
  };

  // The Wrong Way:
  // mutate items and set it back
  addItemMutably = () => {
    this.state.items.push(this.makeItem());
    this.setState({ items: this.state.items });
  };

  render() {
    return (
      <div>
        <button onClick={this.addItemImmutably}>
          Add item immutably (good)
        </button>
        <button onClick={this.addItemMutably}>Add item mutably (bad)</button>
        <ItemList items={this.state.items} />
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

试试吧！

点击几次**不可变**添加按钮，注意列表是如何按预期更新的。

然后点击**可变的**添加按钮，注意新的条目是如何不出现的，即使状态正在改变。

最后，再次单击不可变的 Add 按钮，观察 ItemList 如何重新呈现所有缺失的(可变添加的)项目。

发生这种情况是因为`ItemList`是纯的，并且因为在`this.state.items`数组上推一个新的项目不会替换底层数组。当`ItemList`被要求重新渲染时，它会注意到它的道具没有改变，它不会重新渲染。

## 重述

这就是为什么你不应该改变状态，即使你立即调用 setState。如果这样做，优化的组件可能不会重新渲染，并且渲染错误将很难跟踪。

相反，当您调用`setState`时，总是创建新的对象和数组，这就是我们在上面使用 spread 操作符所做的。[了解如何使用 spread 操作符进行不可变更新](https://daveceddia.com/immutable-updates-react-redux)。

[为什么不直接修改 React 状态](https://daveceddia.com/why-not-modify-react-state-directly/)最初由戴夫·塞迪亚于 2018 年 6 月 01 日在[戴夫·塞迪亚](https://daveceddia.com)发表。

[代码项目](http://www.codeproject.com)