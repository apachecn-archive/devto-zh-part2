# 要将 MobX 的可观察数组传递给 FlatList，。需要切片()

> 原文：<https://dev.to/acro5piano/to-pass-mobxs-observable-array-to-flatlist-slice-is-needed-2b45>

一起使用 MobX 和 React Native 时出现问题。

# 问题

我在将 MobX 的`ObservableArray`传递给`FlatList`时遇到了警告。

```
[mobx.array]
Attempt to read an array index(2) that is out of bounds (2).
Please check length first. Out of bound indices will not be tracked by MobX 
```

Enter fullscreen mode Exit fullscreen mode

这可能是因为 FlatList 接收数组和呈现列表。
对于 FlatList，确切的属性类型数组是意外的。

MobX 的 ObservableArray 是这样定义的:

```
@observable users: Array<User> = [] 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
users: Array<User> = observable([]) 
```

Enter fullscreen mode Exit fullscreen mode

`ObservableArray`不是`Array`，但是 ObservableArray 表现得像本机数组，所以我很困惑。

`ListView`，`SectionList`也将`ObservableArray`视为不合适的道具。

# 解

我们可以通过`.toJS`或`.slice()`将`ObservableArray`转换成`Array`。

```
// @flow

import React from 'react'
import { FlatList, Text } from 'react-native'
import { observable } from 'mobx'
import { userApi } from 'app/api'

type User = {
  id: number,
  name: string
}

class SomeComponent extends React.Component<{}> {
  @observable users: Array<User>

  componentDidMount() {
    userApi.get().then(users => { this.users = users })
  }

  render() {
    <FlatList
      keyExtractor={(user) => String(user.id)}
      renderItem={({ item }) => <Text>{item.name}</Text> }
      // data={this.users} create Warning
      data={this.users.slice()}
    />
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 其他情况

`ObservableArray`和`render()`产生反应性问题。
所以如果没有警告，用`.slice()`传递状态是防止意外错误的第二个好方法。

特别是，当我们添加一个元素到空的`ObservableArray`，或者改变数组中一个对象的属性时，会发生反应性问题。

[https://stack overflow . com/questions/44278526/react-native-flat list-not-re rendering-row-when-props-change](https://stackoverflow.com/questions/44278526/react-native-flatlist-not-rerendering-row-when-props-change)

有时候没必要`.slice()`。
`.slice()`导致性能问题，所以我们不应该滥用它。
但是，在我看来，反应性问题很难解决，因此性能损失在某种程度上是可以接受的。

为了防止进一步的问题，我编写了 MobX Store 的单元测试。

TODO:研究并理解渲染问题何时发生，何时不发生

# 参考文献:

*   [https://github.com/facebook/react-native/issues/15167](https://github.com/facebook/react-native/issues/15167)
*   [https://mobx.js.org/best/pitfalls.html](https://mobx.js.org/best/pitfalls.html)