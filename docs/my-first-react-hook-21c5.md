# 我的第一个反应钩

> 原文：<https://dev.to/avraammavridis/my-first-react-hook-21c5>

这个**不是**对钩子的介绍，或者任何种类的技巧或者最佳实践，在钩子刚刚被宣布的最后，所以我们必须和它们一起玩，等着看社区如何接受它们，以及将会出现的最佳实践。这只是我实现一个钩子的经验。

我想创建一个钩子，使用交叉点观察器 API 来跟踪功能组件的可见性。每当可见性改变时(组件是否在视口中),我希望钩子更新组件的状态。

我要把我的钩子叫做`useVisibility`。

交叉点观察器需要访问它要观察的 DOM 节点:

```
var target = document.querySelector('#listItem');
observer.observe(target); 
```

所以`useRef`似乎是创建对组件标记
的引用的正确挂钩

```
function HelloComponent(){
  const nodeRef = useRef(null);

  return (
    <div className="App" ref={nodeRef}>
      <h1>Hello</h1>
    </div>
  );
} 
```

除了节点引用，交叉点观察器接受一些选项，这可以是我们钩子的第二个参数，所以它的签名可以是:

```
 const visibility = useVisibility(nodeRef, options); 
```

现在让我们编写实际的钩子，我们将需要 React 的`useState`钩子来跟踪可见性状态

```
function useVisibility(node, options = {}) {
  const [ visible, setVisibilty ] = useState({});
  ... 
```

然后我们将创建一个交叉点观察器的实例，传递一个回调和选项。

```
const observer = new IntersectionObserver(handleObserverUpdate, options); 
```

要开始调用回调，我们需要告诉观察者开始观察我们的节点，因为这是一个“react world 之外”的浏览器 API，我们将使用 react 提供的`useEffect`钩子。

```
 useEffect(() => {
    if (node.current) {
      observer.observe(node.current);
    }
  }); 
```

虽然我们的代码有内存泄漏，但我们需要提供一种方法来清理，因此我们返回一个回调，当调用时，它告诉我们的观察者停止观察节点。

```
 useEffect(() => {
    if (node.current) {
      observer.observe(node.current);
    }

    return function cleanup() {
      observer.unobserve(node.current);
    };
  }); 
```

最后，我们的处理程序将如下所示:

```
 const isIntersecting = useRef();

  const handleObserverUpdate = (entries) => {
    const ent = entries[0];

    if (isIntersecting.current !== ent.isIntersecting) {
      setVisibilty(ent);
      isIntersecting.current = ent.isIntersecting;
    }
  }; 
```

现在，你可能想知道为什么我使用`const isIntersecting = useRef()`，因为这不是一个 DOM 节点。

正如文件所述:

> 请注意，useRef()不仅对 Ref 属性有用。它便于保存任何可变值，就像在类中使用实例字段一样。

我想要一个在组件的整个生命周期中保持不变的 ref 属性。

总的来说，我个人认为新的 API 非常简单而且非常有用，我对社区在接下来的几个月中提出的解决方案和最佳实践感到非常兴奋。

不过说实话，测试并不像预期的那么容易，主要是因为需要模仿这样的浏览器 API，而不是因为 hook API。大多数测试框架都附带了 utils 来模拟点击、输入变化等，但是一些 API 需要人工模拟，这取决于它们的复杂性，这可能很棘手。

你可以在这里找到钩子

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png) [ AvraamMavridis ](https://github.com/AvraamMavridis) / [反应-交集-可见-钩子](https://github.com/AvraamMavridis/react-intersection-visible-hook)

### React Hook 跟踪功能组件的可见性

<article class="markdown-body entry-content container-lg" itemprop="text">

# 反应-相交-可见-挂钩

React hook 根据 IntersectionVisible Observer 跟踪功能组件的可见性。

在下面的例子中，我们根据蓝框的可见性改变了主体的背景颜色。

[![](../Images/a225fe756d8b79a1f23f4d56d6867087.png)T2】](https://github.com/AvraamMavridis/react-intersection-visible-hook/blob/master/demo_gif.gif?raw=true)

*演示和测试即将开始*

```
import useVisibility from 'react-intersection-visible-hook' 
```

### 如何使用它

```
function App() {
  const nodeRef = useRef(null);
  const visibility = useVisibility(nodeRef);
  return (
    <div className="App" ref={nodeRef}>
      <h1>Hello</h1>
    </div>
  );
}
```

##### 带选项

```
const options = {
  root: document.querySelector('#scrollArea'),
  rootMargin: '0px',
  threshold: 1.0
}
function App() {
  const nodeRef = useRef(null);
  const visibility = useVisibility(nodeRef, options);
  return (
    <div className="App"
```

…</article>

[View on GitHub](https://github.com/AvraamMavridis/react-intersection-visible-hook)