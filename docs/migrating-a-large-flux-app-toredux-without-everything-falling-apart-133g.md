# 将一个大流量的应用程序迁移到 Redux，而不会导致一切崩溃

> 原文：<https://dev.to/edrpls/migrating-a-large-flux-app-toredux-without-everything-falling-apart-133g>

[![react to redux image](img/17a154545e599b7cac185147d1b90657.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BoEvJZC---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lczfyy0mt4w983nhgu1h.jpg)

这篇文章最初发表在 GumGum 的科技博客上。

* * *

我们是 gum gum React 的忠实粉丝。事实上，我们的大多数应用都是用它构建的，几乎所有的应用都使用 Redux。

然而，它并不总是这样。我们的第一个应用程序是用 Flux 构建的，虽然它运行得非常好，但还是有一定程度的上下文切换疲劳，特别是对于开发较新的 Redux 应用程序的工程师来说。此外，不熟悉 Flux 会导致在组件挂载时读取存储，但之后不会再次更新时出现一些错误。因为 Redux 将状态作为道具传递，所以我们可以确保从存储中读取的信息总是最新的。最后但同样重要的是，Redux 的实现只是第一次比较麻烦(我们将在下一节中看到)，而 Flux 需要向组件添加一个 store 侦听器，并确保在卸载组件时删除该侦听器。

这个应用程序在内部被广泛使用，也为我们的一些客户所使用，所以试图一次性迁移它将是一个相当大的挑战。一次完成还需要大量的编码时间，这将阻止我们开发新的功能(以及对任何人进行审查的可怕的拉式请求)。因此，我们决定慢慢地迁移应用程序，只要有空闲时间，从通常的新功能和支付技术债务。

如果您和我一样，在阅读 Redux 文档后，仍然不知道如何从 Flux 迁移到 Redux，那么您已经找到了学习如何迁移的正确途径。

这种方法将帮助您将 React 应用程序的一部分迁移到 Redux reducers 和 actions，而其他部分仍然使用您的旧 Flux 存储。

* * *

## 先决条件

有一些库可以让 Redux 和 React 一起使用变得更加容易，所以让我们继续安装它们。根据项目的结构，这些可能会有所不同，有些甚至可能不需要。

在我们的示例应用程序中，我们使用 react-router，因此我们需要连接路由器属性，以便将它们与存储一起传递。这可以通过使用 [react-router-redux 中间件](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-redux)来完成(我们使用 react-router v3，因此如果您的项目使用 v4，请改用 [connected-react-router](https://github.com/supasate/connected-react-router) )。

为了方便地将 React 连接到 Redux，我们将使用 [react-redux 中间件](https://github.com/reduxjs/react-redux)，当然，我们还需要 [Redux](https://github.com/reduxjs/redux) 。

最后，我们的 Flux 存储对服务器执行许多请求，但是由于 Redux 动作在默认情况下不是异步的，我们将使用 [redux-thunk 中间件](https://github.com/reduxjs/redux-thunk)来允许这种行为。如果需要，您可以使用更好的东西，但是这个简单的中间件对于我们的目的来说已经足够了。

如果您想在一行中安装所有这些，请尝试:

`npm -i redux react-redux react-router-redux redux-thunk`

本教程假设您的项目有一个工作通量存储。

### 商店之间的桥梁

现在我们已经安装了所需的依赖项，我们需要一种方法让我们的应用程序处理 Redux 和 Flux 的动作调用。为了做到这一点，我们将复制 Redux `createStore`的简化版本，并对其进行更改，使其能够分别处理包含 Redux 和 Flux 的`type`或`actionType`属性的对象。

你可以继续复制[这个 createFluxStore 文件](https://gist.github.com/edrpls/0d0aba51ef704063527da9539ed65720)以节省时间，但是要注意它使用了 lodash 的`isPlainObject`，所以如果你在你的项目中不使用它，只需删除第 4 行和第 158 到 162 行，一切应该仍然工作正常。

## 示例 App 结构

我们将使用的示例应用程序具有以下结构:

```
 Home
    ├── Products
    ├── Shipments
    └── Clients 
```

Enter fullscreen mode Exit fullscreen mode

在这个场景中，我们将从迁移 Clients 部分开始，并假设每个部分都有相应的 Flux 存储和动作。

## 创建第一个减速器

我们的 clients 部分非常简单，它显示了一个可以反向排序的客户列表。

商店使用的语法有点旧，但应该足够容易理解:

*注意:*为简洁起见，省略了错误处理。

```
// ClientStore.js

// Creates an instance of a flux store, will be replaced later
import Store from './Store';

// Some helpers to handle async calls
import * as http from './helpers/http';

// Instance of a flux store, more on that later
import Store from './Store';

// Flux dispatcher
import Dispatcher from 'flux/lib/Dispatcher';

// Instance of flux dispatcher
const AppDispatcher = new Dispatcher();

// store's state
let _state = {
    clients: []
};

// the store
class ClientStore extends Store {
    getState() {
        return _state;
    }
}

// Create a new instance of the store
const clientStoreInstance = new ClientStore();

// Async function that makes a server request to get all clients, returns a Promise
const getClients = () =>
    http.get('/clients').then(clients => {
        // Update the state with the successful server response
        _state.clients = clients;
    });

// Toggles the direction of the results
const toggleSorting = () => {
    _state.clients = _state.clients.reverse();
};

// listen for actions and define how handle them
clientStoreInstance.dispatchToken = AppDispatcher.register(async action => {
    switch (action.actionType) {
        case 'GET_CLIENTS':
            await getClients();
            break;
        case 'TOGGLE_SORTING':
            await toggleSorting();
            break;
    }

    // Notify of the store change
    clientStoreInstance.emitChange();
});

// Export the new instance of the store
export default clientStoreInstance; 
```

Enter fullscreen mode Exit fullscreen mode

`getClients`函数是异步的，所以这不会很好地转换成 Redux，因为[缩减器应该是一个纯函数](https://redux.js.org/introduction/three-principles#changes-are-made-with-pure-functions)。(这意味着在别处没有副作用——即。异步请求)。它应该只是一个输入和一个输出，但是后面会详细介绍。

另一方面，排序函数没有任何副作用，因此非常适合 reducer:

```
// clientsReducer.js

// Set the initial state to be used
const initialState = {
    clients: []
};

// define and export reducer
export default function clientsReducer(state = initialState, action) {
    // handle action's results
    switch (action.type) {
        // Set the result of the async request to state
        case 'GET_CLIENTS': {
            return {
                clients: action.clients
            };
        }

        // Toggles the direction of the results
        case 'TOGGLE_SORTING': {
            return {
                clients: state.clients.reverse()
            };
        }

        // return the default state if no action was found
        default:
            return state;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了，我们的第一个减速器！现在的问题是，我们没有处理服务器请求(还没有)，reducer 没有连接到 app(还没有)。

接下来，我们将全新的减速器连接到通量存储。

## Redux 减速器用一层焊剂储存

此时，Flux store 和 Redux Redux reducer 彼此独立运行，因此这是使用`createFluxStore`功能连接两者的时候了。这样，针对任一存储的操作将由相应的存储处理，并且两者将共享相同的数据源。这种实现的一个缺点是，尽管 Flux 使用 Redux 作为其状态的来源，但两者都有对象的副本。

我们需要对 ClientStore 进行一些更改，以便从 Redux 中读取状态。

第一个变化是将 ClientStore 创建为 EventEmitter 的实例，而不是 Store 的实例。这个步骤会因项目而异，甚至可能没有必要。

```
// ClientStore.js
// Modified to instance directly from EventEmitter instead of Store for better control of its methods

// Removed flux Store class: "import Store from './Store';"

// will notify components when the store is updated
import EventEmitter from 'events';

// helper that creates a flux store connected to a redux reducer
import createFluxStore from './createFluxStore';

// the new reducer
import clientReducer from './clientsReducer';

// Flux dispatcher
import Dispatcher from 'flux/lib/Dispatcher';

// Constant used by the dispatcher to notify when data changed
const CHANGE_EVENT = 'change';

// Instance of flux dispatcher
const AppDispatcher = new Dispatcher();

// Redux store compatible with flux
const clientsReduxStore = createFluxStore(clientsReducer);

// Initial state will come from redux
let _state = clientsReduxStore.getState();

// modified store, instance of EventEmitter
const ClientStore = Object.assign({}, EventEmitter.prototype, {

    getState() {
        return _state;
    },
    emitChange() {
        this.emit(CHANGE_EVENT);
    },
    addChangeListener(callback) {
        this.on(CHANGE_EVENT, callback);
    },
    removeChangeListener(callback) {
        this.removeListener(CHANGE_EVENT, callback);
    },
    dispatcherIndex: AppDispatcher.register(function(payload) {
        const action = {
            ...payload,
            type: payload.actionType
        };
        adminReduxStore.dispatch(action);
    })
}

// remove instance of the store: const clientStoreInstance = new ClientStore();

// Async function that makes a server request to get all clients
// returns a Promise
const getClients = () =>
    http.get('/clients').then(clients => {
        // Update the state with the successful server response
        _state.clients = clients;
    });

// Toggles the direction of the results
const toggleSorting = () => {
    _state.clients = _state.clients.reverse();
};

// listen for flux actions with the redux-flux store
clientsReduxStore.subscribe(async function(action) {
    switch (action.actionType) {
        case 'GET_CLIENTS':
            await getClients();
            break;
        case 'TOGGLE_SORTING':
            await toggleSorting();
            break;
    }

    // Notify of the redux-flux store change
    ClientStore.emitChange();
});

// Export the the redux-flux store
export default AdminStore; 
```

Enter fullscreen mode Exit fullscreen mode

有了这个存储，我们可以从 Redux reducer 获得状态，开始将每个函数从 flux 转移到 Redux，并且让两个存储都工作，而不必停止其中一个。

对于我们的简单应用程序来说，这似乎有点过头了，在这里，当我们切换到 Redux 时，我们可以冒着两个操作都被破坏的风险，但是在一个有十个或更多方法和存储的应用程序上，您可能希望所有的 Flux 方法在迁移其他方法时都工作。

您可以尝试这个设置，以便在 Redux 更新时让商店更新。我认为没有必要，因为我通常只处理存储或方法的一部分，然后将它移植到使用它的所有组件上。

## 迁移的第一个动作

我们将迁移的第一个操作是颠倒结果的顺序。这个很简单，因为没有副作用，一切都同步发生。

在迁移到 Redux:
之前，我们的 ClientActions 文件如下所示

```
// ClientActions.js

// Flux dispatcher
import Dispatcher from 'flux/lib/Dispatcher';

// Instance of flux dispatcher
const AppDispatcher = new Dispatcher();

// Flux actions
const ClientActions = {
    getClients() {
        AppDispatcher.dispatch({
            actionType: 'GET_CLIENTS'
        });
    },
    toggleSorting() {
        AppDispatcher.dispatch({
            actionType: 'TOGGLE_SORTING'
        });
    }
};

// Export the actions
export default AdminActions; 
```

Enter fullscreen mode Exit fullscreen mode

让我们在文件的底部添加 Redux 的等效动作创建器，

```
export function toggleSorting() {
    return {
        type: 'TOGGLE_SORTING'
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

如果应用程序的另一部分需要使用 Flux 操作，可以像下面这样导入:

```
// Flux actions
import ClientActions from 'ClientActions';
ClientActions.toggleSorting(); 
```

Enter fullscreen mode Exit fullscreen mode

Redux 动作可以在不干扰 Flux 的情况下导入:

```
// Redux actions
import * as clientActions from 'ClientActions';
clientActions.toggleSorting(); 
```

Enter fullscreen mode Exit fullscreen mode

在所有组件开始使用新的减速器后，旧的 Flux 动作可以被移除或被注释。

## 迁移异步操作

要使用 Redux 执行异步操作，我们需要使用 redux-thunk 中间件。我们将在下一节看到如何将 Redux 连接到我们的应用程序，但是首先，让我们通过将这个动作创建者添加到 ClientActions.js:
来添加获取客户端列表的服务器请求

```
// First import our http helper to the top of the file, you can use whatever you want, maybe just a simple fetch call
import * as http from './helpers/http';

// ...

// action that will pass the clients from the server request to the reducer
// will be 'dispatched' after the async request is successful
function saveClientsToStore(clients) {
    return {
        type: 'GET_CLIENTS',
        clients
    };
}

// Async action that will make a server request to get the list of clients
export function getClients() {
    // redux-thunk not only helps redux perform async actions, but it also makes the
    // redux dispatch available for any action this in turn let's us return another
    // action instead of an action creator object
    return dispatch =>
        http
            .get('/clients')
            // Call the dispatcher to pass the received data to the reducer
            .then(clients => dispatch(saveClientsToStore(saveClientsToStore)));
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的流量存储和动作在 Redux 中有了对应的版本！

不幸的是，我们的组件仍然不知道 Redux 或 reducer 的任何事情，所以在下一节中，我们将把它连接到应用程序。

## 连接店铺

首先，我们把 Redux 连接到 app 的入口:

```
// index.js

// hot reloading for development env
import { AppContainer } from 'react-hot-loader';

// react dependencies
import React from 'react';
import { render } from 'react-dom';

// redux tools
import {
    createStore, // turn the reducers into stores
    combineReducers, // combineReducers to merge all different reducer's states into one object
    applyMiddleware, // incorporate redux helpers into the store pipeline
    compose // helps combine different functions into one
} from 'redux';

// helps redux handle async actions
import thunkMiddleware from 'redux-thunk';

// Component that makes the reducers and actions accessible to our application
import { Provider } from 'react-redux';

// react-router's browser history, this is different in v4
import { browserHistory } from 'react-router';

// react-router and redux helpers
import {
    syncHistoryWithStore, // keeps the browser history and synchronized
    routerReducer // provides the router as a redux reducer
} from 'react-router-redux';

// Reducers
import clientsReducer from 'reducers/clientsReducer';

// App wrapper, we will connecte it to redux next
import App from './App';

// Make the redux-dev-tools browser extension work with the app if available
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

// The app store with all middlewares and reducers available
const store = createStore(
    combineReducers({
        clientsReducer,
        routing: routerReducer
    }),
    composeEnhancers(applyMiddleware(thunkMiddleware))
);

// Browser's history synchronized with Redux
const history = syncHistoryWithStore(browserHistory, store);

// App rendering using the Provider component to enable redux
// We pass the store to the Provider and the history to the App wrapper
render(
    <Provider store={store}>
        <App history={history} />
    </Provider>,
    document.getElementById('content')
); 
```

Enter fullscreen mode Exit fullscreen mode

### 连接组件

现在应用程序已经知道了 Redux，我们需要应用程序来处理新的商店和动作:

```
// App.jsx

import React from 'react';
// We use react-router v3, migrating to v4 will be done in the future
import { Router, Route, Redirect, IndexRoute, browserHistory } from 'react-router';

// all our new redux actions
import * as clientActions from 'actions/clientActions';

// redux helper that connects the actions to the dispatcher
import { bindActionCreators } from 'redux';

// redux helper that connects redux actions to the dispatcher
import { connect } from 'react-redux';

// all the app components
import Clients from '/Clients';
import Shipments from '/Shipments';
import Products from '/Products';

// other flux actions that have not been migrated
import AuthActions from 'actions/AuthActions';

// the app container
const App = ({ actions, clients }) => (
    <Router history={browserHistory}>
        {/* Other flux stores still used */}
        <Route path="/" component={Home} onEnter={AuthActions.isAuthenticated}>
            {/* Untouched routes using Flux */}
            <Route path="products" component={Products} />
            <Route path="shipments" component={Shipments} /> 
            {/* Modified route using Redux state and actions */}
            <Route
                path="clients"
                component={() => (
                    <Clients
                        clients={clients}
                        getClients={actions.getClients}
                        toggleSorting={actions.toggleSorting}
                    />
                )}
            />
        </Route>
    </Router> );

// pass the redux store(s) to the component as props
const mapStateToProps = state => ({
    clients: state.clients
    // These can be done in a future pull request with our new setup:
    // TBD: products: state.products
    // TBD: shipments: state.shipments
});

// pass redux actions to the component as props
const mapDispatchToProps = dispatch => ({
    actions: bindActionCreators(clientActions, dispatch)
});

// pass both redux state and actions to your component
export default connect(mapStateToProps, mapDispatchToProps)(App);

// export just the unplugged component, this is helpful for testing
export { App }; 
```

Enter fullscreen mode Exit fullscreen mode

通过这样设置我们的应用程序，我们可以传递每条路线将需要的特定状态和动作。在某些情况下，您甚至会发现您的组件会变成无状态，因为它们总是从存储中接收新的状态。

另一件要注意的事情是，我们导出我们的组件两次，默认的导出需要一个 Redux 存储及其动作，而另一个导出没有连接，这有助于我们测试组件，因为它让我们传递我们需要的状态和属性，而不是让
模拟整个 Redux 存储。测试是最好留给另一篇文章讨论的话题。

请注意，根据您的应用程序使用的 react-router 版本，您的连接方式可能会有所不同。

## 看马！没有通量！

现在我们几乎完成了客户端部分的迁移，最后一步是在组件中使用 Redux 操作，而不是旧的 Flux 操作。

目前，我们的组件将客户端存储在状态中，并监听 Flux 存储的变化，但现在它使用 props 的 reducer 函数来切换排序。

```
// Clients.jsx

import React from 'react';

// import flux actions
import ClientActions from 'ClientActions';

// import flux store
import ClientStore from 'ClientStore';

class Clients extends React.Component {
    // Set the initial state
    constructor(props) {
        super(props);
        const { clients } = ClientStore.getState();
        this.state = { clients };
    }

    // Set flux listener
    componentDidMount() {
        ClientStore.addChangeListener(this._onChange);
        // Request clients from server
        ClientActions.getClients();
    }

    // remove flux listener on unmount
    componentWillUnmount() {
        ClientStore.removeChangeListener(this._onChange);
    }

    // update the state when flux emits a change event
    _onChange = () => {
        const { clients } = ClientStore.getState();
        this.setState({ clients });
    };

    _reverseOrder = () => {
        // previously, using Flux:
        // ClientActions.toggleSorting();
        // now with Redux:
        this.props.toggleSorting();
    };

    render() {
        return (
            <div>
                <button type="button" onClick={this._reverseOrder}>
                    Reverse order
                </button>
                <ul>{this.state.clients.map(client => <li key={client.id}>{client.name}</li>)}</ul>
            </div>
        );
    }
}

export default Clients; 
```

Enter fullscreen mode Exit fullscreen mode

现在这个组件可以处理 Redux 和 Flux 动作，让我们添加下一个动作，并删除所有与 Flux 相关的内容，使用我们之前传递给父组件的属性:

```
// Clients.jsx

import React from 'react';

class Clients extends React.Component {
    // Request the server data
    componentDidMount() {
        this.props.getClients();
    }

    _reverseOrder = () => this.props.toggleSorting();

    render() {
        return (
            <div>
                <button type="button" onClick={this._reverseOrder}>
                    Reverse order
                </button>
                <ul>
                    {/* We now use the clients array that comes from the props */}
                    {this.props.clients.map(client => <li key={client.id}>{client.name}</li>)}
                </ul>
            </div>
        );
    }
}

export default Clients; 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们的组件现在更简单了，它从道具中获取所有东西，并且它只获取所需的特定数据，而不必调用整个商店。

就这样，我们的第一部分已经被迁移了。我们现在可以清理它，并删除所有对旧 Flux 方法的引用(如果没有其他组件还在使用它们的话)，然后提交这个 pull 请求，并为下一个 sprint 处理下一部分！

## 结论

*   迁移一个大型的 react 存储不是一件容易的事情，但是它可以在不破坏应用程序整体功能的情况下，通过一些渐进的步骤来完成。

*   各种第三方库可以帮助我们集成 Redux 和 React，通过使用 Redux 的`createStore`的修改副本，我们可以创建一个处理 Redux 和 Flux 操作的 Flux 存储。

* * *

感谢 GitHub 用户 [vivek3003](https://github.com/vivek3003/flux-redux-migration) 的`createFluxStore`功能和初始方法。