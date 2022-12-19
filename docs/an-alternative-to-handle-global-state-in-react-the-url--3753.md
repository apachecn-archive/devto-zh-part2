# React 中处理状态的另一种方法:URL！

> 原文：<https://dev.to/gaels/an-alternative-to-handle-global-state-in-react-the-url--3753>

要获得这篇文章的法语版本，请点击[这里](https://www.apprendre-react.fr/tutorial/library/state/Supprimer-Redux-en-utilisant-l-url-de-votre-application/):)

# react app 的状态

管理 React 应用程序的全局状态是库之间无休止的战争。因此，为了增加我的 2 美分，我们将看到如何使用 URL 和 react-router 做完全相同的事情。

# 网址 FTW

在单页应用程序中，URL 并不重要。大多数时候，它只是一个请求所有资产的端点，仅此而已。

登陆`https://myApp.io` ou `https://myApp.io?user=gael&job=dev`不会影响你第一次加载应用时看到的内容。

让我们改变这一点。

# 一些代码

我开始在一个*边项目* ( [代码](https://github.com/GaelS/first-contrib-app)和[演示](https://first-contrib.surge.sh))中使用开发这个想法

然而，在这篇文章中，我重新开发了一个 [codesandbox](https://codesandbox.io/s/5v0v7vwo6k) ,只关注这里重要的内容。

首先，我们将如何使用 URL？

嗯，我们将使用位于**之后的所有内容？**在 URL 中，所谓的*搜索参数*。

> 这里有一些关于这个主题的文件。

## 从 URL 获取值

在本文的上下文中，我们将只寻找一个参数，我们将其命名为 *query* 。

为了检索该参数(如果它确实在 URL 中，例如`https://myApp.io?query=javascript`，我们将检查...*搜索参数*。幸运的是，它们很容易被发现，位于物体`window`中。更确切地说是在`winndow.location.search`。

因此，如果我们请求:`www.first-contrib?query=react`，我们在控制台中得到的是:

`console.log(window.location.search); // "?query=react"`

理想情况下，操作正确格式化的 JS 对象比操作`string`更方便。为了实现这一点，我们将使用最近浏览器中可用的`URLSearchParams`对象，而不是通过`=`和`?`来分割 URL。否则，你可以用这个[库](https://www.npmjs.com/package/url-search-params-polyfill)填充它。

对于代码，它屈服于:

```
function getParams(location) {
  const searchParams = new URLSearchParams(location.search);
  return {
    query: searchParams.get('query') || '',
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，

```
const params = getParams('www.first-contrib.fr?query=react');

console.log(params) // { query: "react" } 
```

Enter fullscreen mode Exit fullscreen mode

既然我们可以从 URL 中获取一个对象，我们将让它与使用 **react-router** 的应用程序一起工作。因此，我们将创建一个`router`，它处理一个`route`，以`props`的身份获取 URL 中的属性。

```
import React from "react";
import { render } from "react-dom";
import { BrowserRouter as Router, Route } from "react-router-dom";

// ...
// getParams code above

//a simple component to display 
//the value of the query ...
// which is for now unknown
//so we'll instantiate it with an empty value
const MainPage = (props) => {
  let query = '';
  return (
    <h2>{`Query : ${query}`}</h2>
  );
}

const App = () => (
  <React.Fragment>
    <Router>
      <React.Fragment>
        <Route path="/" component={MainPage} />
      </React.Fragment>
    </Router>
  </React.Fragment> );

render(<App />, document.getElementById("root")); 
```

Enter fullscreen mode Exit fullscreen mode

为了获得`query`的实际值，我们必须操作我们编写的函数`getParams`以及`MainPage`从`Route`对象`<Route path="/" component={MainPage} />`接收的*隐式* `props`

如果我们记录那个`props`，我们会得到:

`{match: Object, location: Object, history: Object, /*other stuff */}`

这里感兴趣的是与我们之前操作的`window.location`相似的`location`对象。因此，我们可以更新`MainPage`组件来获取 URL:
中的值

```
const MainPage = (props) => {
  const { location } = props;
  const { query } = getParams(location);

  return (
      <h2>{`My query: ${query}`}</h2>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

现在`MainPage`被插到网址上了！

## 更新网址(和状态)！

既然我们可以读取 URL，我们将实现一种方法来根据应用程序的状态更新 URL。

为此，一个简单的`input`就可以了:

```
class InputPage extends React.Component {

  state = { inputValue: "" };

  updateInputValue = e => this.setState({ inputValue: e.target.value });

  render() {
    return (
      <React.Fragment>
        <input
          type="text"
          placeholder="Change your URL !"
          value={this.state.inputValue}
          onChange={this.updateInputValue}
        />
        <input type="button" value="Change the URL" onClick={null} />
      </React.Fragment>     );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们的组件管理一个内部状态来显示它的当前值，但是我们仍然需要实现`onClick`函数来用相同的值更新 URL。

我们看到来自`Route`的隐式`props`对象看起来像这样:

`{match: Object, location:Object, history: Object, /*d'autres valeurs */}`

这里感兴趣的是`history`(关于`history`的附加信息[这里](https://reacttraining.com/react-router/web/api/history)...)

由于其`push`功能，根据**反应路由器**文档:

> 将新条目推送到历史堆栈上

简单来说，`push`将允许我们更新网址！

因此，如果我们输入中的`query`值是`javascript`，我们必须将我们的 URL 更新为下面的值:`www.myApp.io?query=javascript`。因此，我们需要生成 URL 的新的`searchParams`。为了做到这一点，`URLSearchParams`对象将再次帮助我们:

```
function setParams({ query = ""}) {
  const searchParams = new URLSearchParams();
  searchParams.set("query", query);
  return searchParams.toString();
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，如果查询没有默认值，那么查询实际上是`undefined`，生成的 URL 将是`?query=undefined`...

现在我们可以写:

```
const url = setParams({ query: "javascript" });
console.log(url); // "query=javascript" 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以在输入组件中实现`onClick`。

```
class InputPage extends React.Component {
  state = { inputValue: "" };
  updateInputValue = e => this.setState({ inputValue: e.target.value });

  updateURL = () => {
    const url = setParams({ query: this.state.inputValue });
    //do not forget the "?" !
    this.props.history.push(`?${url}`);
  };

  render() {
    return (
      <React.Fragment>
          <input
          type="text"
          className="input"
          placeholder="What am I looking for ?"
          value={this.state.inputValue}
          onChange={this.updateInputValue}
        />
        <input
          type="button"
          className="button"
          value="Update the URL !"
          onClick={this.updateURL}
        />
      </React.Fragment>     );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们更改输入值，单击按钮将触发 URL 的更新，`MainPage`将相应地显示新值！

将应用程序的状态放在 URL 中的好处之一就是当你复制/粘贴链接时。鉴于这个 URL 中包含了状态，我们将在第一次加载时找到处于特定状态的应用程序！

例如，当你处理一个搜索引擎时，你可以在应用程序加载后立即触发查询。在这个[应用程序](https://github.com/GaelS/first-contrib-app)中，我使用了 [react-apollo](https://github.com/apollographql/react-apollo) ，但是以简单的方式，我们可以用任何 HTTP 客户端实现同样的事情。

让我们创建一个组件，一旦使用它的一些生命周期方法得到了`props`，它就使用 [axios](https://github.com/axios/axios) 和 Github REST API(不需要任何认证)来触发请求。

```
 const httpClient = axios.create({
  baseURL: "https://api.github.com"
});

class ResultsPage extends React.Component {
  state = { results: [], loading: false, error: false };

  //Search as soon as it is mounted !!
  componentDidMount() {
    return this.searchRepositories(this.props.query);
  }

  //Search as soon as query value is updated
  componentWillReceiveProps(nextProps) {

    if (nextProps.query !== this.props.query) {
      this.setState({ query: nextProps.query });
      return this.searchRepositories(nextProps.query);
    }
  }

  searchRepositories = query => {

    //handle if query is undefined
    if (!query) {
      return this.setState({
        results: []
      });
    }

    this.setState({ loading: true, error: false });

    //the actual search on Github
    return httpClient
      .get(`/search/repositories?q=${query}`)
      .then(({ data }) =>
        this.setState({
          results: data.items,
          loading: false
        })
      )
      .catch(e => this.setState({ loading: false, error: true }));
  };

  render() {
    return (
      <div>
        {this.state.results.map(repo => (
          <div key={repo.id}>
            <a href={repo.html_url}>
              {repo.name}
            </a>
            <div>{`by ${repo.owner.login}`}</div>
          </div>
        ))}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们现在有了一个组件，每当 URL 中包含的`query`参数被更新时，它就会触发请求！

如前所述，你可以在这里找到一个活生生的例子。

在我们的例子中，我们只处理一个`searchParams`,但是如果有更多的组件可以修改 URL，这将变得非常酷和强大。例如，分页、过滤、排序等可以成为 URL 的参数。它看起来可能是这样的:`https://myApp.io?query=react&sort=ASC&filter=issues&page=2`。

代码将类似于我们以前所做的。通过修改 URL，由`Route`组件提供的隐式`props`被更新。然后，它触发一个重新呈现器，并更新所有监听 URL 中特定值的子节点。因此，它会导致 UI 更新或副作用，如 HTTP 调用。

# 结论

就是这样！本文试图展示仍然有其他方法来处理 React 应用程序的全局状态，这些方法在包大小方面可以非常小(在现代浏览器中为 0kb)，并且仍然有趣、简单，并且提供免费的深度链接效果，我觉得这非常酷:)

希望你喜欢！