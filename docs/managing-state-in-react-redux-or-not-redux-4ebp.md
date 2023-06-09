# React 中的状态管理:Redux 还是 not Redux？

> 原文：<https://dev.to/alexisjanvier/managing-state-in-react-redux-or-not-redux-4ebp>

***注:本帖原帖于 marmelab.com 的[。](https://marmelab.com/blog/2018/06/27/redux-or-not-redux.html)***

在 Marmelab，我们非常喜欢使用 [Redux](https://redux.js.org/) 来管理 React 应用的状态。它的出现改变了我们编写应用程序的方式:不变性、函数式编程、使用 Redux-Saga 生成器的异步 API 调用管理...以至于我们有时倾向于“事实上”将 Redux 集成到我们的项目启动堆栈中。

但这是个好主意吗？不确定...

## 一个例子:用 React 管理聚会

让我们看一个简单的 meetup 管理应用程序。它应该能够显示:

*   一份提案清单，
*   会谈的愿望清单，
*   meetup 成员列表。

数据来自 REST API。登录/密码保护应用程序和 API。

应用程序通过 [Create React App](https://github.com/facebook/create-react-app) 引导，并通过以下方式升级:

*   [还原](https://redux.js.org/)
*   [Redux-Saga](https://redux-saga.js.org/)
*   [react-router-redux](https://github.com/reactjs/react-router-redux)

这个项目是这样的:

[![Edit simple-application-with-redux](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/m5n2xjl6pj)

该应用程序反映了典型的 redux 架构。它从一个`<App />`组件开始，该组件安装了 redux store ( `<Provider store={store}>`)和路由器(`<ConnectedRouter history={history}>` ):

```
// in App.js
...
 export const App = ({ store, history }) => (
    <Provider store={store}>
        <ConnectedRouter history={history}>
            <Container>
                <Header />
                <Switch>
                    <Route exact path="/" component={Home} />
                    <Route path="/talks" component={Talks} />
                    <Route path="/wishes" component={Wishes} />
                    <Route path="/members" component={Members} />
                    <Route path="/login" component={Authentication} />
                    <Route component={NoMatch} />
                </Switch>
            </Container>
        </ConnectedRouter>
    </Provider> ); 
```

Enter fullscreen mode Exit fullscreen mode

Redux 用户会对我选择的文件结构感到满意。我将与一个特性相关的所有代码分组到一个目录中。一个关于`talks`页面的例子:

```
├── talks
│   ├── actions.js
│   ├── reducer.js
│   ├── sagas.js
│   └── Talks.js 
```

Enter fullscreen mode Exit fullscreen mode

`<Talks>`页面组件是一个简单的“连接组件”:

```
 // in talks/Talks.js
export const Talks = ({ isLoading, talks }) => (
    <div>
        <h1>Talks</h1>
        {isLoading && <Spinner />}
        {talks && talks.map(talk => <h2 key={talk.id}>{talk.title}</h2>)}
    </div> );

const mapStateToProps = ({  talks }) => ({
    isLoading: talks.isLoading,
    talks: talks.data,
});

// passing {} as the second's connect argument prevents it to pass dispatch as prop
const mapDispatchToProps = {};

export default connect(mapStateToProps, mapDispatchToProps)(Talks); 
```

Enter fullscreen mode Exit fullscreen mode

会谈的数据不是在`componentWillMount`获取的，而是通过监听路线变化的传奇:

```
// in talks/sagas.js
import { put, select, takeLatest } from 'redux-saga/effects';
import { LOCATION_CHANGE } from 'react-router-redux';

import { loadTalks } from './actions';

const hasData = ({ talks }) => !!talks.data;

export function* handleTalksLoading() {
    if (yield select(hasData)) {
        return;
    }

    yield put(loadTalks());
}

export const sagas = function*() {
    yield takeLatest(
        action =>
            action.type === LOCATION_CHANGE &&
            action.payload.pathname === '/talks',
        handleTalksLoading,
    );
}; 
```

Enter fullscreen mode Exit fullscreen mode

当路线改变并对应于会谈部分(`action.type === LOCATION_CHANGE && action.payload.pathname === '/talks'`)时，我的应用程序通过`loadTalks`功能触发一个动作:

```
// in talks/actions.js
export const LOAD_TALKS = 'LOAD_TALKS';

export const loadTalks = payload => ({
    type: 'LOAD_TALKS',
    payload,
    meta: {
        request: {
            url: '/talks',
        },
    },
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个动作在它的**元**中包含了获取会谈数据的 url，它将被一个通用获取**传奇**T0:
拦截

```
// in /services/fetch/fetchSagas.js
import { call, put, takeEvery, select } from 'redux-saga/effects';

import { appFetch as fetch } from './fetch';

export const fetchError = (type, error) => ({
    type: `${type}_ERROR`,
    payload: error,
    meta: {
        disconnect: error.code === 401,
    },
});

export const fetchSuccess = (type, response) => ({
    type: `${type}_SUCCESS`,
    payload: response,
});

export function* executeFetchSaga({ type, meta: { request } }) {
    const token = yield select(state => state.authentication.token);
    const { error, response } = yield call(fetch, request, token);
    if (error) {
        yield put(fetchError(type, error));
        return;
    }

    yield put(fetchSuccess(type, response));
}

export const sagas = function*() {
    yield takeEvery(
        action => !!action.meta && action.meta.request,
        executeFetchSaga,
    );
}; 
```

Enter fullscreen mode Exit fullscreen mode

一旦获取成功，saga 将触发指示数据恢复成功的最终操作(`createAction('${type}_SUCCESS')(response)`)。该动作由会谈使用**减速器** :

```
// in talks/reducers.js
export const reducer = (state = defaultState, action) => {
    switch (action.type) {
        case LOAD_TALKS:
            return {
                ...state,
                loading: true,
            };
        case LOAD_TALKS_ERROR:
            return {
                ...state,
                loading: false,
                error: action.payload,
            };
        case LOAD_TALKS_SUCCESS:
            return {
                ...state,
                loading: false,
                data: action.payload,
            };
        case LOGOUT:
            return defaultState;
        default:
            return state;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

效果很好。这很聪明，甚至很优雅！动作的**元**的使用允许在应用程序内共享通用行为(数据获取以及错误处理或注销)。

## 很聪明，但是很复杂

当你发现应用程序时，不容易找到方向，有些行为是如此神奇。总而言之，应用程序使用连接到路由器的 redux-saga 获取数据，路由器发送由另一个通用 saga 拦截的获取动作，如果成功，则发出另一个动作，由页面的 redux-saga 拦截的动作已经发出了链的第一个动作...

有些人可能会说这是对 redux 的滥用，但它主要是在这个堆栈上完成的几个项目的结果，有重写动作和 reducers 的经验。

除此之外，还有大量的*管道*，也就是说，每个特性都有许多重复的文件(动作、减少器和其他传奇)。

让我们分析一下这个包含三个页面的示例应用程序:主页和登录页面:

```
 ❯ cloc services/cra_webapp/src
      32 text files.
      32 unique files.
       0 files ignored.

github.com/AlDanial/cloc v 1.74  T=0.06 s (581.6 files/s, 17722.1 lines/s)
-------------------------------------------------------------------------------
Language                     files          blank        comment           code
-------------------------------------------------------------------------------
JavaScript                      31            150              1            819
CSS                              1              0              0              5
-------------------------------------------------------------------------------
SUM:                            32            150              1            824
------------------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

31 个文件，819 行代码，对于一个简单的应用来说已经很多了。这段代码可以简化一点，但有可能会变得不那么通用。

当然是时候问问我们自己 Redux 在这里是否有必要了？

> Redux 是 JavaScript 应用程序的可预测状态容器。

但是，应用程序的不同部分是否会修改相同的数据，需要该数据的可预测状态？不，我只需要显示来自 API 的数据。DOM 中是否埋有可以修改数据的组件？不，用户交互非常有限。

所以我大概不需要 Redux。

## 取数据无冗余

让我们尝试在没有 Redux 的情况下获取数据，或者更准确地说，在没有 **Redux-Saga** 的情况下获取数据(因为执行数据获取不是 Redux 的直接工作)。我可以在每个页面上实现所有这些获取逻辑。然而，这将建立非常重复的机制和大量重复的代码。所以我必须找到一种通用的方法从 API 中获取数据，而不会引入太多的重复和复杂性。

[**渲染道具**](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce) 图案是这类问题的绝佳候选！

让我们创建一个`DataProvider`组件:

```
// in DataProvider.js
import React, { Component, Fragment } from 'react';
import { Redirect } from 'react-router';
import { appFetch } from './services/fetch';

export class DataProvider extends Component {
    static propTypes = {
        render: PropTypes.func.isRequired,
        url: PropTypes.string.isRequired,
    };

    state = {
        data: undefined,
        error: undefined,
    };

    fetchData = async props => {
        const token = window.sessionStorage.getItem('token');
        try {
            const data = await appFetch({ url }, token);
            this.setState({
                data: data.response,
                error: null,
            });
        } catch (error) {
            this.setState({
                error,
            });
        }
    };

    componentDidMount() {
        return this.fetchData(this.props);
    }

    render() {
        const { data, error } = this.state;
        const { location } = this.props;

        if (error) {
            return error.code >= 401 && error.code <= 403 ? (
                <Redirect to="/login" />
            ) : (
                <p>Erreur lors du chargement des données</p>
            );
        }

        return (
            <Fragment>
                {data ? (
                    <p>Aucune donnée disponible</p>
                ) : (
                    this.props.render({
                        data,
                    })
                )}
            </Fragment>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该组件在`componentDidMount`期间从属性`url`获取数据。它管理错误和丢失的数据。如果它得到数据，它会将渲染委托给作为`render` prop ( `this.props.render({ data })`)传递的函数。

让我们在对话页面上实现这个组件:

```
// in talks/Talks.js
import React from 'react';
import PropTypes from 'prop-types';

import { DataProvider } from '../DataProvider';

export const TalksView = ({ talks }) => (
    <div>
        <h1>Talks</h1>
        {talks && talks.map(talk => <h2 key={talk.id}>{talk.title}</h2>)}
    </div> );

TalksView.propTypes = {
    talks: PropTypes.array,
};

export const Talks = () => (
    <DataProvider
        url="/talks"
        render={({ data }) => <TalksView talks={data} />}
    />
); 
```

Enter fullscreen mode Exit fullscreen mode

我现在有两个组件:

*   `TalksView`组件，只显示数据，不管它来自哪里，
*   `Talks`组件，使用`DataProvider`获取数据，使用`TalksView`显示数据`render={({ data }) => <TalksView talks={data} />}`。

简单有效，可读性强！

有一个优秀的库实现了这种类型的 data provider:[React-request:React 的声明性 HTTP 请求](https://github.com/jamesplease/react-request)

我现在准备从应用程序中删除 Redux。

[![Edit simple-application-without-redux](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/o77qv75rmq)

让我们重新开始分析我们的项目:

```
❯ cloc services/cra_webapp/src
      16 text files.
      16 unique files.
       0 files ignored.

github.com/AlDanial/cloc v 1.74  T=0.04 s (418.9 files/s, 13404.6 lines/s)
-------------------------------------------------------------------------------
Language                     files          blank        comment           code
-------------------------------------------------------------------------------
JavaScript                      15             64              1            442
CSS                              1              0              0              5
-------------------------------------------------------------------------------
SUM:                            16             64              1            447
------------------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

所以我从 819 行代码到 **442 行**，几乎少了一半。还不错！

## 用 React 状态替换 Redux 存储

在当前状态下，每个页面都使用 DataProvider 获取数据。然而，我的应用程序需要通过一个 **json-web-token** 进行身份验证来获取用户信息。

如果没有 Redux 存储，这些用户信息将如何传输到各个组件？嗯，通过使用上级组件(`App.js`)的**状态**，并将`user`作为道具传递给需要它的子组件(`PrivateRoute.js`、`Header.js`)。

**总之让我们再做一次 React 代码吧！**

```
// in App.js
import React, { Component } from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

import { Authentication } from './authentication/Authentication';
import { Header } from './components/Header';
import { PrivateRoute } from './PrivateRoute';
import { Talks } from './talks/Talks';

export class App extends Component {
    state = {
        user: null,
    };

    decodeToken = token => {
        const user = decode(token);
        this.setState({ user });
    };

    componentWillMount() {
        const token = window.sessionStorage.getItem('token');

        if (token) {
            this.decodeToken(token);
        }
    }

    handleNewToken = token => {
        window.sessionStorage.setItem('token', token);
        this.decodeToken(token);
    };

    handleLogout = () => {
        window.sessionStorage.removeItem('token');
        this.setState({ user: null });
    };

    render() {
        const { user } = this.state;
        return (
            <Router>
                <div>
                    <Header user={user} onLogout={this.handleLogout} />
                    <Switch>
                        <PrivateRoute
                            path="/talks"
                            render={() => (
                                <Talks />
                            )}
                            user={user}
                        />
                        <Route
                            path="/login"
                            render={({ location }) => (
                                <Authentication
                                    location={location}
                                    onNewToken={this.handleNewToken}
                                />
                            )}
                        />
                    </Switch>
                </div>
            </Router>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**注**:我知道:把`token`存储在`window.sessionStorage`里是一个 [**烂练**](https://www.rdegges.com/2018/please-stop-using-local-storage/) 。但是为了这个例子，这允许我快速设置认证。这和去除 Redux 没有关系。

```
// in PrivateRoute.js
import React from 'react';
import PropTypes from 'prop-types';
import { Redirect, Route } from 'react-router';

/**
 * This Route will redirect the user to the login page if needed.
 */
export const PrivateRoute = ({ user, ...rest }) =>
    user ? (
        <Route {...rest} />
    ) : (
        <Redirect
            to={{
                pathname: '/login',
                state: { from: rest.location },
            }}
        />
    );

PrivateRoute.propTypes = {
    user: PropTypes.object,
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
// in components/Header.js
import React from 'react';
import PropTypes from 'prop-types';

import { Navigation } from './Navigation';

export const Header = ({ user, onLogout }) => (
    <header>
        <h1>JavaScript Playground: meetups</h1>
        {user && <Navigation onLogout={onLogout} />}
    </header> );

Header.propTypes = {
    user: PropTypes.object,
    onLogout: PropTypes.func.isRequired,
}; 
```

Enter fullscreen mode Exit fullscreen mode

我的申请相对简单，将`user`作为**道具**传递给孩子们并不是一个真正的问题。

假设我想让我的导航栏更漂亮，用一个真正的注销菜单显示用户的名字。我必须将这个`user`传递给`Navigation`组件。

```
<Navigation onLogout={onLogout} user={user}/> 
```

Enter fullscreen mode Exit fullscreen mode

此外，如果`<UserMenu>`组件使用另一个组件来显示用户，我将不得不再次传输我的用户:

```
const UserMenu = ({ onLogout, user }) => {
    <div>
        <DisplayUser user={user} />
        <UserSubMenu onLogout={onLogout} />
    </div> } 
```

Enter fullscreen mode Exit fullscreen mode

在显示之前，`user`已经通过了 4 个组件...

更复杂和/或更繁重的应用程序呢？这会变得非常痛苦。这是询问 Redux 的使用问题变得合理的情况之一！

然而，现在有一个直接的解决方案可以将数据从一个组件传输到 React 树中更深层次的其他组件:React 上下文[**。**](https://reactjs.org/docs/context.html)

### 使用 React 上下文传递状态

`React.createContext`方法生成两个组件:

```
const {Provider, Consumer} = React.createContext(defaultValue); 
```

Enter fullscreen mode Exit fullscreen mode

*   一个`Provider`负责*分发*数据，
*   能够读取提供商数据的。

让我们回到前面的三个组件。

```
// in App.js
import React, { Component } from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import styled from 'styled-components';
import { decode } from 'jsonwebtoken';

...

const UserContext = React.createContext({
    user: null,
    onLogout: () => true,
});

export const UserConsumer = UserContext.Consumer;
const UserProvider = UserContext.Provider;

export class App extends Component {
    ...

    render() {
        const { user } = this.state;
        return (
            <UserProvider
                value={{
                    user,
                    onLogout: this.handleLogout,
                }}
            >
                <Router>
                    <Container>
                        <Header />
                        <Switch>
                            <PrivateRoute
                                exact
                                path="/"
                                render={({ location }) => (
                                    <Home location={location} />
                                )}
                            />
                        ... 
```

Enter fullscreen mode Exit fullscreen mode

```
// in PrivateRoute.js
import React from 'react';
import PropTypes from 'prop-types';
import { Redirect, Route } from 'react-router';

import { UserConsumer } from './App';

const PrivateRouteWithoutContext = ({ user, ...rest }) =>
    user ? (
        <Route {...rest} />
    ) : (
        <Redirect
            to={{
                pathname: '/login',
                state: { from: rest.location },
            }}
        />
    );

PrivateRouteWithoutContext.propTypes = {
    user: PropTypes.object,
};

export const PrivateRoute = props => {
    return (
        <UserConsumer>
            {({ user }) => (
                <PrivateRouteWithoutContext user={user} {...props} />
            )}
        </UserConsumer>
    );
}; 
```

Enter fullscreen mode Exit fullscreen mode

注意，`Consumer`使用了**渲染道具**模式。

```
// in components/Header.js
import React from 'react';
import PropTypes from 'prop-types';

import { UserConsumer } from '../App';
import { Navigation } from './Navigation';

export const HeaderWithoutContext = ({ user, onLogout }) => (
    <header>
        <h1>JavaScript Playground: meetups</h1>
        {user && <Navigation onLogout={onLogout} />}
    </header> );

HeaderWithoutContext.propTypes = {
    user: PropTypes.object,
    onLogout: PropTypes.func.isRequired,
};

export const Header = () => {
    return (
        <UserConsumer>
            {({ user, onLogout }) => (
                <HeaderWithoutContext user={user} onLogout={onLogout} />
            )}
        </UserConsumer>
    );
}; 
```

Enter fullscreen mode Exit fullscreen mode

React Context 是将数据从应用程序的 N 级组件直接传送到任何 N-x 级子组件的简单方法。

## 那么，Redux 还是不 Redux？

一旦项目达到一定的复杂程度，Redux 就变得有趣了。然而，预先判断代码的复杂程度并不是一个好主意！我喜欢把事情简单化，对自己说:“太好了！之后我要做一个复杂的。这让我想起了几年前，Symfony 被系统地用来启动一个 PHP 项目，而 Silex 让它上手起来舒服多了，也快多了。

尽管如此，就像 Symfony 一样，使用 Redux 可以成为一个非常明智的选择。

**在项目开始时使用它只是一个不成熟的决定。**

这不是什么新鲜新闻😄

> 你可能不需要 Redux。
> 
> — Dan Abramov ([@dan_abramov](https://dev.to/dan_abramov)) [19 septembre 2016](https://twitter.com/dan_abramov/status/777983404914671616)

此外，除了这些理论上的考虑，似乎远离 Redux 也有一些有益的影响。

首先，我更关注 React！通过编写这篇文章中的第二个例子，我重新发现了只用砖块或组件构建应用程序的乐趣:就像玩乐高一样。使用**渲染属性**允许在整个项目中重用代码，同时保持嵌套 React 组件的逻辑。这是一个强大的模式，没有 [HOC](https://reactjs.org/docs/higher-order-components.html) 那么神奇。再者，到时候会适应 Redux 可能的实现。这方面的证据是 [react-admin 2.0](https://marmelab.com/blog/2018/05/18/react-admin-2-0.html) ，它将 [UI 部分](https://github.com/marmelab/react-admin/tree/master/packages/ra-ui-materialui)从[应用程序逻辑](https://github.com/marmelab/react-admin/tree/master/packages/ra-core)中分离出来，这要归功于一个渲染道具。

最后，这似乎是 React 团队采取的方向。有了新的 **Context API** ，他们提供了在不采用 Redux 的情况下建立一个易于共享的全球商店的可能性。