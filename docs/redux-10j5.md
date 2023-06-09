# 为什么不能在 reducer 上引起副作用

> 原文：<https://dev.to/origamium/redux-10j5>

# TL；速度三角形定位法(dead reckoning)

如果在 reducer 中发生副作用，最坏的情况是无法调整组件

# Subject

你好。 首先有了这样的代码。

```
class Account {
    constructor(id, name){
        this.id = id;
        this.name = name;
        this.aka = [];
    }

    pushAka(name){
        this.aka.push(name);
        return this;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我想用 reducer 管理这个实例。 有那样的事吧。 例如，如下所示…(隐含使用…(redux-actions 这一点请原谅)

```
const initState = new Account('Donald Trump');

export default handleActions({
  "ADD_AKA": (state, action) => (state.pushAka(action.payload.aka))
}, initState); 
```

Enter fullscreen mode Exit fullscreen mode

这个，如果什么都不想的话，总之先做好。 但是，[作为 Redux 的 3 个基本概念](https://redux.js.org/introduction/three-principles)的大前提**在不能引起副作用的**这一点上是错误的。

问题出在`Account`类的`pushAka(name)`方法中。 毕竟，这是***在变更了自己本身的成员变量的基础上返回自己的***本身就有问题。 但是，目前运转良好。

[这是此时的 Stackblitz 的样本](https://stackblitz.com/edit/reducer-not-pure-anti-pattern)。

那么，现在正在动。 虽然已经是大问题了，但是从这里会发生无法挽回的事情。

React 虽然速度很快，但很多情况下还是需要调整。 为了防止组件的不必要的重新渲染，根据情况主要进行以下三种调整。

*   `componentShouldUpdate(prevState, nextState)`的使用
*   使用`React.PureComponent`而不是`React.Component`
*   无国籍功能 Componentsでは `recompose/pure`，`recompose/onlyUpdateForKeys` を使う
*   自己写 pure HoC

那么，在这种情况下也试着进行调整吧。 这次在刚才的样品中，`components/AkaList.js`是 Stateless Functional Components，我们试着用`pure`进行组件的调谐。 只需改写如下…

```
import React, {Fragment}from 'react';
import {pure} from 'recompose';

const AkaList = (props) => (
  <Fragment>
    {props.account.aka.map((v, i) => (<p key={i}>{v}</p>))}
  </Fragment>
)

export default pure(AkaList); 
```

Enter fullscreen mode Exit fullscreen mode

recompose 的`pure`试图抑制组件的重新渲染(例如有点极端，请原谅。 虽然没有时间，但是这边有不动的样品)

然后，在应该有列表的位置将不再渲染任何内容。 更具体地说，组件已装载，在首次渲染后将不再进行任何重新渲染。

从某种意义上来说，应该得到了最好的性能，但在所有情况下这都是个问题。

[是此时的 Stackblitz 的样本](https://stackblitz.com/edit/reducer-not-pure-anti-pattern-tuenning)。

# 应该怎么办

只能说是有副作用的设计不好。

在这里，第一个代码所示的`Account`类的`pushAka(name)`方法不好。 因此，将其替换为以下代码:

```
class Account {
    constructor(name){
        this.id = Date.now();
        this.name = name;
        this.aka = [];
    }

    pushAka(name){
        const r =  Object.assign(Object.create(Object.getPrototypeOf(this)), this);
        r.aka = [...r.aka, name];
        return r;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`r`中对自己进行浅拷贝，在此基础上建立新的数组。 总之，这就是动作。

[以及 Stackblitz 的样本](https://stackblitz.com/edit/reducer-not-pure-anti-pattern-tuenning-successed)。

在这种情况下，效果很好，但对于更复杂的实例、对象和数组，这可能无法解决问题。 但是，这种复杂的数据结构原本就可能设计不好。

# 结论

****保持纯函数**，**简单数据结构**，**！

## Remaining remarks

到目前为止的所有 Stackblitz 样本都引入了`redux-logger`。 特别是在发生副作用时的[第 1 个](https://stackblitz.com/edit/reducer-not-pure-anti-pattern-tuenning)和[第 2 个](https://stackblitz.com/edit/reducer-not-pure-anti-pattern-tuenning)打开开发工具，实际运行程序，追加 Donald Trump 的两个名称吧。

在几次尝试中，你会发现 logger 记录了非常有趣的行为。

[![](img/a32debc0b9a535fd1561121e1e77fe33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Svh2zOyz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bhstbm3hn4te0xaymdhb.png)

`prev state`和`next state`竟然是同一个东西。 不仅如此。

[![](img/6bfee477192e6cc30984cb3d6bdd1d8e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EXHi1V1e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wey83ohpegx13v72yqpj.png)

连过去的输出也被改变了——虽然是非常有趣、有趣的故事，但是要解释这个现象，我并不熟悉`redux`和`redux-logger`的安装。 请谁写这篇解说文章。 我这是极限了。