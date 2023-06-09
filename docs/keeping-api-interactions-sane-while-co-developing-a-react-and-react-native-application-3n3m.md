# 在共同开发 React 和 React 本机应用程序时，保持 API 交互的合理性

> 原文：<https://dev.to/drews256/keeping-api-interactions-sane-while-co-developing-a-react-and-react-native-application-3n3m>

# React and React-Native 啊呀！

## 两个应用的故事

我最近开发了一个应用程序，它有两个独立的面向公众的应用程序。一部分是网络应用，另一部分是电话应用。分别用 React 和 React-Native 编写。这个用例并不罕见。从一开始就很明显，最终我们需要在 web 应用程序和电话应用程序中都提供大多数功能。我认为我们应该使用 Redux 进行状态管理，但是当它涉及到与 React 和 React-Native 的后端进行“交互”时，我感到非常困惑。

好的一面是它只是 Javascript，所以这里没有什么特别的东西。

react-原生生命周期挂钩是相同的，总的来说，我们希望让我们的移动应用体验更像我们的 web 应用体验。

经过一些研究后，我们决定使用 Redux-Saga 来使我们的生活更轻松，我们能够复制两个应用程序之间的大多数 api 交互。甚至有一种想法认为，我们可以将 API 交互从视图中完全分离出来。不过，这可能比听起来要困难一些。无论哪种方式的代码示例！

## 但只有一个后端

目前，后端是用 Rails 编写的，是作为一个纯 API 应用程序构建的。这让我们完全分离后端和前端。我们能够静态托管 web 应用程序的所有 JS 和 CSS。我们从网络服务器上提供零 html。多酷啊！？(我想它没那么酷，毕竟它是一个温泉浴场...)虽然整个 API 并不遵循 JSON API 规范，但它确实服务于符合 JSON API 的 JSON。我认为这给我们的 API 增加了一致性。我想继续构建符合 JSON API 的 API，但这无关紧要。

这是一个我们用来与后端交互的传奇故事的例子。

`src/sagas/inventory.js`

```
import { call, put, takeLatest } from 'redux-saga/effects'
import { defineTypes } from '../helpers'
import ProductsEndpoint from '../persistance/ProductsEndpoint'

// I use a custom Fetch implementation that wraps my requests to force
// authorization when required, it also forces JSON responses to be camelCased and // returns a consistent response from each request that we make.
import { fetch } from '../http/reduxFetch'

// This helper lets us have consistent reducer names, though it ended up not being perfect. 
const inventoryTypes = { ...defineTypes('INVENTORY')  }

export const initialState = {
  loading: false,
  loaded: false,
  stocked: false,
  error: {},
  products: [],
  inventories: []
}

// Each sagas exports actions and a reducer, which lets us combine them together // or use them across the app. Since they are all called 'actions' and 'reducer' // consistently it's super easy to import them at any point or from any component.

export const actions = {
  getInventory () {
    return { type: inventoryTypes.REQUEST }
  },
  toggleStock () {
    return { type: 'TOGGLE_STOCK' }
  }
}

export const reducer = (state = initialState, action) => {
  switch (action.type) {
    case inventoryTypes.REQUEST:
      return { ...state, started: true, loading: true }
    case inventoryTypes.DESTROY:
      return { ...state, loading: false }
    case 'TOGGLE_STOCK':
      return { ...state, stocked: !state.stocked }
    case inventoryTypes.FAILURE:
      return { ...state, loading: false, error: action.error }
    case inventoryTypes.SUCCESS:
      return { ...state, loading: false, loaded: true, inventories: action.inventories, products: action.products }
    default:
      return state
  }
}

// JSON API led to some heavy selectors. It would be prudent to add an inventory // selector or an attribute selector, but add it to the list!

export const selectors = {
  inventories: (store) => {
    return store['inventory'] && store['inventory']['inventories']
  },
  products: (store) => {
    return store['inventory'] && store['inventory']['products']
  },
  stocked: (store) => {
    return store['inventory'] && store['inventory']['stocked']
  },
  loaded: ({ inventory }) => {
    return inventory && inventory['loaded']
  },
}

export function * inventorySaga () {
  try {
    const products_response = yield call(fetch, ProductsEndpoint.index())

    if (products_response['data'] ) {
      yield put({ 
        type: inventoryTypes.SUCCESS,
        products: products_response.data 
      })
    } else {
      yield put({
        type: inventoryTypes.FAILURE,
        error: products_response['error'] 
      })
   }
  } catch (error) {
    yield put({ 
        type: inventoryTypes.FAILURE, 
        error 
    })
  }
}

// All our top level Saga's are exported as a rootSaga. This lets us import and 
// combine all of our sagas in a rootSaga file so we can keep everything 
// organized and check name spacing.

export function * rootSaga () {
  yield takeLatest(inventoryTypes.REQUEST, inventorySaga)
} 
```

