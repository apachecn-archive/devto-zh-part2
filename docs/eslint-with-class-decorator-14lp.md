# 带有类装饰器的 ESlint

> 原文：<https://dev.to/acro5piano/eslint-with-class-decorator-14lp>

当我创建一个函数返回一个附加了装饰的类时，ESLint 会导致一个错误。

我们可以这样写这个函数:

```
// RequireLogin.js(error)
// @flow

import React from 'react'

@inject('userStore')
@observer
export default (ComposedComponent: *) => class RequireLogin extends React.Component<Props> {
  render() {
    if (this.props.userStore.guest) return <div>LOGIN REQUIRED</div>
    else return <ComposedComponent />
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`babel-plugin-transform-decorators-legacy`可以按预期转换它，但是 ESLint 抛出语法错误。

要通过 ESLint，代码应该是这样的:

```
// RequireLogin.js(fixed)
// @flow

import React from 'react'

export default (ComposedComponent: *) => {
  @inject('userStore')
  @observer
  class RequireLogin extends React.Component<Props> {
    render() {
      if (this.props.userStore.guest) return <div>LOGIN REQUIRED</div>
      else return <ComposedComponent />
    }
  }

  return RequireLogin
} 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一句，这个 HOC 使用起来很简单:

```
// Home.js
// @flow

import React from 'react'
import RequireLogin from './RequireLogin'

@RequireLogin
export default class Home extends React.Component<Props> {
  render() {
    return <div>Hello</div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个 HOC 返回引用注入的 MobX 的存储。

*   当登录用户->组合组件时
*   当客人用户->登录组件时

ref:t0]https://github . com/yannickcr/ESL int 插件反应/问题/419