# 使用 React Native 中的 Flexbox 将元素放置在屏幕底部

> 原文：<https://dev.to/onmyway133/position-element-at-the-bottom-of-the-screen-using-flexbox-in-react-native-4387>

[![](img/0878aa587a9f5385cda1ad5df11aa9fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OKxqqRvr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AI6Shwpq7vceXFIBo.jpg)

React Native 使用 [Yoga](https://github.com/facebook/yoga) 来实现 [Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox) 风格的布局，这有助于我们以一种声明式的简单方式设置布局。

> 柔性盒模块，通常称为 flexbox，被设计为一维布局模型，并作为一种方法，可以在界面中的项目之间提供空间分布和强大的对齐功能

作为一个在 iOS 中使用自动布局和在 Android 中使用约束布局的人，我有时发现在 React Native 中使用 Flexbox 很困难。其中之一是如何将某个元素定位在屏幕的顶部或底部。这是一个元素在容器中不遵循规则的情况。

## 传统布局

考虑这个传统的欢迎屏幕，其中有一些文本和一个登录按钮。

[![](img/24c02240813f391a94104cf9fcc48220.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mkNVd66J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AmYxO00FDyT91dNaFHeCvIA.png)

这很容易实现

```
import React from 'react'
import { View, StyleSheet, Image, Text, Button } from 'react-native'
import strings from 'res/strings'
import palette from 'res/palette'
import images from 'res/images'
import ImageButton from 'library/components/ImageButton'

export default class Welcome extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Image
          style={styles.image}
          source={images.placeholder} />
        <Text style={styles.heading}>{strings.onboarding.welcome.heading.toUpperCase()}</Text>
        <Text style={styles.text}>{strings.onboarding.welcome.text1}</Text>
        <Text style={styles.text}>{strings.onboarding.welcome.text2}</Text>
        <ImageButton
          style={styles.button}
          title={strings.onboarding.welcome.button} />
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center'
  },
  image: {
    marginTop: 50
  },
  heading: {
    ...palette.heading, ...{
      marginTop: 40
    }
  },
  text: {
    ...palette.text, ...{
      marginHorizontal: 8,
      marginVertical: 10
    }
  }
}) 
```

注意款式。与 web 不同，React Native 中的 Flexbox 默认主轴是垂直的，因此元素从上到下布局。alignItems 确保所有元素在水平轴上居中，根据 Flexbox 术语，水平轴是横轴。

## 底部位置按钮

根据设计，按钮应该位于屏幕的底部。一个黑暗的想法可能会建议我们使用位置:“绝对”，类似于

```
button: {
  position: 'absolute',
  bottom:0
} 
```

它的变通办法可能有效，但这就像选择退出 Flexbox。我们喜欢 Flexbox，我们喜欢拥抱它。解决方案是为按钮添加一个容器，并在里面使用 flex-end，以便按钮移动到底部。

让我们添加一个容器

```
<View style={styles.bottom}>
  <ImageButton
    style={styles.button}
    title={strings.onboarding.welcome.button} />
</View> 
```

和样式

```
bottom: {
  flex: 1,
  justifyContent: 'flex-end',
  marginBottom: 36
} 
```

柔性告诉底部视图占用剩余的空间。在这个空间内，底部是从底部展开的，这就是柔性端的意思。

这是结果的样子

[![](img/ac5b8e01f4df35e58f4a12e690b89c1a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--48F3Qqcj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AJ2SIgsqzREyRz-GSsgoKIA.png)

这是完整的代码

```
import React from 'react'
import { View, StyleSheet, Image, Text, Button } from 'react-native'
import strings from 'res/strings'
import palette from 'res/palette'
import images from 'res/images'
import ImageButton from 'library/components/ImageButton'

export default class Welcome extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Image
          style={styles.image}
          source={images.placeholder} />
        <Text style={styles.heading}>{strings.onboarding.welcome.heading.toUpperCase()}</Text>
        <Text style={styles.text}>{strings.onboarding.welcome.text1}</Text>
        <Text style={styles.text}>{strings.onboarding.welcome.text2}</Text>
        <View style={styles.bottom}>
          <ImageButton
            style={styles.button}
            title={strings.onboarding.welcome.button} />
        </View>
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center'
  },
  image: {
    marginTop: 50
  },
  heading: {
    ...palette.heading, ...{
      marginTop: 40
    }
  },
  text: {
    ...palette.text, ...{
      marginHorizontal: 8,
      marginVertical: 10
    }
  },
  bottom: {
    flex: 1,
    justifyContent: 'flex-end',
    marginBottom: 36
  }
}) 
```

## 什么是 flex: 1

根据[flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox)的基本概念

> **flex** [CSS](https://developer.mozilla.org/en-US/docs/CSS) 属性指定一个 flex 项目将如何增长或收缩，以适应其 flex 容器中的可用空间。这是一个[简写](https://developer.mozilla.org/en-US/docs/Web/CSS/Shorthand_properties)属性，设置[伸缩](https://developer.mozilla.org/en-US/docs/Web/CSS/flex-grow)、[伸缩](https://developer.mozilla.org/en-US/docs/Web/CSS/flex-shrink)和[伸缩](https://developer.mozilla.org/en-US/docs/Web/CSS/flex-basis)。

和 [w3](https://www.w3.org/TR/css-flexbox-1/#flex-common)

> [伸缩:](https://www.w3.org/TR/css-flexbox-1/#propdef-flex)相当于[伸缩:1 0](https://www.w3.org/TR/css-flexbox-1/#propdef-flex) 。使 flex 项具有灵活性，并将 [flex basis](https://www.w3.org/TR/css-flexbox-1/#flex-flex-basis) 设置为零，使项在 flex 容器中获得指定比例的可用空间。如果 flex 容器中的所有项目都使用这种模式，它们的大小将与指定的伸缩因子成比例。

在大多数浏览器中，flex: 1 等于 1 1 0，这意味着 flex-grow: 1，flex-shrink:1，flex-basis: 0。flex-grow 和 flex-shrink 指定项目相对于同一容器中的其他灵活项目将增长或收缩多少。柔性基础指定了柔性项目的初始长度。在这种情况下，底部视图将占用剩余的空间。在那个空间里，我们可以随心所欲地流动。为了将按钮移动到底部，我们使用 justifyContent 在主轴中布局项目，使用 flex-end 在容器的末端对齐 flex 项目。

## 构图法

虽然这样做是可行的，但是代码可以被快速复制，因为我们需要在很多屏幕上这样做。我们所需要的就是将这个 ImageButton 包装在一个容器中。让我们用一个效用函数来封装它。添加此 utils/moveToBottom.js

```
import React from 'react'
import { View, StyleSheet } from 'react-native'

function moveToBottom(component) {
  return (
    <View style={styles.container}>
      {component}
    </View>
  )
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'flex-end',
    marginBottom: 36
  }
})

export default moveToBottom 
```

现在在我们的屏幕中，我们只需要导入

```
import moveToBottom from 'library/utils/moveToBottom' 
```

包好我们的纽扣

```
{
  moveToBottom(
    <ImageButton
      style={styles.button}
      title={strings.onboarding.welcome.button}
      onPress={() => {
        this.props.navigation.navigate('Term')
      }} />
  )
} 
```

这一次，我们有和以前一样的屏幕，但是有更多可重用的代码。由于样式在我们的 moveToBottom 模块中，我们不再需要在屏幕中指定样式。这是完整的代码

```
import React from 'react'
import { View, StyleSheet, Image, Text, Button } from 'react-native'
import strings from 'res/strings'
import palette from 'res/palette'
import images from 'res/images'
import ImageButton from 'library/components/ImageButton'
import moveToBottom from 'library/utils/moveToBottom'

export default class Welcome extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Image
          style={styles.logo}
          source={images.logo} />
        <Image
          style={styles.image}
          source={images.placeholder} />
        <Text style={styles.heading}>{strings.onboarding.welcome.heading.toUpperCase()}</Text>
        <Text style={styles.text}>{strings.onboarding.welcome.text1}</Text>
        <Text style={styles.text}>{strings.onboarding.welcome.text2}</Text>
        {
          moveToBottom(
            <ImageButton
              style={styles.button}
              title={strings.onboarding.welcome.button}
              onPress={() => {
                this.props.navigation.navigate('Term')
              }} />
          )
        }
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center'
  },
  logo: {
    marginTop: 70,
    marginBottom: 42,
  },
  image: {

},
  heading: {
    ...palette.heading, ...{
      marginTop: 40
    }
  },
  text: {
    ...palette.text, ...{
      marginHorizontal: 8,
      marginVertical: 10
    }
  }
}) 
```

## 如何将组件作为参数传递

我不得不承认，我最初实现 moveToBottom 时使用了 Component(我们需要大写，因为 React 习惯于使用组件的首字母大写)来将组件嵌入到视图中

```
function moveToBottom(Component) {
  return (
    <View style={styles.container}>
      <Component />
    </View>
  )
} 
```

但是这会导致捆绑错误

```
ExceptionsManager.js:84 Warning: React.createElement: type is invalid -- expected a string (for built-in components) or a class/function (for composite components) but got: <ImageButton />. Did you accidentally export a JSX literal instead of a component? 
```

和

```
ExceptionsManager.js:76 Invariant Violation: Invariant Violation: Invariant Violation: Element type is invalid: expected a string (for built-in components) or a class/function (for composite components) but got: object. 
```

此时我释放了我传入的东西实际上是一个对象，而不是一个类，所以我把它当作一个对象，它就工作了

```
function moveToBottom(component) {
  return (
    <View style={styles.container}>
      {component}
    </View>
  )
} 
```

## Android 上的 marginBottom

在上面的 moveToBottom 函数中，我使用 marginBottom 从底部留一些边距。这在 iOS 上有效，但不知何故在 Android 上没有任何效果，我目前使用的是 react-native 0.57.0。这种不一致在 React 本地开发中经常发生。一个快速的解决方法是执行平台检查，我们可以在 src/library/utils/check 中把它变成一个漂亮的函数

```
import { Platform } from 'react-native'

const check = {
  isAndroid: () => {
    return Platform.OS === 'android'
  }
}

export default check 
```

然后在 moveToBottom 中，让我们使用 paddingBottom，以防应用程序在 Android 中运行

```
const styles = StyleSheet.create({

  container: {

    flex: 1,

    justifyContent: 'flex-end',

    paddingBottom: check.isAndroid ? 14 : 0

  }

}) 
```

## 
  
何去何从

在这篇文章中，我们从绝对位置到另一个容器，了解 flex，如何添加可重用函数，以及如何正确地将组件作为参数传递。希望你觉得有用。您也可以使用脸书 SDK 查看这篇帖子 [React Native Login，其中我展示了 React Native 开发的更多技巧和推荐链接，以了解 Flexbox。](https://medium.com/react-native-training/facebook-sdk-with-login-for-react-native-apps-in-2018-196601804f66)

原帖[https://medium . com/react-native-training/position-element-at-the-bottom-the-screen-using-flexbox-in-react-native-a00b 3790 ca 42](https://medium.com/react-native-training/position-element-at-the-bottom-of-the-screen-using-flexbox-in-react-native-a00b3790ca42)