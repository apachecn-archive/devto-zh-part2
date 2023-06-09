# 高阶组件(HOC)反应模式

> 原文：<https://dev.to/superkarolis/higher-order-components-hoc-react-pattern-5ejp>

今天我决定更熟悉一些库中常用的高阶组件(HOC)，比如 Redux ( `connect`)、react-i18next ( `translate`)或 react-router ( `withRouter`)。我经常将它们作为上述库的一部分使用，但从未完全掌握它们的魔力。今天是到地毯下面看看它们是如何真正工作的日子。

但在我们开始之前，让我们定义一下什么是高阶分量。根据 [React docs](https://reactjs.org/docs/higher-order-components.html) 的说法，高阶组件是一种高级 React 模式，由一个函数组成，该函数将一个基本(包装)组件作为参数，并返回一个新的、增强的组件。它最大的优点是允许以一种干净的组合方式重用组件逻辑。

HOC 的抽象代码看起来有点像这样:

```
const higherOrderComponent = BaseComponent => {
    // ...
    // create new component from old one and update
    // ...

    return EnhancedComponent
} 
```

Enter fullscreen mode Exit fullscreen mode

在更具体的形式中，高阶分量总是采用类似于下面的形式:

```
import React from 'react';

const higherOrderComponent = BaseComponent => {
    class HOC extends React.Component {
        ...
        enhancements
        ...

        render() {
            return <BaseComponent newProp={newPropValue} {...this.props} />;
        }
    }

    return HOC;
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 简单例子

为了更好地理解，我们来看一个高阶元件的简单例子。想象一下，我们希望有几个组件，每次组件被渲染时都设置随机背景值。

我们首先定义一个名为`withColor`的高阶分量函数。它接受`BaseComponent`作为参数，并返回增强的组件。`EnhancedComponent`由一个`getRandomColor`和`render`方法组成。在`render`方法中，我们返回被分配了一个新的`color`道具的`BaseComponent`，并且通过利用`{...this.props}`的分解，`BaseComponent`还可以访问从 HOC 外部传递给`BaseComponent`的所有道具。

```
import React from 'react';

const withColor = BaseComponent => {
  class EnhancedComponent extends React.Component {
    getRandomColor() {
      var letters = '0123456789ABCDEF';
      var color = '#';

      for (let i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }

      return color;
    }

    render() {
      return <BaseComponent color={this.getRandomColor()} {...this.props} />;
    }
  }

  return EnhancedComponent;
};

export default withColor; 
```

Enter fullscreen mode Exit fullscreen mode

要使用新创建的 HOC，我们只需导出它，并将我们的基本组件作为参数传递。

```
import React from 'react';
import withColor from './withColor';

const ColoredComponent = props => {
  return <div style={{ background: props.color }}>{props.color}</div>; };

export default withColor(ColoredComponent); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们以与呈现常规组件相同的方式呈现新的增强组件。

```
import React, { Component } from 'react';
import ColoredComponent from './ColoredComponent';

class App extends Component {
  render() {
    return (
      <div>
        <ColoredComponent someProp="Prop 1" />
        <ColoredComponent someProp="Prop 2" />
        <ColoredComponent someProp="Prop 3" />
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在这一级定义的所有属性(如`someProp`)将通过`{...this.props}`进一步向下传递，而上面示例中的新属性(如`color`)是在特设中明确定义的。

## 实际例子

前面的例子有点做作。例如，我们可以使用一个生成随机颜色的效用函数，然后在每个组件中调用它。使用更少的代码，最终结果将是相同的。让我们更好地看一个例子，其中高阶组件模式确实是最优雅的解决方案。

在下面的例子中，我们将开发一个高阶组件，它接受`BaseComponent`和 API URL 来获取它需要的数据。当数据被加载时，它将显示一个加载状态，一旦数据被加载，我们将显示任何基于数据的`BaseComponent`渲染。最终结果将如下所示。

[![HOC example](img/d992330e38790beb3550302055b919e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5CAapSJ2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.karolisram.com/conteimg/2018/07/loading_gif.gif)

首先，我们将创建一个高阶组件函数。我们称它为`withLoader`,并且最初将组件状态的`data`属性设置为`null`。一旦组件已经安装，我们将开始获取数据，当完成后，将`data`属性设置为返回的响应。

如前所述，当状态为`null`时，我们将显示加载状态。一旦我们获取了数据，我们将返回`BaseComponent`,作为回报，它将基于返回的数据呈现标记。高阶分量函数看起来如下:

```
import React from 'react';

const withLoader = (BaseComponent, apiUrl) => {
  class EnhancedComponent extends React.Component {
    state = {
      data: null,
    };

    componentDidMount() {
      fetch(apiUrl)
        .then(res => res.json())
        .then(data => {
          this.setState({ data });
        });
    }

    render() {
      if (!this.state.data) {
        return <div>Loading ...</div>;
      }

      return <BaseComponent data={this.state.data} {...this.props} />;
    }
  }

  return EnhancedComponent;
};

export default withLoader; 
```

Enter fullscreen mode Exit fullscreen mode

使用这个特设的组件相当简单。我们只需从`props`获取数据，并在需要时使用它。在下面的例子中，我们使用数据来显示用户列表。

像往常一样，我们首先需要导入高阶组件函数，并将组件作为函数参数传递。除此之外，我们还传递从中获取数据的 API URL。参见下面的基本组件代码:

```
import React from 'react';
import withLoader from './withLoader';

const Users = props => {
  return (
    <div>
      <h1>Users:</h1>
      <ul>{props.data.map(user => <li key={user.id}>{user.name}</li>)}</ul>
    </div>
  );
};

export default withLoader(Users, 'https://jsonplaceholder.typicode.com/users'); 
```

Enter fullscreen mode Exit fullscreen mode

类似地，我们使用另一个组件来显示帖子列表。除了组件名和 API URL 之外，代码基本相同。

```
import React from 'react';
import withLoader from './withLoader';

const Posts = props => {
  return (
    <div>
      <h1>Posts:</h1>
      <ul>{props.data.map(post => <li key={post.title}>{post.title}</li>)}</ul>
    </div>
  );
};

export default withLoader(Posts, 'https://jsonplaceholder.typicode.com/posts/'); 
```

Enter fullscreen mode Exit fullscreen mode

现在剩下的就是渲染组件了。为了做到这一点，我们不需要做任何特别的事情。只需像往常一样导入组件并在需要的地方渲染它们。就这样😃

```
import React, { Component } from 'react';
import Users from './Users';
import Posts from './Posts';

class App extends Component {
  render() {
    return (
      <div>
        <Users />
        <Posts />
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

## 特设说明🤔

*   HOC 应该总是一个纯函数。这意味着相同的增强组件应该总是与作为参数传递的相同的基本组件一起返回。
*   不要在 render 方法中使用 hoc。这使得 React 的协调算法认为在每个渲染中重新声明了一个新组件，导致整个子树被卸载，而不仅仅是检查差异。
*   静态方法不会被隐式复制。这需要明确地完成。一个很好的方法是使用`hoist-non-react-statics`包。
*   裁判不会被放行。

## 总结🔥🔥🔥

高阶组件可以访问所有默认的 React API，包括状态和生命周期方法。这允许以一种非常有意识的方式重用逻辑，并使您的代码更加优雅。正如我们所见，它可以用于各种用例，但有时它不是最明智的解决方案。有时，一个简单的实用函数或一个智能父组件就足够了。因此，只要根据自己的最佳判断使用高阶组件，不要在不需要的地方过度设计😊