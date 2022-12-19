# ES7 相对于 ES6 有哪些优势(侧重 React)？

> 原文：<https://dev.to/madhu/what-are-the-advantages-of-es7-over-es6-18bh>

我知道在 ES7 中，我们可以在构造函数之外声明状态变量，也可以将 propTypes 声明为静态属性，在组件代码中尽可能地声明。

```
import React, { Component } from 'react'
import { string, object } from 'prop-types'

export default class ProfileContainer extends Component {
  state = { expanded: false }

  static propTypes = {
    model: object.isRequired,
    title: string
  }

  static defaultProps = {
    model: {
      id: 0
    },
    title: 'Your Name'
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

谁能解释一下这样做的好处是什么，我也想知道 ES7 的新功能，一个门外汉的解释。