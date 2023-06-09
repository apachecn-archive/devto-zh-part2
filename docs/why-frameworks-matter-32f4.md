# 为什么框架很重要

> 原文：<https://dev.to/rwieruch/why-frameworks-matter-32f4>

*最初发布于个人网站:[为什么框架很重要](https://www.robinwieruch.de/why-frameworks-matter)*

现在有很多人进入 web 开发领域。对于新手来说，了解现代 web 开发中使用的所有工具可能是一种压倒性的体验。在 Netscape 中运行 HTML 与今天之间的历史差距随着这些新工具的出现而扩大，这些新工具被放在技术栈的顶部。在某种程度上，新人学习 jQuery 已经没有任何意义了。学生们在学习完普通的 JavaScript 后会直接跳到他们最喜欢的框架上。对于这些人来说，缺少的是这两者之间的所有历史知识。

在本文中，我想重点介绍从普通 JavaScript 到 React 这样的现代库的飞跃。当人们打算使用这样一个库时，他们通常从未经历过导致这些解决方案的过去的斗争。要问的问题是:为什么我们最终会有这些库？我想展示为什么像 React 这样的库很重要，为什么你不想再用普通的 JavaScript 实现应用程序。整个故事可以类比应用于任何其他库/框架，如 Vue、Angular 或 Ember。

我想展示如何用普通的 JavaScript 构建一个小应用程序并做出反应。如果您是 web 开发的新手，那么它应该会给你一个清晰的对比，说明为什么要使用一个库来用 JavaScript 构建一个更大的应用程序。下面这个小应用程序的大小正好适合普通的 JavaScript，但是它清楚地表明了为什么一旦你打算扩展它，你会选择一个库。您可以在这个 [GitHub 库](https://github.com/rwieruch/why-frameworks-matter)中检查完成的应用程序。如果能找到贡献者来为其他库/框架添加实现，那就太好了。

# 用普通 JavaScript 解决问题

让我们一起用普通的 JavaScript 构建一个应用程序。问题:搜索来自[黑客新闻](https://hn.algolia.com/api)的报道，并在你的浏览器列表中显示结果。该应用程序只需要一个搜索请求的输入字段和一个显示结果的列表。如果提出新的搜索请求，必须在浏览器中更新列表。

在你选择的文件夹中，创建一个*index.html*文件。让我们在这个文件中写几行 HTML 代码。首先，必须有 HTML 样板文件将内容呈现给浏览器。

```
<!DOCTYPE html>
<html>
  <head>
    Vanilla JavaScript
  </head>
  <body>
  </body>
  <script src="index.js"></script>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

重要的部分是导入的 *index.js* 文件。这是普通 JavaScript 代码将要结束的文件。你可以在你的 index.html 文件旁边创建它。但是在您开始编写 JavaScript 之前，让我们再添加一些 HTML。应用程序应该显示一个输入字段和一个按钮，根据输入字段的搜索查询请求数据。

```
<!DOCTYPE html>
<html>
  <head>
    Vanilla JavaScript
  </head>
  <body>
    <div id="app">
      <h1>Search Hacker News with vanilla JavaScript</h1>
      <input id="searchInput" />
      <button id="searchButton">Search</button>
    </div>
  </body>
  <script src="index.js"></script>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经注意到，还没有显示所请求内容的容器。在一个完美的世界中，会有某种元素本身具有多个元素来显示来自黑客新闻的请求的故事。由于这些内容在请求发生之前是未知的，因此在请求发出后动态呈现是一种更好的方法。在下一部分中，您将通过使用 DOM API 进行 HTML 操作来用 JavaScript 实现这一点。

稍后可以使用 id 为`app`的 HTML 元素将 JavaScript 与 DOM 挂钩。此外，button 元素可用于分配一个单击事件侦听器。这是开始 JavaScript 代码的最佳位置。让我们从 *index.js* 文件开始。

```
function addButtonEvent() {
  document.getElementById('searchButton')
    .addEventListener('click', function () {
      // (4) remove old list if there is already a list

      // (1) get value from input field
      // (2) search list from API with value

      // (3) append list to DOM
    });
};

addButtonEvent(); 
```

Enter fullscreen mode Exit fullscreen mode

这基本上是应用程序所需的一切。一旦 *index.js* 文件运行，就会有一个事件监听器添加到 id 为`searchButton`的按钮元素中。你可以在你的*index.html*文件中找到按钮元素。

最后一行很重要，因为首先必须有人调用这个函数。函数本身只是声明，而不是执行。下面所有的实现都只是一些功能，一旦有人点击按钮，这些功能就会被执行。

代码中的注释向您展示了将逐步实现的业务逻辑。这里让我们尽量保持代码简洁。您可以提取在按钮单击事件中调用的函数。

```
function addButtonEvent() {
  document.getElementById('searchButton')
    .addEventListener('click', onSearch);
};

function onSearch() {

}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们实现单击按钮后的业务逻辑。需要做三件事。首先，您需要从用于搜索请求的 HTML 输入字段中检索值。其次，您必须发出异步搜索请求。第三，您需要将搜索请求的结果附加到 DOM 中。

```
function addButtonEvent() {
  document.getElementById('searchButton')
    .addEventListener('click', onSearch);
};

function onSearch() {
  doSearch(getValueFromElementById('searchInput'))
    .then(appendList);
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在有三个函数，您将在以下步骤中实现它们。首先，让我们从 id 为`searchInput`的输入元素中检索值。

```
function onSearch() {
  doSearch(getValueFromElementById('searchInput'))
    .then(appendList);
};

function getValueFromElementById(id) {
  return document.getElementById(id).value;
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果您在浏览器中的呈现 HTML 输入字段中键入了一些内容，那么一旦您单击该按钮，它就会被选中。现在这个值应该用在您将在第二部分实现的`doSearch()`函数中。该函数返回一个[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)，因此在第三步中可以使用`then()`方法追加结果(列表)。

```
var BASE_URL = 'https://hn.algolia.com/api/v1/';

function doSearch(query) {
  var url = BASE_URL + 'search?query=' + query + '&hitsPerPage=200';
  return fetch(url)
    .then(function (response) {
      return response.json();
    })
    .then(function (result) {
      return result.hits;
    });
}

function onSearch() {
  doSearch(getValueFromElementById('searchInput'))
    .then(appendList);
}; 
```

Enter fullscreen mode Exit fullscreen mode

该函数使用返回承诺的[本地获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 。为了简化起见，我在这个场景中保留了可以在`catch()`块中实现的错误处理。向 Hacker News API 发出请求，并使用字符串连接插入来自输入字段的值。之后，响应被转换，结果中只返回`hits` (list)。第三步是将列表附加到 DOM 中。

```
function onSearch() {
  doSearch(getValueFromElementById('searchInput'))
    .then(appendList);
};

function appendList(list) {
  var listNode = document.createElement('div');
  listNode.setAttribute('id', 'list');
  document.getElementById('app').appendChild(listNode);

  // append items to list
}; 
```

Enter fullscreen mode Exit fullscreen mode

首先，创建一个新的 HTML 元素，然后给该元素一个 id 属性进行检查。一旦发出第二个请求，就可以使用这个 id 来检查 DOM 中是否已经有一个列表。第三，使用 id 为`app`的 HTML 元素将新元素添加到 DOM 中，这个 id 可以在*index.html*文件中找到。缺少的是追加项目列表。

```
function onSearch() {
  doSearch(getValueFromElementById('searchInput'))
    .then(appendList);
};

function appendList(list) {
  var listNode = document.createElement('div');
  listNode.setAttribute('id', 'list');
  document.getElementById('app').appendChild(listNode);

  list.forEach(function (item) {
    var itemNode = document.createElement('div');
    itemNode.appendChild(document.createTextNode(item.title));
    listNode.appendChild(itemNode);
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

对于列表中的每一项，创建一个新的 HTML 元素，将文本追加到元素中，并将元素追加到列表 HTML 元素中。你可以把函数提取出来，让它再简洁一点。因此，您必须使用一个高阶函数来将列表元素传递给函数。

```
function onSearch() {
  doSearch(getValueFromElementById('searchInput'))
    .then(appendList);
};

function appendList(list) {
  var listNode = document.createElement('div');
  listNode.setAttribute('id', 'list');
  document.getElementById('app').appendChild(listNode);

  list.forEach(appendItem(listNode));
};

function appendItem(listNode) {
  return function (item) {
    var itemNode = document.createElement('div');
    itemNode.appendChild(document.createTextNode(item.title));
    listNode.appendChild(itemNode);
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

这就是实施的三个步骤。首先，从输入字段中检索值，其次，使用值执行异步请求，从 Hacker News API 的结果中检索列表。第三，将 list 和 item 元素追加到 DOM 中。

最后但同样重要的是，还缺少一个关键部分。当从 API 请求新列表时，不要忘记从 DOM 中删除列表。否则，来自搜索请求的新结果将被追加到您在 DOM 中的前一个结果。

```
function onSearch() {
  removeList();

  doSearch(getValueFromElementById('searchInput'))
    .then(appendList);
};

function removeList() {
  var listNode = document.getElementById('list');

  if (listNode) {
    listNode.parentNode.removeChild(listNode);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到，要解决文章中定义的问题需要做大量的工作。需要有人负责大教堂。DOM 更新在这里以一种非常幼稚的方式发生，因为如果已经有一个结果的话，它只是删除一个先前的结果，并再次将新的结果附加到 DOM 中。一切都可以很好地解决定义的问题，但是一旦您添加功能或扩展应用程序的特性，代码就会变得复杂。

如果你还没有安装 npm，先通过[节点](https://nodejs.org/en/)安装。最后，您可以在本地浏览器中测试您的两个文件，方法是在命令行上安装一个带有 npm 的 HTTP 服务器。

```
npm install http-server -g 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以在您创建 index.html 和 index.js 文件的目录中从命令行启动 HTTP 服务器:

```
http-server 
```

Enter fullscreen mode Exit fullscreen mode

输出应该会给出一个 URL，您可以在浏览器中找到您的应用程序。

# 在 React 中解决同样的问题

在文章的这一部分，您将使用 React 解决同样的问题。它应该给你一种方法来比较这两种解决方案，并可能说服你为什么像 React 这样的库是解决这类问题的合适工具。

该项目将再次由一个*index.html*和 *index.js* 文件组成。我们的实现再次从 index.html 文件中的 HTML 样板文件开始。它需要两个必要的 React 和 ReactDOM 库。后者用于将 React 挂钩到 DOM 中，而前者用于 React 本身。此外， *index.js* 也包含在内。

```
<!DOCTYPE html>
<html>
  <head>
    React
    <script src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
  </head>
  <body>
    <script src="index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

其次，添加 Babel 将您的 JavaScript 代码转换成普通 JavaScript，因为您的 *index.js* 文件中的以下代码将使用非普通 JavaScript 功能，如 [JavaScript ES6 类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)。因此，你必须添加 Babel 来把它转换成普通的 JavaScript，使它能在所有浏览器中工作。

```
<!DOCTYPE html>
<html>
  <head>
    React
    <script src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/babel-standalone@6.26.0/babel.min.js"></script>
  </head>
  <body>
    <script type="text/babel" src="index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

第三，您必须定义一个带有 id 的元素。这是 React 可以与 DOM 挂钩的关键地方。不需要在你的*index.html*文件中定义更多的 HTML 元素，因为所有其他的都将在你的 *index.js* 文件中的 React 代码中定义。

```
<!DOCTYPE html>
<html>
  <head>
    React
    <script src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/babel-standalone@6.26.0/babel.min.js"></script>
  </head>
  <body>
    <div id="app" />
    <script type="text/babel" src="index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

让我们跳到 *index.js* 文件中的实现。首先，您可以像以前在普通 JavaScript 中一样，在文件的顶部定义搜索请求。

```
var BASE_URL = 'https://hn.algolia.com/api/v1/';

function doSearch(query) {
  var url = BASE_URL + 'search?query=' + query + '&hitsPerPage=200';
  return fetch(url)
    .then(function (response) {
      return response.json();
    })
    .then(function (result) {
      return result.hits;
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

既然您已经将 Babel 包含在您的*index.html*文件中，您可以通过使用[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)和[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)将最后一段代码重构为 JavaScript ES6。

```
const BASE_URL = 'https://hn.algolia.com/api/v1/';

function doSearch(query) {
  const url = `${BASE_URL}search?query=${query}&hitsPerPage=200`;
  return fetch(url)
    .then(response => response.json())
    .then(result => result.hits);
} 
```

Enter fullscreen mode Exit fullscreen mode

在下一部分中，让我们使用 ReactDOM 在 HTML 中挂接一个 React 组件。id 为`app`的 HTML 元素用于呈现名为 App 的第一个根组件。

```
class App extends React.Component {
  render() {
    return <h1>Hello React</h1>;
  }
}

ReactDOM.render(
  <App />,
  document.getElementById('app')
); 
```

Enter fullscreen mode Exit fullscreen mode

App 组件使用 React 的 JSX 语法来显示 HTML。在 JSX，你也可以使用 JavaScript。让我们扩展呈现的输出来解决本文中定义的问题。

```
class App extends React.Component {
  render() {
    return (
      <div>
        <h1>Search Hacker News with React</h1>
        <form type="submit" onSubmit={}>
          <input type="text" onChange={} />
          <button type="text">Search</button>
        </form> 
        {/* show the list of items */}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

该组件呈现一个带有输入元素和按钮元素的表单。此外，最后还有一个占位符来呈现来自搜索请求的列表。缺少输入元素和表单提交的两个处理程序。在下一步中，您可以在组件中以声明的方式将处理程序定义为类方法。

```
class App extends React.Component {
  constructor() {
    super();

    this.onChange = this.onChange.bind(this);
    this.onSubmit = this.onSubmit.bind(this);
  }

  onSubmit(e) {
    e.preventDefault();
  }

  onChange(e) {

  }

  render() {
    return (
      <div>
        <h1>Search Hacker News with React</h1>
        <form type="submit" onSubmit={this.onSubmit}>
          <input type="text" onChange={this.onChange} />
          <button type="text">Search</button>
        </form> 
        {/* show the list of items */}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后一段代码展示了 React 的声明能力。您可以基于定义良好的类方法来实现 HTML 中每个处理程序正在做的事情。这些可以用作处理程序的回调。

每个处理器都可以访问 React 的[合成事件](https://reactjs.org/docs/events.html)。例如，当有人在字段中键入内容时，它可以用来从`onChange()`处理程序的 input 元素中检索值。您将在下一步中执行此操作。

请注意，该事件已经在“onSubmit()”类方法中使用，以防止本机浏览器行为。通常浏览器会在提交事件后刷新页面。但是在 React 中你不想刷新页面，你只想让 React 处理它。

让我们在 React 中输入状态处理。您的组件必须管理状态:输入字段中的值和最终从 API 中检索的项目列表。它需要知道这些状态，以便从搜索请求的输入字段中检索值，并最终呈现列表。因此，您可以在其构造函数中定义组件的初始状态。

```
class App extends React.Component {
  constructor() {
    super();

    this.state = {
      input: '',
      list: [],
    };

    this.onChange = this.onChange.bind(this);
    this.onSubmit = this.onSubmit.bind(this);
  }

  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以通过使用 React 的本地状态管理来更新输入字段值的状态。在 React 组件中，您可以访问`setState()`类方法来更新本地状态。它使用浅层合并，因此在更新输入状态时不需要担心列表状态。

```
class App extends React.Component {
  constructor() {
    super();

    this.state = {
      input: '',
      list: [],
    };

    this.onChange = this.onChange.bind(this);
    this.onSubmit = this.onSubmit.bind(this);
  }

  ...

  onChange(e) {
    this.setState({ input: e.target.value });
  }

  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

通过在组件中使用`this.state`,您可以再次从组件中访问状态。您应该向 input 元素提供更新后的输入状态。这样，你就可以控制元素的状态，而不是元素自己不做。它变成了所谓的[受控组件](https://reactjs.org/docs/uncontrolled-components.html)，这是 React 中的最佳实践。

```
class App extends React.Component {
  constructor() {
    super();

    this.state = {
      input: '',
      list: [],
    };

    this.onChange = this.onChange.bind(this);
    this.onSubmit = this.onSubmit.bind(this);
  }

  ...

  onChange(e) {
    this.setState({ input: e.target.value });
  }

  render() {
    return (
      <div>
        <h1>Search Hacker News with React</h1>
        <form type="submit" onSubmit={this.onSubmit}>
          <input type="text" onChange={this.onChange} value={this.state.input} />
          <button type="text">Search</button>
        </form> 
        {/* show the list of items */}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦组件的本地状态在 React 中更新，组件的`render()`方法就会再次运行。因此，在呈现元素时，您总是可以获得正确的状态。如果您再次改变状态，例如通过在输入字段中键入一些内容，`render()`方法将再次为您运行。当事情发生变化时，您不必担心创建或删除 DOM 元素。

在下一步中，您将调用已定义的`doSearch()`函数向 Hacker News API 发出请求。它应该发生在`onSubmit()`类方法中。请求成功解决后，您可以为列表属性设置新的状态。

```
class App extends React.Component {
  constructor() {
    super();

    this.state = {
      input: '',
      list: [],
    };

    this.onChange = this.onChange.bind(this);
    this.onSubmit = this.onSubmit.bind(this);
  }

  onSubmit(e) {
    e.preventDefault();

    doSearch(this.state.input)
      .then((hits) => this.setState({ list: hits }));
  }

  ...

  render() {
    return (
      <div>
        <h1>Search Hacker News with React</h1>
        <form type="submit" onSubmit={this.onSubmit}>
          <input type="text" onChange={this.onChange} value={this.state.input} />
          <button type="text">Search</button>
        </form> 
        {/* show the list of items */}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦请求成功完成，状态就会更新。一旦状态被更新，`render()`方法再次运行，您可以使用 JavaScript 的内置 map 功能，使用状态中的列表来呈现您的元素。

**阅读更多关于[在 React](https://www.robinwieruch.de/react-fetching-data/) 中获取数据的信息**

这就是 React 中 JSX 的强大之处，因为您可以使用普通的 JavaScript 来呈现多个元素。

```
class App extends React.Component {
  constructor() {
    super();

    this.state = {
      input: '',
      list: [],
    };

    this.onChange = this.onChange.bind(this);
    this.onSubmit = this.onSubmit.bind(this);
  }

  onSubmit(e) {
    e.preventDefault();

    doSearch(this.state.input)
      .then((hits) => this.setState({ list: hits }));
  }

  ...

  render() {
    return (
      <div>
        <h1>Search Hacker News with React</h1>
        <form type="submit" onSubmit={this.onSubmit}>
          <input type="text" onChange={this.onChange} value={this.state.input} />
          <button type="text">Search</button>
        </form> 
        {this.state.list.map(item => <div key={item.objectID}>{item.title}</div>)}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。这两个类方法都以同步或异步的方式更新状态。在状态最终更新后，`render()`方法再次运行，并使用当前状态显示所有 HTML 元素。您不需要以强制的方式移除或追加 DOM 元素。您可以用声明的方式定义您希望组件显示的内容。

您可以像测试普通 JavaScript 应用程序一样测试这个应用程序。在命令行中，导航到您的文件夹，并使用 http-server 为应用程序提供服务。

总的来说，使用普通 JavaScript 和 React 的两个场景应该已经向您展示了命令式代码和声明式代码的巨大差异。在命令式编程中，你用代码*描述如何做某事*。这就是您在普通 JavaScript 场景中所做的。相反，在声明式编程中，你用你的代码*描述你想要做什么*。这就是 React 的强大之处，也是在普通 JavaScript 之上使用库的强大之处。

这两个例子的实现都很小，应该向您展示了这两种方法都可以解决这个问题。我认为普通的 JavaScript 解决方案更适合这个问题。然而，一旦扩展了应用程序，用普通的 JavaScript 来管理 DOM、DOM 操作和应用程序状态就变得更加复杂了。总有一天，您会得到臭名昭著的意大利面条式代码，就像过去许多 jQuery 应用程序发生的那样。在 React 中，您保持代码的声明性，可以用组件描述整个 HTML 层次结构。这些组件管理它们自己的状态，可以被重用和相互组合。你可以用它们来描述一个完整的组件树。React 使您的应用程序保持可读性、可维护性和可伸缩性。将一个组件拆分成多个组件相当简单。

```
class App extends React.Component {
  ...

  render() {
    return (
      <div>
        <h1>Search Hacker News with React</h1>
        <form type="submit" onSubmit={this.onSubmit}>
          <input type="text" onChange={this.onChange} value={this.state.input} />
          <button type="text">Search</button>
        </form>
        {this.state.list.map(item =>
          <Item key={item.objectID} item={item} />
        )}
      </div>
    );
  }
}

const Item = ({ item }) =>
  <div>{item.title}</div> 
```

Enter fullscreen mode Exit fullscreen mode

最后一段代码展示了如何从 App 组件中提取另一个组件。这样，您可以扩展您的组件层次结构，并维护与组件共存的业务逻辑。用普通的 JavaScript 维护这样的代码要困难得多。

你可以在[这个 GitHub 库](https://github.com/rwieruch/why-frameworks-matter)里找到所有的解决方案。JavaScript ES6 也有一个解决方案，可以用在普通 JavaScript 和 React 方法之间。为 Angular、Ember 和其他解决方案找到实施示例的贡献者将是非常棒的。请随意投稿:)

如果你喜欢这段从普通 JavaScript 到 React 的旅程，并且你决定学习 React，请查看[学习 React 之路](https://www.robinwieruch.de/the-road-to-learn-react/)作为你学习 React 的下一段旅程。在这个过程中，您将顺利地从普通 JavaScript 过渡到 JavaScript ES6 甚至更高版本。

最后，永远记住有人在幕后为你实现这些解决方案。你可以偶尔在 Twitter 上鼓励一下贡献者，或者参与到开源项目中来，这样可以帮他们一个大忙。毕竟，没有人想再用普通的 JavaScript 构建更大的应用程序了。所以请珍惜你每天都在使用的库或框架:)