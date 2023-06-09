# 向 React 应用程序添加 Redux(带 ducks)材质 UI 加载器

> 原文：<https://dev.to/jsmegatools/adding-redux-with-ducks-material-ui-loader-to-a-react-app-630>

这篇文章是 JS Mega Tools 的 React 在线课程的第三课。你可以从下面的地址获得上一课的代码:[https://github.com/jsmegatools/React-online-course](https://github.com/jsmegatools/React-online-course)一旦你克隆了存储库，你就可以进入 Lesson-2 文件夹，按照本教程中的方式编辑文件。

在这一课中，我们将添加 redux 到我们的应用程序中，并设置材质-ui 加载器。

首先让我们安装必要的 redux 模块。在应用程序的根文件夹中运行以下代码:

```
npm install redux react-redux --save 
```

第一个模块是官方的 redux 模块，第二个是用于与 redux 一起使用的 react。

我们在根文件夹而不是 react-ui 文件夹(前端 react 代码所在的文件夹)中运行该命令的原因是，它使我们能够在服务器渲染中使用 redux

Redux 有 3 个重要的概念:存储、动作和 reducers。

存储区是存储应用程序状态的地方。应用程序的状态是单个对象。应用程序的状态就像应用程序在某一时刻的快照。理想情况下，您不会将 React 组件状态与 redux 一起使用，redux 状态将是整个应用程序的唯一真实来源。这有助于保持对应用程序中数据流的控制，并避免导致各种错误的意大利面条式代码。但是在一些用例中，你可能想使用 react store 来代替 redux。

Redux 中的动作是普通对象，表示应用程序的不同部分想要执行来修改状态的动作。他们发送各种数据到一个商店，并有一个类型。这种将数据发送到存储的过程称为调度，也就是调度操作。这样做的方法是调用一个名为 dispatch 的存储方法。对状态应用更改的唯一方式必须是操作，而不是直接修改。

最后，reducers 是纯函数(也就是说，给定相同的参数，它们返回相同的结果),用动作中发送的数据更新存储。在 reducers 内部，如果有由操作带来的状态修改，旧状态将被新状态替换，修改将应用于新状态。

我们将在名为 configureStore.js 的文件中创建一个 redux store，我们将在根目录中创建该文件。以下是 configureStore.js 的内容:

```
import { createStore, applyMiddleware } from 'redux';
import thunkMiddleware from 'redux-thunk';
import rootReducer from './reducers';

export default function configureStore() {
  return createStore(
    rootReducer,
    applyMiddleware(
      thunkMiddleware
    )
  )
} 
```

我们从模块中导出 configureStore，该模块配置并返回一个存储。主要工作由创建商店的 createStore 函数完成。还有 applyMiddleware 功能，它将中间件添加到商店中。我们已经在前面的课程中讨论过 Express 中间件，redux 中间件是一个类似的概念。Redux 中间件可以访问一个存储、一个调度的动作，并且可以自己调度动作。

我们使用的是 redux-thunk 的 thunkMiddleware，它允许 dispatch 接受函数作为参数，而没有 thunkMiddleware，dispatch 只接受对象。这允许我们拥有异步动作，这允许将 http 请求放入动作中，所以我们的组件所要做的就是分派动作，而不需要知道各种异步 API，比如 fetch。

要将 redux-thunk 添加到我们的项目中，请在应用程序的根文件夹中运行以下命令:

```
npm install redux-thunk --save 
```

我们在应用程序的根文件夹中运行它，以将其用于服务器渲染(像 redux 和 react-redux 模块)。

我们还传递 rootReducer 函数，我们一会儿会讲到。

一旦我们创建了 configureStore 模块，我们就可以将商店添加到我们的应用程序中了。我们将在 react-redux 模块(react 的官方 redux 绑定)的提供者组件的帮助下，将商店添加到我们的应用程序中。

用以下内容替换 react-ui/index.js 文件的内容:

```
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import './index.css';
import App from './App';
import configureStore from './configureStore';
import registerServiceWorker from './registerServiceWorker';

const rootElement = <Provider store={configureStore()}>
  <App />
</Provider>;

ReactDOM.render(rootElement, document.getElementById('root'));
registerServiceWorker(); 
```

我们将提供者组件导入顶部的模块。我们还导入 configureStore。然后我们用它创建一个根 Provider 元素，我们将 configureStore 调用创建的 store 作为 store prop 传递给 Provider 元素。然后，我们将使用 App 组件创建的 App 元素包装在 Provider 元素中。Provider 现在位于应用程序元素层次结构的顶部。商店现在对我们应用程序的组件层次结构中的每个组件实例都可用。你不必通过道具从父母传给孩子。

### 设置减速器、动作、动作类型。

之前使用 createStore 时，我们给它传递了 rootReducer 函数。Redux 允许我们分离 reducers 的职责，这样它们只负责一个状态的特定部分。例如，在我们的应用程序中，我们有一个主区域和一个管理区域，为这些部分使用不同的状态分支是非常合理的。

负责一个州的一部分的还原者可以进一步将该州的该部分的责任分配给其他还原者。这种分布借助于 combineReducers 函数发生，该函数返回一个归约器，该归约器将该归约器所负责的状态的各个部分的责任赋予传递给 combineReducers 函数的归约器。前一句话里有大量的还原词:)。下面是我们的 rootReducer 将如何被创建。

