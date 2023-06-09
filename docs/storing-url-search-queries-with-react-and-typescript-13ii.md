# 用 React 和 Typescript 存储 url 搜索查询

> 原文：<https://dev.to/alexs1305/storing-url-search-queries-with-react-and-typescript-13ii>

我最近在研究在 url 中存储一些值，以使用户能够传递 URL 并将应用程序加载到某个状态。很简单，尽管由于应用程序也使用 redux 来管理应用程序状态这一事实而变得复杂，因为 Redux 将不在这里讨论，我们将用 state 来模拟这一行为。

想看看用的代码？ **[访问代码沙盒](https://codesandbox.io/s/k0vjrrkwjv)**

### 设置

因此，首先我们需要确保我们已经安装了 react-router-dom 和 react-router，并且我们的应用程序正在使用 react-router-dom(如果您需要这方面的帮助[，Paul Sherman 有一篇关于基本设置的精彩文章](https://medium.com/@pshrmn/a-simple-react-router-v4-tutorial-7f23ff27adf)。我们还需要确保我们想要使用搜索参数捕获的组件通过 props 暴露给它们，因此我们需要将 RouterComponentProps 添加到 prop 类型，然后在导出组件时，我们需要用 withRouter()包装。

```
import {RouteComponentProps, withRouter} from "react-router";

class SimpleMainComponent extends React.Component<
  RouteComponentProps<any>
{
...
}

export default withRouter(SimpleMainComponent); 
```

### 获取搜索查询

设置完成后，我们将能够访问路线属性，我们感兴趣的是位置。正确搜索。

```
this.props.location.search 
```

这是一个字符串，所以当我们导航到“http://.../用户？name=john“这将意味着 **location.search** 是”？name=john”。当我们想从中获取一个特定的值时，这对我们没有太大的帮助。这就是 **URLSearchParams** 类型发挥作用的地方

```
let search = new URLSearchParams(this.props.location.search); 
```

这封装了根据关键字提取搜索查询值所需的逻辑。

```
let search = new URLSearchParams(this.props.location.search);
let name = search.get("name"); 
```

当与上面的 URL“http://一起使用时.../用户？名字=约翰”，我们得到的名字等于“约翰”。太好了，我们已经准备好开始拼凑一个简单的组件来消费搜索查询中的值。

```
import * as React from "react";
import { RouteComponentProps, withRouter } from "react-router";

class SimpleMainComponent extends React.Component<RouteComponentProps<any>> {
  render() {
    let name = this.getName();
    return (
      <div>
        name is: {name}
      </div>
    );
  }

  getName(): string {
    let search = this.getUrlParams();
    return search.get("name") || "";
  }

  getUrlParams(): URLSearchParams {
    if (!this.props.location.search) return new URLSearchParams();
    return new URLSearchParams(this.props.location.search);
  }
} 
```

### 有境界问题

下一步开始问题。如果我们同时使用 redux 和这个组件，我们会遇到这样的问题:我们可能会读取名称，将其存储在 redux 中，导航到应用程序的不同区域，然后稍后返回到这个组件。在这种情况下，url 现在是错误的，尽管我们可能会得到“/users”我们的搜索参数已经消失了。我们可能在 redux 中正确设置了用户名，但是搜索参数与显示的内容不一致，因此如果我们刷新页面，将会得到不同的结果。在这种情况下，当我们回到这个组件时，我们可能希望将搜索查询字符串附加到路由的末尾。为此，我们需要将该字符串存储在某个地方。

如果我们重温刚刚制作的组件，我们可以通过将它置于状态来模仿这种行为。我想引起注意的具体行为是当状态或道具改变时触发的重新渲染。我们很快遇到的一个问题是，如果我们简单地将搜索参数设置为类似于这样的状态，那么就需要不断地重新呈现组件

```
 state = {
    search: this.props.location.search,
  };

  componentDidMount() {
    this.setState({ search: this.getUrlParams().toString() });
  }

  componentDidUpdate() {
    this.setState({ search: this.getUrlParams().toString() });
  } 
```

这显然违反了设置状态，但是很容易被忽略，特别是如果您打破了获取搜索参数、找到您想要的值并更新状态的逻辑。解决方法是什么？

```
 componentDidMount() {
    let search = this.getUrlParams().toString();
    this.setState({ search: search });
  }

  componentDidUpdate() {
    let search = this.getUrlParams().toString();
    if (this.didQueryChange(search)) this.setState({ search: search });
  }
...

didQueryChange(search: string): boolean {
    return this.state.search !== search;
  } 
```

有了这个检查，我们可以安全地更新我们的道具或状态，而不会导致无限的重新渲染循环

```
import * as React from "react";
import { RouteComponentProps, withRouter } from "react-router";

type State = { search: string; count: number };

class MainComponent extends React.Component<RouteComponentProps<any>, State> {
  state = {
    search: this.props.location.search,
    count: 0
  };

  componentDidMount() {
    let search = this.getUrlParams().toString();
    this.setState({ search: search });
  }

  componentDidUpdate() {
    let search = this.getUrlParams().toString();
    if (this.didQueryChange(search)) this.setState({ search: search });
  }

  render() {
    let name = this.getName();
    return (
      <div>
        name is: {name}{" "}
        <button onClick={this.increaseCount}>count: {this.state.count}</button>
      </div>
    );
  }

  increaseCount = () => this.setState({ count: this.state.count + 1 });

  getName(): string {
    let search = this.getUrlParams();
    return search.get("name") || "";
  }

  didQueryChange(search: string): boolean {
    return this.state.search !== search;
  }

  getUrlParams(): URLSearchParams {
    if (!this.props.location.search) return new URLSearchParams();
    return new URLSearchParams(this.props.location.search);
  }
}

export default withRouter(MainComponent); 
```

我们在比较的时候很大的一个问题就是****this . props . location . search**不等于**new URLSearchParams(this . props . location . search)。**toString()**。这似乎是显而易见的，但这之前已经导致了堆缓存，因为像 **getUrlParams()** 这样的方法很容易使用，处理搜索查询的安全重试，以至于很容易做类似于
的事情

```
this.state.search !== this.props.location.search;
...
this.setState({ search: this.getUrlParams().toString()}) 
```

但是这些评估做不同的事情

*   **this . props . location . search**= "？name=john "
*   **this.getUrlParams()。toString** = "name=john "

您可以将搜索字符串状态设置为任何一种，但是要注意，当您对有状态字符串的比较和设置使用相同的重试方法时

### 最后提示

探索 URLSearchParams 类型，因为它不仅仅是读取值，您还可以使用该类型来设置值，例如

```
let values = this.getUrlParams();
values.append("count",this.state.count.toString())
this.props.history.push({search:values.toString()}) 
```

### 总结

在 react with typescript 中开始访问 URL 搜索参数是相当快速和容易的，我们只需记住在获取和设置值时使用 **URLSearchParams** 类型。当我们开始尝试将这一点添加到应用程序的状态中时，我们很容易遇到问题，因此需要防范无限的呈现问题。

本教程的代码可以在 [github](https://github.com/alexs1305/search-params-with-react-typescript) 中找到，或者在[代码沙箱](https://codesandbox.io/s/k0vjrrkwjv)上玩