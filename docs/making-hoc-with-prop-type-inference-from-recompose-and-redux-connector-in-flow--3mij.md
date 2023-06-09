# 从流中的重组和冗余连接器进行适当的类型推理

> 原文：<https://dev.to/craftzdog/making-hoc-with-prop-type-inference-from-recompose-and-redux-connector-in-flow--3mij>

似乎 react-redux 的`connect`的类型注释与重新组合的`HOC`类型声明不兼容。我经常在`compose`函数中指定`connect`时遇到这样的错误:

```
const enhance: HOC<*, Props> = compose(
  connect(),
  pure, // <-- Flow error - Component: This type is incompatible with the expected param type of Component
  withHandlers({
    ...
  })
) 
```

Enter fullscreen mode Exit fullscreen mode

如果我从参数中删除`connect()`，流量误差消失。啊？但是包含这些代码的应用程序运行良好，所以我猜在流类型的定义中有一些错误。我不想为这个问题浪费时间。

所以我制作了简单的实用函数，使`connect`与`compose`函数兼容，而正确的类型推断在基本组件中工作。下面的代码是不带参数调用`connect`的`getDispatch`函数，所以它会简单地将`dispatch`添加到基础组件
的`props`

```
// @flow
import { type HOC } from 'recompose'
import { connect } from 'react-redux'
import type { Dispatch } from '../types'

type CHOC<E: {}> = HOC<{ ...$Exact<E>, dispatch: Dispatch }, E>

export default function getDispatch<Enhanced: {}>(): CHOC<Enhanced> {
  return (connect(): Function)
} 
```

Enter fullscreen mode Exit fullscreen mode

可以这么用:

```
const enhance: HOC<*, Props> = compose(
  withDispatch(),
  pure,
  withHandlers({
    ...
  })
) 
```

Enter fullscreen mode Exit fullscreen mode

而你会得到`props.dispatch`。

当你想让商店映射到道具时，你可以使用下面的`connectStore`函数:

```
// @flow
import { type HOC } from 'recompose'
import { connect } from 'react-redux'
import type { Dispatch, State } from '../types'

type F<M> = (state: State) => M
type CHOC<E: {}, M> = HOC<{ ...$Exact<E>, dispatch: Dispatch, ...M }, E>

export default function connectStore<Enhanced: {}, M: *>(
  mapper: F<M>
): CHOC<Enhanced, M> {
  return (connect(mapper): Function)
} 
```

Enter fullscreen mode Exit fullscreen mode

它强制将连接器函数的类型转换为重新组合的`HOC`，因此它将毫无问题地工作:

```
const enhance: HOC<*, Props> = compose(
  connect(({ editingNote }) => ({ editingNote })),
  pure,
  withHandlers({
    ...
  })
)
const EnhancedComponent = enhance(props => {
  console.log(props.editingNote) // <-- type inference works!
}) 
```

Enter fullscreen mode Exit fullscreen mode

很明显，这只是一个变通办法，甚至可能在未来崩溃，但它简化了我的代码库，目前工作良好。流程中的类型推理非常强大，但是类型注释往往非常复杂。这让我想起了 C/C++中的宏地狱🙄