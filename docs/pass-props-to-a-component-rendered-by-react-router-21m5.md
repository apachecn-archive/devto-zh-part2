# 将道具传递给 React 路由器 V4 渲染的组件

> 原文：<https://dev.to/tylermcginnis/pass-props-to-a-component-rendered-by-react-router-21m5>

这篇文章最初发表在 TylerMcGinnis.com 大学，是他们 T2 反应路由器课程的一部分。

* * *

### 视频

[https://www.youtube.com/embed/By7vJuSPaYo](https://www.youtube.com/embed/By7vJuSPaYo)

React 路由器使用声明式的、基于组件的路由方法。这意味着当你想创建一条新的路线时，你需要渲染一个`Route`组件。如果你熟悉 React Router，你会知道要做到这一点，当应用程序的位置与`path`匹配时，你需要传递`Route`一个`path`和一个`component`来渲染。

```
<Route path='/dashboard' component={Dashboard} /> 
```

现在，如果我们也想传递一个道具呢？解决这个问题有几种不同的方法，但正确的方法只有一种。你的第一个想法可能是将它作为道具在`Route`上传递。

```
<Route
  path='/dashboard'
  component={Dashboard}
  isAuthed={true}
/> 
```

可惜，这是行不通的。React 路由器不会将属性转发到组件上。相反，它会忽略它。

你可能想到的下一个想法，也是我在几个地方看到的一个模式，是传递`component`一个创建元素的内联函数。

```
<Route
  path='/dashboard'
  component={() => <Dashboard isAuthed={true} />} /> 
```

虽然从技术上来说这是可行的，但这不是最好的解决方案。之所以这样，是因为性能。根据官方文件...

> 当您使用组件 props 时，路由器使用 React.createElement 从给定组件创建新的 React 元素。这意味着，如果您为 component 属性提供一个内联函数，那么每次渲染时都会创建一个新的组件。这会导致现有组件卸载和新组件装载，而不仅仅是更新现有组件。

所以，如果你不应该把一个函数传递给`component`，解决方案是什么？原来 React 路由器团队预测到了这个问题，并给了我们一个方便的解决方案。不要使用`component`，使用`render`道具。`render`接受一个功能组件，该功能不会像`component`那样被不必要地重新安装。该功能也将获得与`component`相同的所有道具。所以你可以把它们传递给 rendred 组件。

```
<Route
  path='/dashboard'
  render={(props) => <Dashboard {...props} isAuthed={true} />} /> 
```

概括一下，如果你需要将一个属性传递给 React Router 正在渲染的组件，不要使用`Route`的`component`属性，而是使用它的`render`属性传递给它一个内联函数，然后将参数传递给你正在创建的元素。