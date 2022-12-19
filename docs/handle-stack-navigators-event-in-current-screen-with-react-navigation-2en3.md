# 使用 React 导航处理当前屏幕中的堆栈导航器事件

> 原文：<https://dev.to/acro5piano/handle-stack-navigators-event-in-current-screen-with-react-navigation-2en3>

# 概述

当我们使用 React Native + React 导航创建应用程序时，我们通常希望在导航标题中放置类似“保存”的按钮。

[![image.png](img/a43f10a96e72cd1d6b605cc3744180bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--resla3Rp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/103885/09a71c59-9726-cf05-1491-d62f9e4da27a.png)

在这种情况下，我们可能希望在屏幕组件中处理 press 事件，而不是在导航操作中。这是因为 React 导航应该专注于处理导航，所以屏幕上的事件和逻辑应该由屏幕组件来处理。

官方文件说定义导航动作和设置路线参数是一个好方法，但如果我们这样做，显然会导致代码混乱。

[https://react navigation . org/docs/navigators/navigation-actions](https://reactnavigation.org/docs/navigators/navigation-actions)

我们将数据封装在屏幕组件中，我们希望将逻辑放入其中。

有些人也有同样的想法:

[https://github . com/react-navigation/react-navigation/issues/145](https://github.com/react-navigation/react-navigation/issues/145)

> 很多时候，如果你正在编辑或创建一些东西，其中一个标题按钮会是“保存”。我看不出这个库是如何可行的，因为不能访问当前屏幕组件的状态或属性。唯一(丑陋的)解决方案是将数据保存在组件之外。任何人有任何建议如何用这个库实现这个模式？

# 解

在上面的问题中，我发现下面的代码运行良好。

```
import React from 'react'
import {
  View,
  Button,
} from 'react-native'

class MyScreen extends React.Component {
  static navigationOptions = ({ navigation }) => {
    const { state } = navigation
    return {
      headerTitle: 'New Task',
      headerRight: <Button title="Save" onPress={() => state.params.handleSave()} />,
    }
  }

  componentDidMount() {
    this.props.navigation.setParams({ handleSave: () => this.saveDetails() })
  }

  saveDetails() {
    alert('saved')
  }

  render() {
    return (
      <View />
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用 Flow，我们可以注释类型`NavigationNavigator`。

```
import { NavigationNavigator } from 'react-navigation'

  static navigationOptions = ({ navigation }: NavigationNavigator) => {
    return {
      headerTitle: navigation.state.params.intern.title
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

# 描述

在正式文档中，我们在创建`StackNavigator`、
的实例时，需要定义`NavigationOptions`

```
import { StackNavigator } from 'react-navigation'
import HomeScreen from './components/HomeScreen'
import NewScreen from './components/NewScreen'

const navigator = StackNavigator({
  Home: {
    screen: HomeScreen,
    navigationOptions: ({ navigation }) => {
      const { navigate } = navigation
      return {
        headerTitle: 'Home',
        headerRight: <Button title="New" onPress={() => navigate('NewScreen')} />,
      }
    },
  }, 
```

Enter fullscreen mode Exit fullscreen mode

其实我们也可以在 screen 组件中定义`static navigationOptions: NavigationNavigator => void`。

```
import React from 'react'
import { Button } from 'react-native'

class HomeScreen extends React.Component {
  static navigationOptions = ({ navigation }) => {
    const { state } = navigation
    return {
      headerTitle: 'New Task',
      headerRight: <Button title="Save" onPress={() => state.params.handleSave()} />,
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`navigationOptions`是一个静态函数，所以我们应该把动作传递给导航状态。

缺点是我们应该编写几乎无用的代码来定义导航动作。

# 结论

如果您遇到了 React Navigation 的导航标题问题，您应该尝试创建自己的标题。它的导航标题很容易使用，但随着应用程序的增长，很难定制标题。

在我的项目中，React 导航一直是我关注的问题。
不要高度依赖 React 导航，用自己的逻辑就能做好。