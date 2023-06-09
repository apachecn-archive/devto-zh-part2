# 具有易反应状态的实用模式

> 原文：<https://dev.to/solkimicreb/practical-patterns-with-react-easy-state-3ehh>

React Easy State 是一个透明的反应式状态管理库，有两个函数和两个伴随规则。

1.  始终用`view()`包装您的组件。
2.  总是用`store()`包装你的状态存储对象。

```
import React from 'react'
import { store, view } from 'react-easy-state'

const counter = store({
  num: 0,
  incr: () => counter.num++
})

export default view(() =>
  <button onClick={counter.incr}>{counter.num}</button>
) 
```

这足以让它在需要时自动更新您的视图。无论您如何构建或改变您的状态存储，任何语法上有效的代码都可以工作。

如果您还不熟悉 Easy State，请不要担心，您已经了解了足够的知识，可以继续学习。或者，你可以在这里查看[。](https://github.com/solkimicreb/react-easy-state)

## 国家管理和啤酒

Easy State 不关心你如何管理你的状态，它寻找任何类型的状态突变，并在需要时更新视图。但是这篇文章是关于状态管理模式的。非常简单的模式，但是被无数固执己见的库所掩盖。

接下来的部分将通过一个小应用程序探索这些模式，该应用程序会为您的餐食找到匹配的啤酒。我建议你在继续阅读之前先[尝试一下](https://solkimicreb.github.io/react-easy-state/examples/beer-finder/build/)。

[![Edit beer-finder](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/github/solkimicreb/react-easy-state/tree/master/examples/beer-finder?codemirror=1&hidenavigation=1&module=%2Fsrc%2FappStore.js)

## 组件间共享全局状态

React 的`state`和`setState`通常足以管理本地状态，但是大型项目往往需要更多。有些信息最好全局保存。

JavaScript 对象是单件的，它们可以在带有 ES6 `import`和`export`的文件之间共享。这使得它们成为存储全局状态的完美候选。

```
import { store } from 'react-easy-state'

const appStore = store({
  beers: [],
  fetchBeers (filter) {
    appStore.isLoading = true
    appStore.beers = [{ name: 'Awesome Beer' }]
    appStore.isLoading = false
  }
})

export default appStore 
```

> 注意`fetchBeers`方法是如何使用`appStore.beers`而不是`this.beers`的。这是一个巧妙的技巧，它使得对象方法可以安全地作为回调来传递。

先不要担心虚拟的`fetchBeers`方法，我们稍后会改进它。可以导入并在任何文件中使用`appStore`——比如下面的`NavBar`组件。

```
import React from 'react'
import { view } from 'react-easy-state'
import SearchBar from 'material-ui-search-bar'
import { LinearProgress } from 'material-ui/Progress'
import appStore from './appStore'

export default view(() =>
  <div className='searchbar'>
    <SearchBar onRequestSearch={appStore.fetchBeers} placeholder='Some food ...'/>
    {appStore.isLoading && <LinearProgress/>}
  </div>
) 
```

我们需要另一个组件来显示获取的啤酒。自然，它还必须导入全局`appStore`来将视图映射到它的 beers 数组。

```
import React from 'react'
import { view } from 'react-easy-state'
import appStore from './appStore'
import Beer from './Beer'

export default view(() =>
  <div className='beerlist'>
    {!appStore.beers.length
      ? <h3>No matching beers found!</h3>
      : appStore.beers.map(beer => <Beer key={beer.name} {...beer }/>)
    }
  </div>
) 
```

> 当`appStore.isLoading`或`appStore.beers`改变时，Easy State 重新渲染上述`NavBar`和`BeerList`组件。

## 异步动作

让我们给这个方法注入活力。没有太多的变化:它应该变成一个异步方法，应该从 API 获取啤酒，而不是伪造啤酒。

```
 import { store } from 'react-easy-state'
import * as api from './api'

const appStore = store({
  beers: [],
  async fetchBeers (filter) {
    appStore.isLoading = true
    appStore.beers = await api.fetchBeers(filter)
    appStore.isLoading = false
  }
})

export default appStore 
```

一个理想的商店只负责状态操作，其他什么都不负责。将组件中与视图相关的逻辑和 API 层中的网络逻辑抽象出来是一个很好的实践。这可能意味着在组件的事件处理程序中析构事件，并在单独的 API 层中处理身份验证和头。

我们的 API 很简单。它只有一个功能，为传递的食物获取匹配的啤酒。

```
import axios from 'axios'

const api = axios.create({
  baseURL: 'https://api.punkapi.com/v2'
})

export async function fetchBeers (filter) {
  const { data } = await api.get('/beers', {
    params: { food: filter }
  })
  return data
} 
```

> 这个例子使用了 [Punk API](https://punkapi.com/documentation/v2) 来查找 beers。如果您的编码演示需要一些免费数据，请查看一下。

## 封装本地状态

全局状态对于大型应用程序来说至关重要，但是局部状态也同样方便:它改善了项目结构和可重用性。决定何时使用哪个是你的责任。

我们仍然缺少一个 Beer 组件，它可以使用一些本地状态在图片和描述视图之间切换。将状态存储对象作为属性放在组件上是实现这一点的简单方法。

```
 import React, { Component } from 'react'
import { view, store } from 'react-easy-state'
import Card, { CardContent, CardMedia } from 'material-ui/Card'

class Beer extends Component {
  store = store({ details: false })

  toggleDetails = () => this.store.details = !this.store.details

  render () {
    const { name, image_url, food_pairing, description } = this.props
    const { details } = this.store

    return (
      <Card onClick={this.toggleDetails} className='beer'>
        {!details && <CardMedia image={image_url} className='media'/>}
        <CardContent>
          <h3>{name}</h3>
          {details
            ? <p>{description}</p>
            : <ul>
                {food_pairing.map(food => <li key={food}>{food}</li>)}
              </ul>
            }
        </CardContent>
      </Card>
    )
  }
}

export default view(Beer) 
```

> 每当`store.details`改变时，简单状态重新呈现`Beer`组件。不管是本地存储还是共享的全局存储，您甚至可以在单个组件中混合使用这两种存储。

`details`标志在啤酒卡的两种视图之间切换。它也可以存储在 beer 对象本身上，但是将它放在一个隔离的本地存储中是一种更干净的方法。它存储与视图相关的元数据，这些元数据不会污染真实的数据。

## 结论

Npm 包含了令人惊叹的工具，极大地简化了前端开发。当你需要它们的时候，不要犹豫使用它们，但是在你安装之前一定要考虑清楚。有时候，使用更少的工具可以提高工作效率。

有些人甚至不会把上面的片段称为模式。它们只是大多数开发人员熟悉的代码示例。尽管如此，对于一个小应用来说，它们已经足够了。

如果这篇文章引起了你的兴趣，请分享出来。还可以看看[易邦回购](https://github.com/solkimicreb/react-easy-state)并在离开前留下一颗星。

*谢谢！*
*(本文原载于[中](https://medium.com/dailyjs/design-patterns-with-react-easy-state-830b927acc7c) )*