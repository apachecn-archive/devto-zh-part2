# 用 react，redux，material-ui 等设置 Google Analytics。

> 原文：<https://dev.to/maruware/setting-google-analytics-with-react-redux-material-ui-etc--40lg>

# 安装

```
yarn add react-ga 
```

# 代码

### 初始化

```
import ReactGA from 'react-ga'
ReactGA.initialize('UA-000000-01') 
```

### 绑定 react-路由器

```
export const history = createHistory()
history.listen((location, action) => {
  ReactGA.set({ page: location.pathname })
  ReactGA.pageview(location.pathname)
}) 
```

### 记录素材-ui/Popover

```
import React from 'react'
import ReactGA from 'react-ga'
import Popover from 'material-ui/Popover'

export default ({name, ...rest}) => {
  return (
    <Popover
      onEntered={() => ReactGA.modalview(name)}
      {...rest}
    />
  )
} 
```

### 记录素材-ui/对话框

```
import React from 'react'
import ReactGA from 'react-ga'
import Dialog from 'material-ui/Dialog'

export default ({name, ...rest}) => {
  return (
    <Dialog
      onEntered={() => ReactGA.modalview(name)}
      {...rest}
    />
  )
} 
```