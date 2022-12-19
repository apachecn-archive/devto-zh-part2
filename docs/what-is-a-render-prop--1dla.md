# 什么是渲染道具？

> 原文：<https://dev.to/lvrbrtsn/what-is-a-render-prop--1dla>

*这是来自我的 React 简讯，你可以在这里订阅*

### 什么是渲染道具，为什么要关心？

有趣的是，我实际上从 2016 年的某个时候开始使用渲染道具，但我不知道这并不常见。实际上，我认为这只是每个人在反应中的正常行为。当时我在给我的团队教授它们的时候，称它们为“组件道具”。那是因为本质上他们就是这样。作为道具传入的组件。它们可能看起来更令人困惑，但那只是箭头语法在捉弄你。让我演示一下。

```
const SimpleComp = data => <h2>{data}</h2>;
const CompWithRenderProp = ({ RenderProp }) => (
  <div>
    <h1>Hey You!</h1>
    {RenderProp("from Comp")}
  </div>
);
const App = () => {
  return (
    <div className="App">
      <CompWithRenderProp RenderProp={SimpleComp} />
    </div>
  );
}; 
```

现在你可能在想“我什么时候会想这么做”。良好的渲染道具可以用来代替*任何*高阶组件。没错，字面上每一个高阶组件*都可以用渲染道具代替*编写！

我认为这真的很酷，因为我真的不喜欢编写高阶组件。坦率地说，我觉得它们令人困惑，在这里渲染道具对我来说非常自然。

我经常用这种模式来处理情态动词。我将一个`close`函数传递给组件，这样我可以选择通过“X”或“Cancel”按钮关闭它，或者在提交后关闭，而不需要重复“关闭”逻辑。

使用渲染道具的常见方式是利用儿童道具。我们可以这样写上面的代码。

```
const CompWithRenderProp = ({ children }) => (
  <div>
    <h1>Hey You!</h1>
    {children("from Comp")}
  </div>
);
const App = () => {
  return (
    <div className="App">
      <CompWithRenderProp >
      {data => <h2>{data}</h2>}
      </CompWithRenderProp>
    </div>
  );
}; 
```

### 每周外卖

1.  每一个高阶组件都可以使用渲染道具重新创建。
2.  渲染道具只是作为道具传递的组件。

订阅我的时事通讯，获取更多类似的内容。
[通迅](https://www.getdrip.com/forms/669497779/submissions/new)