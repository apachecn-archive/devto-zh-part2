# 具有 RxJS 和 Redux 可观察值的 Redux 中的异步操作

> 原文：<https://dev.to/andrejnaumovski/async-actions-in-redux-with-rxjs-and-redux-observable-efg>

# 简介

### Redux 是什么？

[*Redux*](https://redux.js.org/) 是一个令人惊叹的库。对于那些不知道 Redux 是什么的人来说，它是一个用于 JavaScript 应用的**可预测状态容器**。在英语中，它作为应用程序状态的唯一来源。状态，或称为 Redux *存储*，只能通过分派*动作*来改变，这些动作由*reducer*来处理，Redux*根据分派的动作类型来决定如何修改状态。对于那些不熟悉 Redux 的人，可以查看一下 [**这个链接**](https://medium.com/codingthesmartway-com-blog/learn-redux-introduction-to-state-management-with-react-b87bc570b12a) 。*

现在，Redux 最常用于与 React 结合使用，尽管它并不局限于 React——它可以与任何其他视图库结合使用。

### Redux 的问题

然而，Redux 有一个非常重要的问题——它本身不能很好地处理**异步操作**。一方面，这是不好的，但另一方面，Redux 只是一个库，为应用程序提供状态管理，就像 React 只是一个视图库一样。这些都不构成一个完整的框架，不同的事情要自己选择使用的工具。有些人认为这是一件坏事，因为没有一种做事的方式，有些人，包括我，认为这是一件好事，因为你不局限于任何特定的技术。这很好，因为每个人都可以选择他们认为最符合自己需求的技术。

### 处理异步动作

现在，有几个库提供了 Redux 中间件来处理异步操作。当我刚开始使用 React 和 Redux 时，我被分配到的项目使用了 [**Redux-Thunk**](https://github.com/reduxjs/redux-thunk) 。Redux-Thunk 允许您编写返回函数而不是普通对象的动作创建器(默认情况下，Redux 中的所有动作都必须是普通对象)，这又允许您延迟某些动作的调度。

作为 React/Redux 的初学者，thunks 非常棒。它们易于编写和理解，并且不需要任何额外的功能——你基本上只是在编写动作创建器，只是方式不同。

然而，一旦您开始使用 React 和 Redux 进入工作流，您就会意识到，虽然非常容易使用，但是 thunks 并不是那么好，因为，1。您可能会陷入回调地狱，尤其是在发出 API 请求时，2。你要么用处理数据的业务逻辑填充你的回调或者你的缩减器(因为，老实说，你不可能每次都得到完全格式化的数据，尤其是如果你使用第三方 API 的话)，和 3。它们实际上是不可测试的(您必须使用 spy 方法来检查是否用正确的对象调用了 dispatch)。因此，我开始研究其他可能的更适合的解决方案。就在这个时候我撞见了 [**Redux-Saga**](https://redux-saga.js.org/) 。

Redux Saga 非常接近我所寻找的。从它的网站来看，*心理模型是一个传奇就像你的应用程序中的一个单独的线程，单独负责副作用。*这基本上意味着 *sagas* 独立于你的主应用程序运行，监听分派的动作——一旦特定 saga 监听的动作被分派，它就会执行一些产生副作用的代码，比如 API 调用。它还允许你从传奇中调度其他动作，并且很容易测试，因为传奇返回的*效果*是简单的对象。听起来很棒，对吧？

Redux-Saga 确实有一个权衡，而且对大多数开发人员来说是一个很大的权衡——它利用了 Javascript 的生成器函数，这有一个相当陡峭的学习曲线。现在，道具*(看我在那里做了什么，呵呵)*给 Redux Saga 的创作者，感谢他们使用 JS 的这一强大功能，然而，我确实觉得生成器函数使用起来感觉很不自然，至少对我来说是这样，尽管我知道它们如何工作以及如何使用它们，但我就是无法抽出时间来实际**使用**它们。*就像那个乐队或歌手，当他们在收音机里演奏时，你听起来没有问题，但你甚至不会想到自己演奏他们。*这就是我继续寻找异步处理 Redux 中间件的原因。

Redux-Saga 没有很好处理的另一件事是取消已经分派的异步动作——比如 API 调用(Redux Observable 由于其反应性而做得很好)。

### 下一步

大约一周前，我在看一个我和一个朋友为大学写的旧 Android 项目，并在那里看到一些 RxJava 代码，我心想:*如果有一个用于 Redux 的反应式中间件会怎么样？*所以我做了一些研究，嗯，诸神听到了我的祈祷:**提示[重复可观察](https://redux-observable.js.org/)T5】。**

那么什么是 Redux 可观测？它是 Redux 的另一个中间件，让您以一种**功能性、反应性和声明性的**方式处理异步数据流。这是什么意思？这意味着您要编写处理异步数据流的代码。换句话说，你基本上是在那些信息流中倾听新的价值(*订阅*信息流*)并对这些价值做出相应的反应。

关于一般反应式编程的最深入的指南，请查看[这个链接](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)，以及[这个链接](http://blog.danlew.net/2017/07/27/an-introduction-to-functional-reactive-programming/)。两者都很好地概述了什么是(函数式)反应式编程，并给了你一个很好的心智模型。

### Redux Observable 解决什么问题？

当考虑一个新的库/工具/框架时，最重要的问题是它将如何帮助你的工作。总的来说，Redux Observable 所做的一切，Redux-Saga 也会做。它将您的逻辑移到您的动作创建者之外，它在处理异步操作方面做得非常好，并且易于测试。然而，**在我看来**，Redux Observable 的整个工作流程感觉更自然，考虑到这两者都有一个陡峭的学习曲线(生成器和反应式编程一开始都有点难掌握，因为它们不仅需要学习，还需要适应你的心态)。

来自 Redux Observable 官方指南:以这种方式处理副作用的模式类似于“流程经理”模式，有时被称为“saga”，但 saga 的原始定义并不真正适用。如果你熟悉 redux-saga，redux-observable 非常相似。但是因为它使用 RxJS，所以它更具有声明性，并且您可以利用和扩展您现有的 RxJS 能力。

### 现在可以开始编码了吗？

所以，现在你知道什么是函数式反应式编程，如果你像我一样，你真的很喜欢使用数据的自然感觉。是时候将这个概念应用到 React/Redux 应用程序中了。

首先，作为任何 Redux 中间件，您必须在创建商店时将其添加到您的 Redux 应用程序中。

首先，要安装它，运行
`npm install --save rxjs rxjs-compat redux-observable`
或
`yarn add rxjs rxjs-compat redux-observable`
，这取决于你正在使用的工具。

现在，重复观测的基础是**史诗**。Epics 类似于 Redux-Saga 中的 Saga，不同之处在于，它不是等待一个动作分派并将该动作委托给一个工作人员，然后使用 yield 关键字暂停执行直到另一个相同类型的动作到来，而是单独运行并侦听动作流，然后在收到流中的特定动作时做出反应。主要组件是 Redux-Observable 中的`ActionsObservable`，它从 RxJS 扩展了`Observable`。这个可观察对象代表了一个动作流，每次你从应用程序中调度一个动作时，它都会被添加到这个流中。

好了，让我们从创建 Redux store 开始，并向其中添加 Redux Observable 中间件(小提醒一下，要引导 React 项目，您可以使用`create-react-app` CLI)。在我们确定已经安装了所有的依赖项(`redux, react-redux, rxjs, rxjs-compat, redux-observable`)之后，我们可以开始修改我们的`index.js`文件，如下所示

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

import { createStore, applyMiddleware } from 'redux';
import { createEpicMiddleware } from 'redux-observable';
import { Provider } from 'react-redux';

const epicMiddleware = createEpicMiddleware(rootEpic);

const store = createStore(rootReducer, applyMiddleware(epicMiddleware));

const appWithProvider = (
    <Provider store={store}>
        <App />
    </Provider> );

ReactDOM.render(appWithProvider, document.getElementById('root')); 
```

你可能已经注意到了，我们遗漏了`rootEpic`和`rootReducer`。这个不用担心，我们稍后会添加它们。现在，让我们来看看这里发生了什么:

首先，我们导入必要的函数来创建我们的商店并应用我们的中间件。之后，我们使用 Redux Observable 中的`createEpicMiddleware`来创建我们的中间件，并传递给它根 epic(我们稍后会谈到)。然后，我们使用`createStore`函数创建我们的商店，将我们的根 reducer 传递给它，并将 epic 中间件应用到商店。

好了，现在我们已经设置好了一切，让我们首先创建我们的根缩减器。创建一个名为`reducers`的新文件夹，并在其中创建一个名为`root.js`的新文件。向其中添加以下代码:

```
const initialState = {
    whiskies: [], // for this example we'll make an app that fetches and lists whiskies
    isLoading: false,
    error: false
};

export default function rootReducer(state = initialState, action) {
    switch (action.type) {
        default:
            return state;
    }
} 
```

任何熟悉 Redux 的人都知道这里发生了什么——我们正在创建一个 reducer 函数，它将`state`和`action`作为参数，并根据动作类型返回一个新的状态(因为我们还没有定义任何动作，我们只是添加了`default`块并返回未修改的状态)。

现在，返回到您的`index.js`文件并添加以下导入:

```
import rootReducer from './reducers/root'; 
```

如你所见，现在我们没有关于`rootReducer`不存在的错误。现在让我们创建我们的根史诗；首先，创建一个新文件夹`epics`，并在其中创建一个名为`index.js`的文件。现在，在其中添加以下代码:

```
import { combineEpics } from 'redux-observable';

export const rootEpic = combineEpics(); 
```

这里我们只是使用 Redux Observable 提供的`combineEpics`函数来组合我们的(到目前为止，不存在的)史诗，并将该值赋给我们导出的常数。我们现在应该通过简单地添加以下导入来修复条目`index.js`文件中的另一个错误:

```
import { rootEpic } from './epics'; 
```

太好了！现在我们已经处理了所有的配置，我们可以开始定义我们可以调度的动作类型，以及这些威士忌的动作创建者。

首先，创建一个名为 actions 的新文件夹和一个`index.js`文件。
(注意:对于大型的生产级项目，你应该以一种逻辑的方式将你的动作、减速器和史诗分组，而不是将它们都放在一个文件中，然而，这在这里没有意义，因为我们的应用程序非常小)

在我们开始编写代码之前，让我们考虑一下我们可以分派什么类型的操作。通常，我们需要一个动作来通知 Redux/Redux-Observable 它应该开始获取威士忌，让我们称这个动作为 FETCH _ 威士忌。由于这是一个异步操作，我们不知道它确切的完成时间，所以我们希望在调用成功完成时调度一个 FETCH _ 威士忌 _ 成功操作。以类似的方式，由于这是一个 API 调用，它可能会失败，我们希望用一条消息通知我们的用户，因此我们将调度一个 FETCH _ 威士忌 _ 失败操作，并通过显示一条错误消息来处理它。

让我们用代码来定义这些动作(及其动作创建者):

```
export const FETCH_WHISKIES = 'FETCH_WHISKYS';
export const FETCH_WHISKIES_SUCCESS = 'FETCH_WHISKYS_SUCCESS';
export const FETCH_WHISKIES_FAILURE = 'FETCH_WHISKYS_FAILURE';

export const fetchWhiskies = () => ({
    type: FETCH_WHISKIES,
});

export const fetchWhiskiesSuccess = (whiskies) => ({
    type: FETCH_WHISKIES_SUCCESS,
    payload: whiskies
});

export const fetchWhiskiesFailure = (message) => ({
    type: FETCH_WHISKIES_FAILURE,
    payload: message
}); 
```

对于任何不清楚我在这里做什么的人，我只是简单地为动作类型定义常数，然后使用 ES6 的 lambda 简写符号，我正在创建 arrow 函数，它返回一个包含类型和(可选)有效负载属性的普通对象。类型用于标识已经分派了哪种类型的动作，有效负载是在分派动作时如何将数据发送到 reducers(和存储)(注意:第二个属性不一定要称为有效负载，您可以随意命名它，我这样做只是为了保持一致性)。

现在我们已经创建了我们的动作和动作创建者，让我们在我们的 reducer 中处理这些动作:
将您的`reducers/index.js`更新为以下内容。

```
import {
    FETCH_WHISKIES,
    FETCH_WHISKIES_FAILURE,
    FETCH_WHISKIES_SUCCESS
} from '../actions';

const initialState = {
    whiskies: [],
    isLoading: false,
    error: null
};

export default function rootReducer(state = initialState, action) {
    switch (action.type) {
        case FETCH_WHISKIES:
            return {
                ...state,
                // whenever we want to fetch the whiskies, set isLoading to true to show a spinner
                isLoading: true,
                error: null
            };
        case FETCH_WHISKIES_SUCCESS:
            return {
                whiskies: [...action.payload],
                // whenever the fetching finishes, we stop showing the spinner and then show the data
                isLoading: false,
                error: null
            };
        case FETCH_WHISKIES_FAILURE:
            return {
                whiskies: [],
                isLoading: false,
                // same as FETCH_WHISKIES_SUCCESS, but instead of data we will show an error message
                error: action.payload
            };
        default:
            return state;
    }
} 
```

现在我们已经完成了所有这些，我们终于可以写一些 Redux-Observable 代码了(抱歉花了这么长时间！)

转到你的`epics/index.js`文件，让我们创建我们的第一部史诗。首先，您需要添加一些导入:

```
import { Observable } from 'rxjs';
import 'rxjs/add/operator/switchMap';
import 'rxjs/add/operator/map';
import 'rxjs/add/observable/of';
import 'rxjs/add/operator/catch';
import { ajax } from 'rxjs/observable/dom/ajax';

import {
    FETCH_WHISKIES,
    fetchWhiskiesFailure,
    fetchWhiskiesSuccess
} from "../actions"; 
```

我们在这里做的是导入我们需要分派的动作创建者，以及我们需要在动作流中观察的动作类型，以及来自 RxJS 和`Observable`的一些操作符。请注意，rxjs 和 Redux Observable 都不会自动导入操作符，因此您必须自己导入它们(另一个选项是在您的 entry index.js 中导入整个“RxJS”模块，但是我不建议这样做，因为这会给您带来很大的包大小)。好了，让我们来看一下我们导入的这些运算符，以及它们的作用:

`map`——类似于 Javascript 的原生`Array.map()` , `map`对流中的每个项目执行一个函数，并返回一个新的流/可观察的映射项目。
`of`——从一个不可观察的值中创建一个可观察的/流(它可以是一个原语，一个对象，一个函数，任何东西)。
`ajax`——是提供的用于做 AJAX 请求的 RxJS 模块；我们将用它来调用 API。
`catch` -用于捕捉任何可能发生的错误
`switchMap` -是其中最复杂的。它所做的是，它采用一个返回可观察对象的函数，并且每当这个内部可观察对象发出一个值时，它将该值合并到外部可观察对象(switchMap 被调用的对象)。这里有一个陷阱，每次一个新的内部可观察对象被创建，外部可观察对象订阅它(即监听值并将它们合并到自身)，并取消所有其他对先前发出的可观察对象的订阅。这对于我们不关心之前的结果是成功还是被取消的情况很有用。例如，当我们调度多个动作来获取威士忌时，我们只需要最新的结果，switchMap 正是这样做的，它将订阅最新的结果，并将其合并到外部可观察对象中，如果之前的请求仍未完成，则丢弃它们。当创建 POST 请求时，您通常关心前一个请求是否已经完成，这就是使用 mergeMap 的时候。`mergeMap`做同样的事情，除了它不退订以前的观察。

考虑到这一点，让我们看看取威士忌的史诗将会是什么样子:

```
const url = 'https://evening-citadel-85778.herokuapp.com/whiskey/'; // The API for the whiskies
/*
    The API returns the data in the following format:
    {
        "count": number,
        "next": "url to next page",
        "previous": "url to previous page",
        "results: array of whiskies
    }
    since we are only interested in the results array we will have to use map on our observable
 */

function fetchWhiskiesEpic(action$) { // action$ is a stream of actions
    // action$.ofType is the outer Observable
    return action$
        .ofType(FETCH_WHISKIES) // ofType(FETCH_WHISKIES) is just a simpler version of .filter(x => x.type === FETCH_WHISKIES)
        .switchMap(() => {
            // ajax calls from Observable return observables. This is how we generate the inner Observable
            return ajax
                .getJSON(url) // getJSON simply sends a GET request with Content-Type application/json
                .map(data => data.results) // get the data and extract only the results
                .map(whiskies => whiskies.map(whisky => ({
                    id: whisky.id,
                    title: whisky.title,
                    imageUrl: whisky.img_url
                })))// we need to iterate over the whiskies and get only the properties we need
                // filter out whiskies without image URLs (for convenience only)
                .map(whiskies => whiskies.filter(whisky => !!whisky.imageUrl))
            // at the end our inner Observable has a stream of an array of whisky objects which will be merged into the outer Observable
        })
        .map(whiskies => fetchWhiskiesSuccess(whiskies)) // map the resulting array to an action of type FETCH_WHISKIES_SUCCESS
        // every action that is contained in the stream returned from the epic is dispatched to Redux, this is why we map the actions to streams.
        // if an error occurs, create an Observable of the action to be dispatched on error. Unlike other operators, catch does not explicitly return an Observable.
        .catch(error => Observable.of(fetchWhiskiesFailure(error.message)))
} 
```

在这之后，还有一件事要做，那就是将我们的 epic 添加到`combineEpics`函数调用中，就像这样:

```
export const rootEpic = combineEpics(fetchWhiskiesEpic); 
```

好吧，这里发生了很多事，我承认。但是让我们一点一点地把它拆开。

`ajax.getJSON(url)`将请求中的数据作为流中的值返回一个可观察值。
`.map(data => data.results)`从可观察对象中获取所有值(在本例中只有 1)，从响应中获取`results`属性，并返回一个带有新值的新可观察对象(即只有`results`数组)。

```
.map(whiskies => whiskies.map(whisky => ({
                    id: whisky.id,
                    title: whisky.title,
                    imageUrl: whisky.img_url
                }))) 
```

从以前的可观察对象(结果数组)中获取值，对其调用`Array.map()`，并映射数组的每个元素(每种威士忌)以创建一个新的对象数组，该数组仅保存每种威士忌的 id、标题和 imageUrl，因为我们不需要任何其他内容。

`.map(whiskies => whiskies.filter(whisky => !!whisky.imageUrl))`获取可观察对象中的数组，并返回一个带有过滤后的数组的新可观察对象。

包装这段代码的`switchMap`获取这个可观察对象，并将内部可观察对象的流合并到调用`switchMap`的可观察对象的流中。感谢`switchMap`，如果另一个取威士忌的请求通过，这个操作将再次重复，之前的结果将被丢弃。

`.map(whiskies => fetchWhiskiesSuccess(whiskies))`简单地获取我们添加到流中的这个新值，并将其映射到 FETCH _ 威士忌 _SUCCESS 类型的动作，该动作将在可观察对象从 Epic 返回后被调度。

捕捉任何可能发生的错误，并简单地返回一个可观察值。然后，这个可观察对象通过 switchMap 传播，switch map 再次将它合并到外部可观察对象中，我们在流中得到一个 FETCH _ 威士忌 _ 失败类型的操作。

慢慢来，这是一个复杂的过程，如果你没有接触过反应式编程和 RxJS，它看起来和听起来都很可怕(阅读我上面提供的链接！).

在这之后，我们需要做的就是呈现一个 UI，它将有一个调度动作的按钮和一个显示数据的表格。就这么办吧；首先创建一个名为 components 的新文件夹和一个名为 Whisky.jsx 的新组件。

```
import React from 'react';

const Whisky = ({ whisky }) => (
    <div>
        <img style={{ width: '300px', height: '300px' }} src={whisky.imageUrl} />
        <h3>{whisky.title}</h3>
    </div> );

export default Whisky; 
```

该组件简单地呈现一个单一的威士忌项目，它的图像和标题。(拜托，*看在上帝的份上*，千万不要使用内嵌样式。我在这里这样做是因为这是一个简单的例子)。

现在我们要渲染威士忌元素的网格。让我们创建一个名为 WhiskyGrid.jsx 的新组件。

```
import React from 'react';

import Whisky from './Whisky';

const WhiskyGrid = ({ whiskies }) => (
    <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr 1fr' }}>
        {whiskies.map(whisky => (<Whisky key={whisky.id} whisky={whisky} />))}
    </div> );

export default WhiskyGrid; 
```

WhiskyGrid 所做的是利用 CSS-Grid 并创建每行 3 个元素的网格，简单地获取我们将作为道具传入的威士忌数组并将每种威士忌映射到一个威士忌组件。

现在让我们来看看我们的 App.js:

```
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
import './App.css';

import { fetchWhiskies } from './actions';

import WhiskyGrid from './components/WhiskyGrid';

class App extends Component {
  render() {
    const {
      fetchWhiskies,
      isLoading,
      error,
      whiskies
    } = this.props;

    return (
      <div className="App">
        <button onClick={fetchWhiskies}>Fetch whiskies</button>
        {isLoading && <h1>Fetching data</h1>}
        {!isLoading && !error && <WhiskyGrid whiskies={whiskies} />}
        {error && <h1>{error}</h1>}
      </div>
    );
  }
}

const mapStateToProps = state => ({ ...state });

const mapDispatchToProps = dispatch =>
    bindActionCreators({
        fetchWhiskies
    }, dispatch);

export default connect(mapStateToProps, mapDispatchToProps)(App); 
```

如你所见，这里有很多修改。首先，我们必须将 Redux 存储和动作创建者绑定到组件的道具。我们使用 react-redux 中的`connect` HOC 来这样做。之后，我们创建一个 div，它有一个按钮，按钮的 onClick 被设置为调用 fetchWhiskies 操作创建者，现在绑定到了`dispatch`。单击该按钮将会分派 FETCH _ 威士忌动作，我们的 Redux Observable epic 将会拾取它，从而调用 API。接下来我们有一个条件，如果 Redux 存储中的 isLoading 属性为 true(FETCH _ whitees 已经被调度，但是既没有完成也没有抛出错误),我们显示一个文本说 Load data。如果数据没有加载，并且没有错误，我们渲染`WhiskyGrid`组件，并将来自 Redux 的威士忌作为道具传递。如果 error 不为 null，我们将显示错误消息。

### 结论

变得被动并不容易。它呈现了一种完全不同的编程范式，迫使你以不同的方式思考。我不会说函数式的比面向对象的好，或者反应式的是最好的。最好的编程范例，**在我看来**，是范例的组合。然而，我相信 Redux Observable 确实为其他异步 Redux 中间件提供了一个很好的选择，在您通过学习曲线之后，您将拥有一个处理异步事件的惊人的、自然的方法。

如果你有任何问题，请在评论中提问！如果这引起了足够的兴趣，我们可以考虑推迟和取消行动。

干杯:)