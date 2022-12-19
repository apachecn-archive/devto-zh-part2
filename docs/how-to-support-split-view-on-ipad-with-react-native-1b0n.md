# 如何使用 React Native 在 iPad 上支持拆分视图

> 原文：<https://dev.to/craftzdog/how-to-support-split-view-on-ipad-with-react-native-1b0n>

我正在开发我的应用程序来支持平板电脑。在 iPad 上，它有一个多任务功能，允许您通过如下方式分割屏幕来同时使用两个应用程序:

[![split view demo](../Images/a27d83d09c5e4a3ad6b11ee05f73693f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qsH6IcMP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mpska7n7rd7odxqmir2a.png)

在 React Native 中，它需要一些黑客来支持这个功能，因为有一个问题是`Dimensions` [不支持它](https://github.com/facebook/react-native/issues/16152)。
你总是从`Dimensions.get`得到相同的数据，即使应用程序在 iPad 上处于“分割视图”或“滑过”状态:

```
console.log(Dimensions.get('screen')) // {fontScale: 1, width: 768, height: 1024, scale: 2}
console.log(Dimensions.get('window')) // {fontScale: 1, width: 768, height: 1024, scale: 2} 
```

所以你需要以某种方式得到一个实际的*窗口*大小。
要做到这一点，你必须有一个观点，一个具有`flex: 1`风格的观点的最外层。
并设置`onLayout`事件获取其大小，并在 Redux store 之类的地方记忆。

## 适应性强的布局提供商/消费者

下面是一些代码片段，可以方便地支持您的应用程序上的拆分视图。它采用提供者-消费者模式，但不依赖于 React 的上下文 API，因为它将状态存储到 Redux store 中。

### 提供者

```
// @flow
// adaptable-layout-provider.js

import * as React from 'react'
import { View, StyleSheet } from 'react-native'
import { compose, withHandlers, pure, type HOC } from 'recompose'
import actions from '../actions'
import withDispatch from '../utils/with-dispatch'

/**
 * <View onLayout={...} />
 * <FlatList onLayout={...} /> (FlatList is just wrapper for View)
 *
 * @see https://facebook.github.io/react-native/docs/view.html#onlayout
 */
export type OnLayout = {|
  nativeEvent: {|
    layout: {|
      x: number,
      y: number,
      width: number,
      height: number
    |}
  |}
|}

type Props = {
  children: React.Node
}

const enhance: HOC<*, Props> = compose(
  withDispatch(),
  pure,
  withHandlers({
    emitDimensionChanges: props => (event: OnLayout) => {
      const { dispatch } = props
      const { width, height } = event.nativeEvent.layout

      dispatch(actions.viewport.update({ width, height }))
    }
  })
)

const Provider = enhance(props => (
  <View style={styles.container} onLayout={props.emitDimensionChanges}>
    {props.children}
  </View> ))

export default Provider

const styles = StyleSheet.create({
  container: {
    flex: 1
  }
}) 
```

### 消费

```
// @flow
// adaptable-layout-consumer.js

import * as React from 'react'
import { compose, pure, type HOC } from 'recompose'
import connect from '../utils/connect-store'

type Props = {
  renderOnWide?: React.Node,
  renderOnNarrow?: React.Node
}

const enhance: HOC<*, Props> = compose(
  connect(({ viewport }) => ({ viewport })),
  pure
)

const Consumer = enhance(props => {
  const { viewport } = props
  // may return nothing:
  // 1\. renderOnWide set but we have narrow layout
  // 2\. renderOnNarrow set but we have wide layout
  let children = null
  const wideLayout = viewport.isTablet

  if (wideLayout === true && props.renderOnWide) {
    children = props.renderOnWide
  } else if (wideLayout === false && props.renderOnNarrow) {
    children = props.renderOnNarrow
  }

  return children
})

export default Consumer 
```

### 减速器

```
// @flow
// reducers/viewport.js
import type { ViewportActionType } from '../actions/viewport'
import * as viewportActions from '../actions/viewport'
import { Dimensions } from 'react-native'

export type Dimension = {
  width: number,
  height: number
}

export type ViewportState = {
  width: number,
  height: number,
  isLandscape: boolean,
  isPortrait: boolean,
  isTablet: boolean,
  isPhone: boolean
}

function isLandscape(dim: Dimension) {
  return dim.width >= dim.height
}

function isTablet(dim: Dimension) {
  return dim.width >= 1024
}

const dim: Dimension = Dimensions.get('window')
export const initialViewportState: ViewportState = {
  width: dim.width,
  height: dim.height,
  isLandscape: isLandscape(dim),
  isPortrait: !isLandscape(dim),
  isTablet: isTablet(dim),
  isPhone: !isTablet(dim)
}

export default function viewport(
  state: ViewportState = initialViewportState,
  action: ViewportActionType
): ViewportState {
  switch (action.type) {
    case viewportActions.VIEWPORT_UPDATE:
      const dim = action.payload
      return {
        ...action.payload,
        isLandscape: isLandscape(dim),
        isPortrait: !isLandscape(dim),
        isTablet: isTablet(dim),
        isPhone: !isTablet(dim)
      }
    default:
      return state || initialViewportState
  }
} 
```

### 动作

```
// @flow
import { type Dimension } from '../reducers/viewport'
export const VIEWPORT_UPDATE = 'VIEWPORT_UPDATE'

export type ViewportActionType = {
  type: 'VIEWPORT_UPDATE',
  payload: Dimension
}

export function update(dim: Dimension) {
  return {
    type: VIEWPORT_UPDATE,
    payload: dim
  }
} 
```

在这个例子中，它将窗口大小存储到 Redux store 中。然而，你也可以把它存储在全局变量中，虽然我不推荐，但这很简单。

## 如何使用

在你的根视图组件:

```
const RootView = () => (
  <AdaptableLayoutProvider>
    <MainScreen />
  </AdaptableLayoutProvider> ) 
```

在您的屏幕组件:

```
const MainScreen = props => {
  return (
    <AdaptableLayoutConsumer
      renderOnNarrow={
        <MobileLayout />
      }
      renderOnWide={
        <ThreeColumnLayout />
      }
    />
  )
} 
```

希望有所帮助！

* * *

*   在 Twitter 上关注我
*   在媒体上阅读我的博客文章