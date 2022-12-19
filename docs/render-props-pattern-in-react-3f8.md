# 在 React 中渲染道具图案

> 原文：<https://dev.to/maksimovicdanijel/render-props-pattern-in-react-3f8>

Render props 是由 reacttraining.com 的 Michael Jackson 和 Paypal 的 Kent C. Dodds 等人推广的一种模式。我们可以看到这种模式在 React 本身及其生态系统(上下文 API、React 路由器、Apollo 客户端、react-motion、降档等)中越来越多地被使用。).

## 你什么时候会用这种模式？

当你构建一个组件时，应该使用这种模式，这个组件不关心渲染，但是它有一些重要的业务逻辑要为你的应用程序做。通过这种方式，您可以为业务逻辑重用该组件(获取数据、进行计算等)。).

## 举例

```
// component that implements render prop
import React from 'react';
import PropTypes from 'prop-types';

import { fetchPosts } from './api';

class FetchPosts extends React.Component {
  static propTypes = {
    children: PropTypes.func
  };

  state = { articles: [], loading: false };

  async componentDidMount() {
    this.setState({ loading: true });

    const articles = await fetchPosts();

    this.setState({ articles, loading: false });  
  }

  render() {
    return this.props.children({articles: this.state.articles});
  }
}

// component that is using render prop component
import React from 'react';

import FetchPosts from './FetchPosts';

const ArticleList = props => {
  return (
    <FetchPosts>
      {({ loading, articles }) => {
        if (loading) {
          return 'Loading...';
        }

        return articles.map(article => <ArticleListItem article />);
      }}
    </FetchPosts>
  );
}; 
```

在我们的例子中，我们有一个组件，它仅用于获取数据，并将渲染留给作为道具传递的子对象(因此得名渲染道具)。组件正在使用该数据来呈现文章列表。我们可以使用不同的组件来呈现文章的网格列表，从我们的`FetchPosts`组件获取数据。

## 来自社区的例子

### React 的上下文 API

让我们来看看 React 的上下文 API 示例:

```
import React from 'react';

const state = {
  counter: 0

const { Provider, Consumer } = React.createContext(state);

const App = props => {
  return (
    <Provider value={state}>
      <Counter />
    </Provider>
  );
}

export default App;
export { Consumer };

// Counter.js
import React from 'react';

import {Consumer} from './App';

const Counter = props => {
  return (
    // render props used here
    <Consumer>
      {state => (
        <p>{state.counter}</p>
      )}
    </Consumer>
  );
} 
```

在我们的应用程序组件中，我们使用`React.createContext`创建新的上下文，并为上下文传递默认值。`Provider` component 用于设置值，该值将提供给请求该值的组件。

在我们的`Counter`组件中，我们使用的`Consumer`组件是`createContext`调用的结果，该组件实现了渲染道具模式。`Consumer`组件的子组件是一个被调用的函数，其值作为一个道具提供给`Provider`组件。

### React 路由器路由组件

React 路由器是 React 社区中最常用的包之一。它还实现了 render props 模式(它没有使用 children 作为函数，而是使用了`render` prop)。传入示例:

```
import React from 'react';
import {BrowserRouter as Router, Route} from 'react-router-dom';

const App = props => {
  return (
    <Router>
      <Route path='/posts' render={(props) => (// ...do something with props and render to UI)} />
    </Router>
  );
} 
```

如你所见,`Route`组件接受`render`道具，这是一个获取道具(历史、位置、比赛等)的函数。)

### 阿波罗客户端的查询组件

React 的 Apollo 客户端实现在其`Query`组件中使用渲染道具。用法示例:

```
import React from 'react';
import {Query} from 'react-apollo';

const App = props => {
  return (
    <Query query={POSTS_QUERY} variables={variables}>
      {({data, loading, error, fetchMore}) => {
        if (loading) {
          return 'Loading...';
        }

        if (error) {
          return error;
        }

        return <ArticleList data={data} />
      }}
    </Query>
  );
}; 
```

关于从渲染属性返回的数据，这个例子有点复杂。当组件渲染它的子组件时，它传递一个对象，这个对象有很多我们可以使用的属性。一般来说，你的函数只接受一个参数，并且在实现函数时你是在析构属性。但是你不一定要这么做，它仍然是一个函数，你可以用你想要的方式来实现它。

## 结论

根据我的观点和我每天使用 React 的经验，我发现渲染道具是一种被广泛使用的模式，它非常适合 React 的组件组合模式。我鼓励您考虑如何在代码中使用这种模式，使其更具可读性和可重用性。另外，请在评论中分享你对这种模式的使用👇感谢阅读😄