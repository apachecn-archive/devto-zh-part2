# 牛逼的 React 钩子，第 1 部分，使用状态

> 原文：<https://dev.to/tclain/awesome-react-hooks-part-1-usestate--11n6>

所以，如果你现在关注 react 社区，我们就火了！

有请功能组件的一个不可思议的东西:**挂钩**

## 什么是钩子？

到目前为止，我们，react 开发人员，区分了无状态组件(函数)和有状态组件(类)。

有了钩子，我们有了一组原语，可以帮助我们与本地状态、副作用，...用于**功能**组件。

这篇文章是介绍可用的 react-hook 的系列文章的第一篇。

## 使用状态挂钩

让我们从一个非常有用且简单的开始:

```
useState 
```

从 npm 上的`16.7.0-alpha.0`标记开始可以使用钩子，因此您可以运行:

```
npm install --save react@16.7.0-alpha.0 react-dom@16.7.0-alpha.0 
```

或者使用在线编辑器来测试它们:

(游乐场)[[https://stackblitz.com/edit/react-avqzi1](https://stackblitz.com/edit/react-avqzi1)

通过这个简单的例子，我们可以演示 useState 钩子的主要特性:

```
import { useState } from 'react';

function Example() {
  // Declare a new state variable, which we'll call "count"
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
} 
```

当我们调用`useState(initialValue: any)`时，这将返回一个包含 2 个元素的数组:

*   首先是数据的实际当前值
*   第二个是让你改变数据的功能。

一开始 count 等于 0，每次我们调用 setCount(count + 1)，**就会发生一次 re render**！

如果您曾经使用过`withState` from recompose，这基本上是相同的事情，除了:

*   这是更简单的推理
*   您不需要用另一个间接层来复杂化您的树，

需要注意的一点是，生成的状态是**隔离的**。有了钩子，我们能够重用*状态逻辑*，而不是*状态数据*。

因此，如果您再次调用 useState(或使用自定义钩子生成的 use state[稍后将详细介绍])，您将引用另一个数据单元格！

## 具体应用:验证

最有趣的部分是，我可以从另一个函数生成一个钩子，它可以在不同的组件中重用。

由于使用状态被映射到组件的“重新呈现”生命周期，我们可以很容易地对其进行推理。

```
 function setWithValidation(businessRule, setValidated, setData) {
        // simple IOC
        // create a new function that is able to set validated state and change the data value
        return newValue => {
            setValidated(businessRule(newValue))    
            setData(newValue)
        }
}

function withValidation(businessRule) {
    return initialData => {
        // create an data cell for the main data we need
        const [data, setData] = React.useState(initialData)
        // create a data cell for the validation state
        const [validated, setValidated] = React.useState(businessRule(initialData))
        // return in a nice object, the data, a modified set function and the validated state
        return {data, set: setWithValidation(businessRule, setValidated, setData), validated}
    }
} 
```

现在我有了一个可以验证的灵活的数据单元！
用法很简单:

```
 function isEven(value){ return value % 2 === 0}

const useEvenData = withValidation(isEven)

function Example() {
  // Declare a new state variable, which we'll call "count"
  const {data, validated, set} = useEvenData(0);

  return (
    <div>
      <p>You clicked {data} times</p>
        {validated ? <p>The number is even</p> : <p>The number is odd :-(</p> }
      <button onClick={() => set(data + 1)}>
        increment
      </button> 
      <button onClick={() => set(data -1)}>
        decrement
      </button>
    </div>
  );
} 
```

你可以在这里试试:

[在线示例](https://stackblitz.com/edit/react-hooks-simple-validation)

> 注意，你不能在组件的上下文之外使用钩子。也要把它放在组件的顶层(不要放在循环中，条件性的...)

就是这样！请随意评论，第二部分再见，带着**副作用钩子**。

[![tclain image](img/e1627620f56a36e5c1d846af5f0be8ff.png)](/tclain) [## 可怕的反应-钩子-第 2 部分-使用效果

### 提摩太索赔

#react#reacthooks](/tclain/awesome-react-hooks---part-2---useeffect-29ao)

### 链接

[https://reactjs.org/docs/hooks-intro.html](https://reactjs.org/docs/hooks-intro.html)
T3】https://reactjs.org/docs/hooks-overview.html