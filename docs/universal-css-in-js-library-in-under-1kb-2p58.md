# 1kb 以下的通用下一代 css-in-js 库

> 原文：<https://dev.to/sadick/universal-css-in-js-library-in-under-1kb-2p58>

# 介绍`scoped-style`

Scoped style 旨在成为一个通用库，您可以使用它来设计组件的样式，同时仍然保持较小的内存占用。

它目前已经过 react、preact 和 hyperapp 的测试。您可以像以前一样使用 css 的全部功能。

```
yarn add scoped-style 
```

Enter fullscreen mode Exit fullscreen mode

## 例子

```
import scoped from "scoped-style"

// for react
import React from "react"
const styled = scoped(React.createElement)
//

// for Preact
import { h } from "preact"
const styled = scoped(h)
//

// for Hyperapp
import { h } from "hyperapp"
const styled = scoped(h)
//

const Button = styled("button")`
  background: ${props => props.primary ? "orange": "gray"};
  border: none;
  border-radius: 2px;
  :hover {
    padding: 10px;
  }
`

const App = () => (
  <div>
    <Button primary>Login</Button>
  </div>
)

// Your rendering code 
```

Enter fullscreen mode Exit fullscreen mode

该项目位于 github 上。非常欢迎投稿。