总的来说，这很简单，看起来像一个 Redux，带有一些奇特的产出和生成器功能。上面最复杂的事情是传奇本身。如果你不太了解生成器函数，你应该看看这个家伙，他解释得很好。

[https://davidwalsh.name/es6-generators](https://davidwalsh.name/es6-generators)

简单来说，它们基本上是逐句通过函数，并允许你调用函数上的`.next()`来运行函数，直到遇到下一个`yield`关键字。

Redux Saga 是一个基于动作调用运行生成器函数的 Redux 副作用库。所以你不必在你的减速器里有你的业务逻辑。它可以让你在任何动作的任何调用上运行任何传奇，你也可以使用许多类型的效果，让你只采取一个动作(这样你就不会重复 API 调用)，或者只采取最后一个动作调用(这样你就可以调用多次，它只采取最新的)，有许多选项，但这些是基本的。

这里有一些还原传奇的文档:[https://github.com/redux-saga/redux-saga](https://github.com/redux-saga/redux-saga)

Redux Saga 的另一个很酷的地方是你的组件对它一无所知。因为您通过 Redux 向组件公开了您的选择器。方便吧！？它们是不依赖于组件生命周期挂钩*或回调的 API 调用。事实上，我们可以合并整个商店，然后一次呈现一个视图，组件不会知道。我们也可以调用 API 端点一次，然后在进行过程中从响应中选择我们需要的东西，而不是多次调用 API。

*注意，在某些时候，我们确实需要进行依赖于被渲染组件的 API 调用，但是这个*极大地*减少了我们正在进行的生命周期挂钩的数量。我还倾向于让父组件挂钩，然后根据父组件中一个生命周期挂钩的 API 调用从存储中选择子组件。

现在我们有了数据，我们如何利用它呢？嗯，只需要一个连接的组件，突然间我们就可以从选择器中提取数据了。

在严格反应式应用中:

`src/components/Inventory.jsx`和`src/components/Inventories.jsx`

```
import React, { Component } from 'react';

class Inventory extends Component {

  render() {
    return (
      <div>
        <div>
          { this.props.inventory && this.props.inventory['name']
        </div>
        <div>
          { this.props.inventory && this.props.inventory['attribute']
        </div>
      </div>
    )
  }
} 
```

```
import React, { Component } from 'react';
import { selectors, actions } from '../sagas/inventory';

class Inventories extends Component {
  componentDidMount() {
    this.props.getInventory()
  }

  render() {
    return (
      <div>
        { this.props.inventories && this.props.inventories.map(inventory => {
          <Inventory inventory={inventory} />
        })
      </div>
    )
  }
}

// Since 'Inventories' is connected, any time we change inventories in our store,
//they're gonna get updated through the component. 

const mapStateToProps = (store) => ({
   inventories: selectors.inventories(store)
});

const mapDispatchToProps = (dispatch) => bindActionCreators(actions, dispatch);

export default connect(mapStateToProps, mapDispatchToProps)(Inventories); 
```

或者在反应式应用中

`src/components/Inventory.jsx`和`src/components/Inventories.jsx`

```
import React, { Component } from 'react';
import {
  View,
  Image,
  Text,
  StyleSheet,
  ScrollView,
} from 'react-native';

export default class Inventory extends Component {

  render() {
    return (
      <View>
        <Text>
          { this.props.inventory && this.props.inventory['name']
        </Text>
        <Text>
          { this.props.inventory && this.props.inventory['attribute']
        </Text>
      </View>
    )
  } 
```

```
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
import {
  View,
  Image,
  Text,
  StyleSheet,
  ScrollView,
} from 'react-native';

import { selectors, actions } from '../sagas/inventory';
import Inventory from './Inventory'

class Inventories extends Component {
  componentDidMount() {
    this.props.getInventory()
  }

  render() {
    return (
      <View>
        { this.props.inventories && this.props.inventories.map(inventory => {
          <Inventory inventory={inventory} />
        })
      </View>
    )
  }
}

// Since 'inventories' are connected, any time we change inventories in our store, they're gonna get updated through the component. 

const mapStateToProps = (store) => ({
   inventories: selectors.inventories(store)
});

const mapDispatchToProps = (dispatch) => bindActionCreators(actions, dispatch);

export default connect(mapStateToProps, mapDispatchToProps)(Inventories); 
```

React-Native 和 React 之间几乎完全相同的组件。但是所有的业务逻辑都被放入了这个故事中，所以你不必太担心组件之外发生了什么。分享传奇的逻辑和展示我们想要的东西非常容易。

总的来说，我们快速浏览了一个使用 Redux-Saga 在 React-Native 和 React 应用程序之间共享 API 交互的例子。它允许您针对单个 API 快速构建 React 和 React-Native 应用程序，但在两个应用程序之间封装和共享业务逻辑，即使组件是完全独立的。

*我绝不是 JS/React 或 React 原生专家。让我知道，如果你看到任何时髦的东西，或者有任何提示来帮助我提升我的 JS 或 React 游戏。