1.  在 react-ui/src 目录下创建一个 redux 文件夹
2.  在该目录中，创建包含以下内容的 index.js 文件:

```
import { combineReducers } from 'redux'
import mainArea from './modules/mainArea'
import adminArea from './modules/adminArea'

export default combineReducers({
  mainArea,
  adminArea
}); 
```

我们从 redux 模块导入 combineReducers。我们从模块目录中导入 reducers mainArea 和 adminArea reducers(稍后会详细介绍)。然后，我们使用组合归约器来创建根归约器，它将状态的主区域属性的责任委托给主区域归约器，将状态的管理区域属性的责任委托给管理区域归约器。这个根缩减器然后被传递给 createStore，正如我们前面看到的。mainArea 或 adminArea reducers 可以是类似的 combineReducers 调用的结果，也可以由开发人员定义为函数。如果它们是 combineReducers 调用的结果，那么它们会将它们负责的状态部分(例如 mainArea)的责任分配给其他 reducers。

我们将使用 ducks 建立我们的应用程序结构。那究竟是什么？这里有一个故事。redux 出来的时候，大家都在沿用 redux 官方教程里用的一个应用结构。它将组件、容器、缩减器、动作、常量等文件夹放在应用程序的根文件夹中。这种方法不可伸缩，因为当您向应用程序添加更多功能时，每个目录中会有许多文件。

然后，出现了另一种构造 react 和 redux 应用程序的方法，通过将组件、容器、缩减器、动作、常量按照它们所代表的特性分组，并将它们放入以该特性命名的文件夹中。这种方法具有更好的可伸缩性，但是 React 和 Redux 之间没有分离。一旦您决定将您的状态管理解决方案切换到其他库，就需要您做大量的移动和编辑工作。

最后，https://github.com/erikras/ducks-modular-redux 提出了一个解决方案，它鼓励将 React 部分和 Redux 部分分开，将 React 代码按特性分组放在文件夹中，将 Redux 代码按特性分组放在文件的模块中。

现在，我们将有 mainArea 和 adminArea 模块。我们将把这些模块放在名为 modules 的文件夹中。这些模块的默认导出是 reducer(这就是为什么我们将这些模块的导入传递给 combine reducers 函数)，但是这些模块也包含动作和动作类型。

让我们在 react-ui/src/redux 中创建一个 modules 文件夹，并在 modules 文件夹中创建 mainArea.js 文件，其内容如下:

```
import fetch from 'cross-fetch';

const GET_LOCATIONS = 'rta/mainArea/GET_LOCATIONS';
const GET_LOCATIONS_SUCCESS = 'rta/mainArea/GET_LOCATIONS_SUCCESS';
const GET_LOCATIONS_FAILURE = 'rta/mainArea/GET_LOCATIONS_FAILURE';

export const requestLocations = () => ({ type: GET_LOCATIONS });
export const receiveLocations = locations => ({ type: GET_LOCATIONS_SUCCESS, locations });
export const receiveLocationsFail = error => ({ type: GET_LOCATIONS_FAILURE, error });

export const fetchLocations = () => (dispatch) => {
  dispatch(requestLocations());
  return fetch('/api/locations').then(
    res => res.json(),
    err => dispatch(receiveLocationsFail(error))
  )
    .then(locations => dispatch(receiveLocations(locations)))
};

const initialState = {
  locations: [],
  isFetching: false,
  error: false
};

export default (state = initialState, action) => {
  switch(action.type) {
    case GET_LOCATIONS:
      return {
        ...state,
        isFetching: true
      };
    case GET_LOCATIONS_SUCCESS:
      return {
        ...state,
        locations: action.locations,
        isFetching: false
      };
    case GET_LOCATIONS_FAILURE:
      return {
        ...state,
        error: action.error,
        isFetching: false
      };
    default:
      return state;
  }
}; 
```

