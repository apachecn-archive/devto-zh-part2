# 使用 React(Native) + MobX + Flow，Flow 将@inject 视为错误

> 原文：<https://dev.to/acro5piano/using-reactnative--mobx--flow-flow-treats-inject-as-error-4mbo>

这是给不懂 ESNext Decorator 的 flow 讲的故事。

# 概述

检查 JS(X)静态类型的 Flow，不懂 es 提案的 Decorator(阶段 1，2018/04/11)。

首先，如果我们使用 Decorators(比如`@inject`)，Flow 不会通过。
我们应该将下面的配置添加到`.flowconfig`中，用 Decorators 执行流检查。

```
esproposal.decorators=ignore 
```

Enter fullscreen mode Exit fullscreen mode

然而，这让 flow 忽略了 Decorators，所以他不解释它们。
因此，如果我们使用`@inject`在 React 组件中注入 MobX 的存储，他声称错误。

要处理这个错误，唯一的选择就是不要使用 Decorators。

另一个可能的选择是`defaultProps`正如这篇文章所建议的，

[https://wie tse . loves . engineering/using-flow type-with-decorators-in-react-af 4 Fe 69 e 66d 6](https://wietse.loves.engineering/using-flowtype-with-decorators-in-react-af4fe69e66d6)

但是我认为我们应该避免这种黑客攻击，因为它增加了不受欢迎的类型，我们不能再相信`Props`。

# 先决条件

*   用 React(原生)+ MobX + Flow 开发
*   已安装`babel-plugin-transform-decorators-legacy`
*   不要使用`Component<*>`或`$flowFixMe`选择最简单的方式

# 问题

当`@inject`引起错误时，

```
// App.jsx

// @flow

import { React, Component } from 'react'
import SomeComponent from './SomeComponent'
import Stores from './stores'

const stores = new Stores()

class App extends Component<{}> {
  render() {
    return (
      <Provider stores={stores}>
        <ScrollView>
          <SomeComponent someProperty="Hello" />
        </ScrollView>
      </Provider>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// SomeComponent.jsx

// @flow

import { React, Component } from 'react'
import SomeStore from './stores/SomeStore'

type Props = {
  someStore: SomeStore<*>;
  someProperty: string;
}

@inject('someStore')
@observer
export default class SomeComponent extends Component<Props> {
  render() {
    const { someProperty, someValue } = this.props.someStore

    return ( 
      <Text>{someProperty}, {someValue}</Text>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们在控制台中运行`flow check`时，Flow 会创建以下错误:

```
Property `someStore` is incompatible:

# ...

property `someStore`. Property not found in. see `SomeComponents.js`

# ...

props of React element `SomeComponent` 
```

Enter fullscreen mode Exit fullscreen mode

让我描述一下心流的感觉。他忽略了装修工，所以他不了解装修工。
怜是个好人，所以他专注于他应该做的事情。

但是不管他的政策如何，我们都愿意使用装饰者。

# 解

真的很简单，好像我不用写这篇文章一样。

我们应该放弃使用`@inject`，直到 Flow 支持。相反，我们使用`inject`作为普通的 JavaScript 函数。
我认为这不会影响代码的可读性。

```
// SomeComponent.js (part)

@observer
class SomeComponent extends Component<Props> {
  render() {
    const { someProperty, someValue } = this.props.someStore

    return ( 
      <Text>{someProperty}, {someValue}</Text>
    )
  }
}

export default inject('someStore')(SomeComponent) 
```

Enter fullscreen mode Exit fullscreen mode

非常简单的解决方案，但对于 ES 初学者来说，需要花费很多时间来解决这个问题。

希望这篇文章对你有帮助。

# 参

[https://gist . github . com/voovak/29c 972 c 6 aa 9 efbb 7d 63 a 6853d 021 FBA 9](https://gist.github.com/vonovak/29c972c6aa9efbb7d63a6853d021fba9)