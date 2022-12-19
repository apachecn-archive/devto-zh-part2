# 可怕的反应-钩子-第 2 部分-使用效果

> 原文：<https://dev.to/tclain/awesome-react-hooks---part-2---useeffect-29ao>

> 这篇文章是 React 钩子系列文章的第二篇。

第一部分:

[![tclain image](img/e1627620f56a36e5c1d846af5f0be8ff.png)](/tclain) [## 牛逼的 React 钩子，第 1 部分，使用状态

### 提摩太索赔

#reactreacthooks](/tclain/awesome-react-hooks-part-1-usestate--11n6)

在上一篇帖子中，我们看到了如何在 React 16.7+中使用`useState`钩子。今天我们来学习一下`useEffect`

# TLDR

`useEffect`将一个`callback`函数作为参数，它将在你的函数组件每次重新提交后重新运行**。**

如果这个回调函数返回另一个函数，那么这个函数将在组件卸载时被调用。

`useEffect`，可以接受第二个参数:`any[]`，这是一个应该触发重写的依赖列表。这些依赖可以是一个道具，或者由`setState`产生的另一种状态。

## 持久化计数器的示例

假设我们像以前一样获取计数器增量，并希望该值被持久存储在 localStorage 中，

我们可以为此使用`useEfffect`。

提醒一下，下面是我们的基本代码:

```
 import React, {useState, useEffect} from 'react'
function Counter() {
  const [counter, setCounter] = useState(0)
    // persist logic will be here
  return {
    counter, 
    setCounter
  }
} 

export const App = () => {
   const {setCounter, counter} = Counter();
  return <button onClick={() => setCounter(counter + 1)}>Change {counter}</button> } 
```

`Counter`函数定义了一个 useState 钩子来存储我们计数器值。

如果我们要定义:

```
 useEffect(() => {
      window.localStorage.setItem('counter', counter)
  }) 
```

`setItem`操作将在每次刷新后**运行。**

我们还有一步，第一次用 localStorage 中的值填充计数器值。

```
 const [counter, setCounter] = useState(JSON.parse(window.localStorage.getItem('counter')) || 0) 
```

完整的例子可以在这里找到:

[https://stackblitz.com/edit/react-use-effect-hook?file=index.js](https://stackblitz.com/edit/react-use-effect-hook?file=index.js)

## 清理功能

如果从 useEffect 回调函数返回一个函数，这个函数将被清除。如果您需要取消订阅全局事件，这将非常有用...诸如此类。

## 实际例子

假设我们有一个异步搜索框，显示来自 GitHub 的用户列表。

我们可以使用`useState`和`useEffect`的组合从用户输入的查询中动态获取列表。

正如我们之前所做的，让我们创建一个定制的钩子函数。

```
 function useGithubUsers() {
  const [query, setQuery] = useState("")
  const [results, setResults] = useState([])
  const [loading, setLoading] = useState(true)
// side effect here
    // exposing only public api !
    return {
        query, setQuery, results, loading    
    }  
} 
```

所以我们基本上声明了三个变量:`query`(当前查询搜索
)，`results`(Github 用户数组)，`loading`(一个加载指示器)。

下面是我们如何使用这个自定义挂钩:

```
export const App = () => {
  const { setQuery, query, results, loading } = useGithubUsers();
  return <div>
    <input onChange={e => setQuery(e.target.value)} />
    <ul>
      {loading && <li>Loading</li>}
      {
        results && results.map((item, index) => <li key={index} >{item.login}</li>)
      }
    </ul>
  </div> } 
```

钩子的酷之处在于，我们可以很容易地推断出数据的生命周期。

在这里，如果我们在`useState`声明和返回函数之间插入 useEffect 钩子，那么所有`state`之后的副作用都将改变，组件重新呈现。

让我们使用来自 github.com 的`search` API 来获取(双关语)GitHub 用户。

```
 if (query !== "") {
      setLoading(true);
      fetch(`https://api.github.com/search/users?q=${query}`, { method: "GET"}).then(req => {
        return req.json();
      }).then(data => {
        setLoading(false)
        setResults(data.items)
      })
    } 
```

如果你直接运行这段代码，你会遇到一个大问题，因为 useEffect 会在每次重新渲染后重新运行(在这种情况下也称为无限循环)，所以你需要使用`useEffect`函数的第二个参数，它接受一个需要改变的变量数组来运行这个效果( **a la shouldComponentUpdate** )

```
 setEffect( () => {...}, [query]) 
```

> 如果您提供一个空数组，那么当组件挂载时，效果中的任何代码将只运行一次，而`dispose`返回的函数中的任何代码将只在组件卸载时运行。
> 顺便说一句，你可以在这里使用道具，给你一个整洁的方式来应对道具的变化！

生成的代码是:

```
import React, { useState, useEffect } from 'react'
import { render } from 'react-dom'
import Select from 'react-select';
import debounce from 'lodash.debounce';

function useGithubUsers() {
  const [query, setQuery] = useState("")
  const [results, setResults] = useState([])
  const [loading, setLoading] = useState(false)
  // each rerender
  useEffect(debounce(() => {
    if (query !== "") {
      setLoading(true);
      fetch(`https://api.github.com/search/users?q=${query}`, { method: "GET"}).then(req => {
        return req.json();
      }).then(data => {
        setLoading(false)
        setResults(data.items)
      })
    }
  }, 300), [query])
  return {
    query,
    setQuery,
    setLoading,
    results,
    loading,
  }
}

export const App = () => {
  const { setQuery, query, results, loading } = useGithubUsers();
  return <div>
    <input onChange={e => setQuery(e.target.value)} />
    <ul>
      {loading && <li>Loading</li>}
      {
        results && results.map((item, index) => <li key={index} >{item.login}</li>)
      }
    </ul>
  </div> }

render(<App />, document.getElementById('root')); 
```

您可以在这里进行现场测试:

[https://stackblitz.com/edit/react-use-effect-hook-github](https://stackblitz.com/edit/react-use-effect-hook-github)？

> Github 对这个 API 有一些速率限制，所以如果它不起作用，请等待一分钟，然后重试。

当然，这个例子很琐碎，也很基础。没有什么能阻止你再抽象一点，并在 AJAX 请求中重用它:-)

## 然后

在下一篇文章中，我们将看到如何通过使用记忆化钩子来优化这个逻辑。