首先，我们从 cross-fetch(一个实现 fetch API 的库，允许进行异步 http 请求)导入 fetch。之后，我们有 3 个动作类型定义。将动作类型定义为常量是一个很好的做法，因为随着应用程序的扩展，向定义中添加修改比替换模块中的每个动作类型更容易。

动作类型的形式为“NPM-模块-or-app/reducer/ACTION_TYPE”。rta 代表 react 旅行住宿。mainArea 是缩减器的名字，虽然我们把它作为一个匿名函数，当我们把它导入到另一个文件中时，我们称它为 mainArea，最后还有一个动作类型。GET_LOCATIONS 对应于住宿位置的服务器请求，GET_LOCATIONS_SUCCESS 对应于成功的 http 请求，GET_LOCATIONS_FAILURE 对应于失败的 http 请求。

接下来我们有动作创建者功能，他们创建动作。它们在 redux 中很常见，通常也被称为动作。动作创建者的目的是可移植性和易于测试。第三个动作创建者返回一个函数，而不是一个对象，这是由我们前面谈到的 thunk 中间件实现的。当调用 fetchLocation 操作创建器时，GET_LOCATIONS 操作通过 requestLocations 从其内部调度，当请求成功完成时，GET_LOCATIONS_SUCCESS 操作通过 receiveLocations 创建器调度(该操作将 LOCATIONS 作为有效负载)。

在上一课中，我们在 MainArea 组件的 componentDidMount 内部调用了一个 fetch，现在该调用被移到 fetchLocations 操作中，并由 redux 处理。

接下来，我们为应用程序状态的 mainArea 部分设置了一个初始状态。还原器初始化需要初始状态，因为还原器在第一次被 redux 调用时会作为第一个参数传递未定义的状态。初始状态也是获得特定缩减器状态的可视化表示的好方法。

模块的默认导出是一个减速器。它接受一个现有状态和一个操作，并基于该操作返回一个新状态，如果 switch 语句中没有匹配的大小写，则返回一个默认状态。

如果一个动作的类型是 GET_LOCATIONS，我们使用 ES6 对象扩展操作符将以前的状态属性复制到一个新的状态。然后，我们将 isFetching 属性设置为 true，这允许我们显示加载程序。对于 GET_LOCATIONS_SUCCESS，我们做了同样的事情，但是我们将状态的 LOCATIONS 属性设置为我们在动作中接收的值，并将 isFetching 属性设置为 false 以隐藏加载器。使用 GET_LOCATIONS_ERROR，我们复制以前的状态，将 isFetching 设置为 false，并将一个错误设置为请求期间发生的错误。最后，如果没有类型匹配动作的类型，我们返回作为参数传递给 reducer 的状态(例如，当到达这个 reducer 的动作意味着另一个 reducer 时，就会发生这种情况)。

我们现在不在管理区工作，因此现在你可以在 react-ui/src/reducers/modules/admin area . js 中放一个占位符 reducer:

```
export default (state = {}, action) => {
  return state;
}; 
```

现在我们使用了 ducks，让我们创建我们想要的 react 项目结构。现在，我们将组件放在 react-ui/src 的 components 文件夹中。让我们创建特性目录，并将主区域和管理文件夹添加到其中。然后我们应该将 MainArea.js 从 components/MainArea 移动到 features/MainArea，将 AdminArea.js 从 comopents/AdminArea 移动到 features/AdminArea。之后，我们可以删除组件文件夹。

当您使用 redux 时，将您的组件视为表示组件和容器组件是有益的。表示组件处理 ui，容器组件在商店和表示组件之间传递数据。让我们为主要区域和管理区域创建容器组件。我们将把容器组件放到它们各自的特性文件夹中:features/MainArea 和 features/AdminArea。

以下是 features/MainArea/MainArea container . js 的内容:

