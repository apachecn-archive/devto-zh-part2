# 本地反应:根据平台编写代码

> 原文：<https://dev.to/damcosset/react-native-write-code-depending-on-the-plateforms-4mha>

## 简介

React Native 允许您使用 Javascript 和 React 语法为 iOS 和 Android 平台编写代码。按照这种逻辑，您会希望在两个平台上尽可能多地重用代码。然而，在某一点上，你将不得不为一个特定的平台写代码。在本文中，我们将看到 React native 如何实现这一点。

## *平台*模块

首先，*平台*模块。可以像这样导入它:

`import {Platform} from 'react-native'`

使用这个，我们现在可以知道我们的`Platform.OS`在哪个平台上。

```
<Text>{Platform.OS == 'ios' ? 'Hello IOS' : 'Hello Android'}</Text> 
```

*平台。OS* 会返回‘IOs’或者‘Android’。您也可以使用选择方法:

```
<Text style={{
    color: 'blue',
    ...Platform.select({
        ios: {
            backgroundColor: 'red'
        },
        android: {
            backgroundColor: 'green'
        }
    })
}}>
    Hello World
</Text> 
```

我们的文本在两个平台上都是蓝色的，但背景色在 iOS 设备上是红色，在 Android 设备上是绿色。

*select* 方法返回任意值。你可以用 React 给它任何有效的东西:

```
const MyComponent = Platform.select({
    ios: () => <Text>Hello iOS</Text>,
    android: () => <Text>Hello Android</Text> })

<MyComponent /> 
```

### 获取具体版本

可以用*平台。版本*检测你当前运行的 iOS 或 Android 版本。

```
// On Android
const version = Platform.Version // 25 = 'Nougat'

// On iOS
const version = Platform.Version // 11.2.2 
```

## 使用平台特定的扩展

在某种程度上，平台模块可能不足以编写您需要的东西，您可能需要为 iOS 和 Android 创建完全不同的组件。React Native 能够检测到*。ios* 或*。android* 文件扩展名并加载正确的组件。

可以有这样的文件:

```
 TopBar.ios.js
TopBar.android.js 
```

并像这样导入它们:

```
import Topbar from './TopBar' 
```

得益于文件扩展名，React Native 将知道采用哪个组件。

玩得开心！！！