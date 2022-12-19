# React -异步组件呈现包装器

> 原文：<https://dev.to/sadarshannaiynar/react---asynchronous-component-rendering-wrapper-2pmo>

大多数时候，我们的前端应用程序与各种服务和 API 进行交互，以填充和显示必要的数据。我们通常显示同样的加载屏幕，让用户等待一段时间，然后才允许他们使用这个页面。但是有时用户所需的大部分内容都是可用的，但是用户不得不等待页面上不必要的数据加载。从用户体验的角度来看，这是非常糟糕的。

考虑这个场景，你正在打开一个博客链接。文本加载速度更快，但页面不允许你导航，直到图片和侧边链接被加载。相反，页面可以让你在图片和其他东西同时加载的时候进行导航。

在 react 中解决这个问题的方法之一是使用异步包装器来呈现组件。我们来取两个分量`HeadingComponent`和`ParagraphComponent`。

```
const HeadingComponent = props => <h1>{props.data}</h1>;

const ParagaphComponent = props => <p>{props.data}</p>; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在将创建`AsyncComponent`，它作为`HeadingComponent`和`ParagraphComponent`的包装器，显示来自两个不同 API 的数据。

```
class AsyncComponent extends React.PureComponent {
  constructor(props) {
    super(props);
    this.state = {
      resolvedError: false,
      resolvedSuccess: false,
      data: '',
      error: '',
    };
    this.renderChildren = this.renderChildren.bind(this);
  }

  componentDidMount() {
    this.props.promise()
      .then(data => this.setState({ resolvedSuccess: true, data }))
      .catch(error => this.setState({ resolvedError: true, error }));
  }

  renderChildren() {
    return React.Children.map(this.props.children, child => (
      React.cloneElement(child, {
        data: this.state.data,
      })
    ))
  }

  render() {
    if (this.state.resolvedError) {
      return <h1>Error Encountered</h1>;
    } else if (this.state.resolvedSuccess) {
      return <div>{ this.renderChildren() }</div>;
    } else {
      return <h1>Loading...</h1>;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`AsyncComponent`从`componentDidMount`那里得到一个叫做`promise`的道具。如果解析成功，它会在拒绝的情况下将数据存储在状态和错误中。然后在渲染方法中我们渲染

1.  错误情况下的错误组件
2.  子节点如果解析成功
3.  否则加载组件

有时子组件需要响应数据。React 不允许我们直接从子元素中获取组件，所以我们使用 React 的内置函数，如`React.Children.map`和`React.cloneElement`。我们遍历组件的子元素，并通过添加一个具有来自 API 的实际响应的属性`data`来克隆每个子元素，这样子元素也可以访问响应。

最后一段代码将上述所有内容放在一起

```
const HeadingAPI = () => new Promise((resolve, reject) => {
  setTimeout(() => resolve('Heading'), 5000);
});

const ParagraphAPI = () => new Promise((resolve, reject) => {
  setTimeout(() => resolve('Paragraph data'), 2000);
});

const App = () => (
  <div>
    <AsyncComponent promise={HeadingAPI}>
      <HeadingComponent />
    </AsyncComponent>
    <AsyncComponent promise={ParagraphAPI}>
      <ParagaphComponent />
    </AsyncComponent>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个运行场景的 Codepen，两个承诺都成功解析。
[https://codepen.io/sadarshannaiynar/embed/PeQRQO?height=600&default-tab=result&embed-version=2](https://codepen.io/sadarshannaiynar/embed/PeQRQO?height=600&default-tab=result&embed-version=2)T2】

这是一个代码笔，运行一个承诺被拒绝的场景。
[https://codepen.io/sadarshannaiynar/embed/aGqYRv?height=600&default-tab=result&embed-version=2](https://codepen.io/sadarshannaiynar/embed/aGqYRv?height=600&default-tab=result&embed-version=2)T2】

正如你所看到的，一个 API 的失败不会影响其他组件的渲染，用户可以继续浏览网页。这极大地改善了用户体验，也减少了跨组件 API 调用所产生的冗余代码量。

您仍然可以通过提供定制的加载器和错误组件来改进包装器，使其看起来更漂亮。