```
import { connect } from 'react-redux';
import MainArea from './MainArea';
import * as actions from '../../redux/modules/mainArea';

const mapStateToProps = ({ mainArea }) => ({
  locations: mainArea.locations,
  isFetching: mainArea.isFetching,
  error: mainArea.error
});

export default connect(mapStateToProps, actions)(MainArea); 
```

我们从 react-redux 导入连接函数，它将 redux 存储连接到 MainArea 组件。然后我们导入 MainArea 组件，并从 mainArea redux 模块导入 actions 作为对象。mapStateToProps 接收整个状态作为参数，并创建一个对象合并到表示组件的 Props 中。您可以选择对象属性的名称，从您想要的状态中选择任何值，并将这些值分配给属性。属性是属性的名称，值是组件属性的值。

这里我们使用函数参数的对象析构来提取状态的 mainArea 属性，并返回一个带有 locations、isFetching 和 error 属性的对象来合并到 MainArea 属性中。然后我们调用 connect with mapStateToProps。

connect 函数有第二个参数，称为 mapDispatchToProps，如果它是一个函数，也返回一个对象合并到一个组件 Props 中，但是它有 dispatch 作为参数。该功能可以通过以下方式使用调度:

```
const mapDispatchToProps = dispatch => {
  return {
    prop: data => {
      dispatch(someAction(data));
    }
    …
  };
} 
```

然后，您的组件可以调用 props 作为函数，这些函数将调用 dispatch。

如果您将一个对象作为 mapDispatchToProps 传递(就像我们传递 actions 一样，它是从 mainArea 模块导入的)，那么合并到组件的 Props 中的对象将是一个具有相同属性名称和值的对象，它将被包装到 dispatch 中。

对于 features/AdminArea/AdminArea container . js，您现在可以使用占位符代码:

```
import { connect } from 'react-redux';
import AdminArea from './AdminArea';

const mapStateToProps = state => ({});

export default connect(mapStateToProps)(AdminArea); 
```

现在我们已经创建了 MainAreaContainer，是时候让 MainArea 组件使用 redux 了。将 react-ui/src/features/main area/main area . js 更改为以下内容:

```
import React, { Component } from 'react';
import RefreshIndicator from 'material-ui/RefreshIndicator';

class MainArea extends Component {
  componentDidMount() {
    this.props.fetchLocations();
  }

  render() {
    const content = this.props.isFetching ? <RefreshIndicator
      size={50}
      top={0}
      left={0}
      loadingColor="#FF9800"
      status="loading"
      style={{
        position: 'absolute',
        top: '50%',
        left: '50%',
        transform: 'translate(-50%,-50%)'
      }}
    /> :
      this.props.locations.map(location =>
        <li key={location.id}>
          <img src={location.image} alt={location.name} />
          {location.name}
        </li>) 
    return (
      <div className="home-page-container">
        {content}
      </div>
    );
  }
}

export default MainArea; 
```

我们暂时去掉了构造函数。现在，我们在这个组件中不使用 fetch，而是调用 this.props.fetchLocations。在 render 中，我们检查来自应用程序状态的 isFetching 值，如果是，我们显示来自 [material-ui](http://www.material-ui.com/) 的 RefreshIndicatior loader(我们将在一分钟后设置 material-ui)，否则，我们呈现一个位置列表，将三元运算符的结果存储在 content constant 中，然后放入 JSX。

现在我们的 MainArea 组件使用 Redux。让我们为加载器安装和设置 material-ui。

在主项目的根目录(而不是 react-ui 目录)中运行以下命令:

```
npm install material-ui --save. 
```

将以下导入添加到 react-ui/index.js:

从“material-ui/styles/MuiThemeProvider”导入 MuiThemeProvider；

然后，在 index.js 中，将涉及 const root 的表达式替换为以下内容:

```
const root = <Provider store={configureStore()}>
  <MuiThemeProvider>
    <App />
  </MuiThemeProvider>
</Provider>; 
```

现在我们的应用程序中有了 material-ui，加载器可以工作了。

这就是第三课的内容。我们已经学习了如何在 React 项目中设置和使用 Redux，以及如何创建一个材质 ui 加载器。本课的完整源代码可以在以下地址找到。

[https://github.com/jsmegatools/React-online-course](https://github.com/jsmegatools/React-online-course)