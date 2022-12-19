# React 的错误和警告信息的真正乐趣

> 原文：<https://dev.to/alshakero/the-true-delight-of-reacts-error-and-warning-messages-168j>

[![Photo by [Štefan Štefančík](https://unsplash.com/@cikstefan) on [Unsplash](https://unsplash.com/photos/pzA7QWNCIYg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)](img/16f0a85e7a963b5e77fa0e14392a2806.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--cUxhBjcT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qlhwsei92auehdgki3sg.jpg) *Photo by [ Štefan Štefančík ](https://unsplash.com/photos/pzA7QWNCIYg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/pzA7QWNCIYg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)* 

## 简介

有一个吸引人的想法多年来一直萦绕着我，那就是软件开发人员，带着他们所有的偏见、成见和人性，塑造了我们周围的世界。

想想吧。软件就像音乐一样，是一种[无形的商品](https://en.wikipedia.org/wiki/Intangible_good)。原材料并不像葡萄酒或巧克力那样会限制产品的数量，进而限制喜欢它的人的数量。存在着成千上万个由个人在周六晚上编写的软件，被世界各地数百万人使用。

公平地说，那些个人对人们的生活有着或大或小的影响。即使我们考虑公司，相对于软件用户的数量来说，生产软件的人数也是一个微小的数字。例如，[谷歌在全球拥有约 1.8 万名](https://www.developer.com/daily_news/samsung-employs-twice-as-many-software-engineers-as-google.html)工程师，而[约有 11.7 亿人使用谷歌](https://www.statista.com/chart/899/unique-users-of-search-engines-in-december-2012/)，这一比例为 0.000015%。我们可以看到巨大的依赖软件开发者的意见和想法来塑造世界。显然存在一个开发者社会和一个亚文化。如果一个迷因或一个想法在这个社会传播，它将不可避免地塑造每个人使用的技术。

现在以科幻的方式想象一下，有一种想象中的软件技术可以解决一个世界问题。说它能治愈癌症或彻底消除贫困。*但是*，这项技术有毁掉使用它的软件开发者生活的代价。这项技术有朝一日会出现吗？几乎没有。这个虚构的故事是软件开发者和最终用户之间利益冲突的一个极端例子。如果好的软件意味着开发人员不舒服，我们将看不到好的软件。软件开发人员生活的很大一部分就是错误消息。它们是体验的关键，关注它们会有很大的帮助。它们对开发者直接重要，对我们每个人间接重要。

## 做出反应

在我过去 10 年使用过的所有库和包中，我还没有见过错误和警告如此漂亮的库。包括我在内的开发人员，通常会试图检测出破坏他们自己代码的错误，并连同他们手头的数据一起报告给用户(scope？).可能会提到导致错误的最常见的错误以及自动生成的堆栈跟踪，就这样。

```
TypeError: undefined is not an object 
```

Enter fullscreen mode Exit fullscreen mode

但是有了 React，库就会加班加点地努力为你猜测哪里出了问题。猜测错误的努力非常明显，而且非常有用；它节省了你大量的调试时间，尽力挽救你的一天。

为了帮助你像我写 React 应用一样享受这种体验，我将把这篇文章变成一次旅行。我将展示其中有错误的 React 片段，您可以尝试找到问题，或者只看下面的警告/错误，并决定它是否对您有帮助。这并不是要列出 React 日志中的所有消息。这是一个非常小的灵感样本。

开始吧！

### 1。一个简单的

```
class MyComponent extends React.Component {
  componentWillMount() {
    console.log('Component mounted!')
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个组件有什么问题？

消息如下:

```
Warning: MyComponent(…): No `render` method found on the returned component instance: you may have forgotten to define `render`. 
```

Enter fullscreen mode Exit fullscreen mode

很美，不是吗？**组件名称**和正确建议。超级容易搞定。

### 2。更棘手的问题

```
class MyComponent extends React.Component {
  componentDidUnmount() {
    console.log('Unmounted!');
  }
  render() {
    return <h1>Hi!</h1>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题是什么？

消息如下:

```
Warning: MyComponent has a method called componentDidUnmount(). But there is no such lifecycle method. Did you mean componentWillUnmount()? 
```

Enter fullscreen mode Exit fullscreen mode

React 在这里出了问题。它预料到你会犯这个错误，就在那里等你。在这种情况下，沉默不会破坏反应，只会破坏你的应用。很同情图书馆。

### 3。有点明显的一个

```
class GreetingComponent extends React.Component {
  constructor() {
    super();
    this.state = {name: 'Omar'};
  }
  render() {
    this.setState({name: 'Not Omar'});
    return <div>Hi {this.state.name}!</div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有什么问题？

以下是警告:

```
Warning: Cannot update during an existing state transition (such as within `render` or another component’s constructor). Render methods should be a pure function of props and state; constructor side-effects are an anti-pattern, but can be moved to `componentWillMount`. 
```

Enter fullscreen mode Exit fullscreen mode

### 4。不太明显

```
class MyComponent extends React.Component {
  constructor() {
    super();
    this.setState({name: 'John'});
  }
  render() {
    return <h1>Hi {this.state.name}!</h1>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有什么问题？

消息如下:

```
Warning: setState(…): Can only update a mounted or mounting component. This usually means you called setState() on an unmounted component. This is a no-op.

Please check the code for the MyComponent component. 
```

Enter fullscreen mode Exit fullscreen mode

### 5。纯粹的优雅

```
 const type = true;

ReactDOM.render( 
  <input type={type} />,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

这里的类型有什么问题？

以下是警告:

```
Warning: Received `true` for non-boolean attribute `type`. If this is expected, cast the value to a string.
 in input 
```

Enter fullscreen mode Exit fullscreen mode

这是另一个不影响 React，而是影响您的应用程序的错误示例。

### 6。初学者的救助者

```
class greetingComponent extends React.Component {
  render() {
    return <h1>Hi!</h1>
  }
}

ReactDOM.render( 
  <greetingComponent />,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

这两个警告:

```
Warning: <greetingComponent /> is using uppercase HTML. Always use lowercase HTML tags in React.

Warning: The tag <greetingComponent> is unrecognized in this browser. If you meant to render a React component, start its name with an uppercase letter. 
```

Enter fullscreen mode Exit fullscreen mode

我至少一次上当了。

### 7。好的，我修好了，但是它还是不能用

```
class GreetingComponent extends React.Component {
  render() {
    return <h1>Hi!</h1>
  }
}

ReactDOM.render( 
  GreetingComponent,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

现在怎么了？

消息是:

```
Warning: Functions are not valid as a React child. This may happen if you return a Component instead of <Component /> from render. Or maybe you meant to call this function rather than return it. 
```

Enter fullscreen mode Exit fullscreen mode

对，应该是:

```
ReactDOM.render(<GreetingComponent />, document.getElementById("root")); 
```

Enter fullscreen mode Exit fullscreen mode

### 8。在最初的几天里，这是一个很常见的错误

```
 class GreetingComponent extends React.Component {
  render() {
    return <h1 class="bold">Hello!</h1>
  }
}

ReactDOM.render( 
  <GreetingComponent />,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

这上面是什么？

消息是:

```
Warning: Invalid DOM property `class`. Did you mean `className`?
 in h1 (created by GreetingComponent)
 in GreetingComponent 
```

Enter fullscreen mode Exit fullscreen mode

足够的数据让你准确地发现你的错误

### 9。你为什么不回来？🎼

```
 class GreetingComponent extends React.Component {
  render() {
    <h1>Hello!</h1>
  }
}

ReactDOM.render( 
  <GreetingComponent />,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

你说再见
我试图隐藏我内心的感受
直到你从我身边经过
你说你会回来
你会回来吗？🎵

```
Uncaught Error: GreetingComponent(…): Nothing was returned from render. This usually means a return statement is missing. Or, to render nothing, return null. 
```

Enter fullscreen mode Exit fullscreen mode

## 10。不需要例子的是:

```
You likely forgot to export your component from the file it's defined in, or you might have mixed up default and named imports. 
```

Enter fullscreen mode Exit fullscreen mode

这是我个人的最爱。

显然，我没有使用过所有存在的框架和库。可能还有更好的有用的错误消息的例子。但是作为一名前端开发人员，我可以有把握地说 React easily 脱颖而出。我受到了它的启发，现在我正尽我所能去猜测我的代码用户可能会犯错误的地方，并在他们犯错误时给他们提供详细而有用的错误信息。因为我坚信更好的开发者体验意味着更好的世界。

如果你知道其他一些关于 React 警告的好例子，请在评论中提出来，我会添加进去。

*这篇文章也发表在[媒体](https://codeburst.io/the-true-delight-of-reacts-error-and-warning-messages-137002a3bbd4)上。*