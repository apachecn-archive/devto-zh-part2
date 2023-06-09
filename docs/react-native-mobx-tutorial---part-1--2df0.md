# React 原生 Mobx 教程-第 1 部分

> 原文：<https://dev.to/satansdeer/react-native-mobx-tutorial---part-1--2df0>

**Mobx** 是一种状态管理解决方案，正在迅速流行起来。我将向你展示
如何使用 **Mobx** 创建一个简单的 **React-Native** 应用。

我强烈建议在原创网站上查看这篇文章，因为我在那里有互动的例子:[maksimivanov.com](http://maksimivanov.com/posts/react-native-mobx-tutorial)

## 我们要建造什么

我在一些交易所有一些加密硬币。我不是在交易什么的，我只是买了一些我喜欢的代币，现在只是拿着它们偶尔买卖。

在[币安](https://www.binance.com/?ref=12930619)上，你可以看到你有多少硬币，以及它们在比特币甚至美元中的价值，但你看不出自上次检查以来有什么不同。

我们将为它建立投资组合跟踪器。第一次运行时，我们将保存所有硬币的价格，然后我们将显示差异。

它看起来有点像这样:

[![binance tracker](img/410fdf9fa05d2742e87cf633aae93959.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WXx2Viba--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://d33wubrfki0l68.cloudfront.net/27b4ee441afcfd68fb2337b5148e7410e027c89f/2225b/asseimg/binance_tracker.png)

我们将学习如何在本地存储数据，使用 flatlist，navigator 和多屏幕应用程序，还将学习如何使用 mobx 管理状态。

## 目录

1.  [什么是 MobX](#)
2.  [制作反应型 app](http://maksimivanov.com/posts/react-native-mobx-tutorial-part-2)
3.  [测试商店](#)
4.  [用酶测试视图](#)

## 为什么是 Mobx？

MobX 通常比 Redux 更快，需要的样板文件更少，而且一般来说新手更容易掌握。

Mobx 的核心概念很少:

*   [可观测量](#observables)
*   [计算值](#computed_values)
*   [反应](#reactions)
*   [动作](#actions)

### 可观测量

Observables 允许您订阅它们的更改。您可以用`@observable` decorator 注释您的类属性，然后用`observers`跟踪它们的值。所以每次数值都会改变——`observers`也会相应更新。

这是一个简单的例子。这是一个滚动骰子——每次你按下**滚动**按钮——它重新滚动骰子并显示从 1 到 6 的结果数字。

```
import React, { Component } from 'react'
import ReactDOM from 'react-dom';
import { observable } from 'mobx';
import { observer } from 'mobx-react';

@observer class Dice extends Component {
  @observable value = 1;

  render() {
    return (
      <div style={this.styles.container}>
        <div style={this.styles.result}>Result: {this.value}</div>
        <button onClick={this.handleRoll}>ROLL</button>
      </div>
    )
  }

  handleRoll = () => {
    this.value = Math.floor(Math.random()*6) + 1;
  }

  styles = {
    container: {
      padding: '16px 0px'
    },
    result: {
      fontSize: 22,
      marginBottom: 10
    }
  }
}
ReactDOM.render(<Dice />, document.getElementById('dice')); 
```

Enter fullscreen mode Exit fullscreen mode

[这是原始网站上的交互示例](http://maksimivanov.com/posts/react-native-mobx-tutorial)

### 计算值

计算值可以从可观察值中导出，当后者发生变化时，计算值将自动更新。请记住，为了更新，必须遵守`computed`。

```
@computed get computedValue() {
  return this.value > 3 ? 'WIN' : 'LOOSE'
}

render() {
  return (
    <div style={this.styles.container}>
      <div style={this.styles.result}>Result: {this.value}</div>
      <div style={this.styles.result}>Computed Result: {this.computedValue}</div>
      <button onClick={this.handleRoll}>ROLL</button>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

[这是原始网站上的交互示例](http://maksimivanov.com/posts/react-native-mobx-tutorial)

### 反应

反应与计算值非常相似，但它们不是返回新值，而是用来产生副作用(发出网络请求、修补 dom 等)

Mobx 提供了三种反应功能【T1 时的 T0】、[自动运行](https://mobx.js.org/refguide/autorun.html)和[反应](https://mobx.js.org/refguide/reaction.html)

*   `when`接受两个函数:谓词和效果。它运行并观察谓词，直到它返回`true`，然后运行`effect`函数。它处置后，停止对观察到的性质作出反应。
*   `autorun`用于需要反应函数的情况，该函数将在每次观察值更新时触发。不像`computed`不需要自己去观察。
*   `reaction`类似于`autorun`,但是让您可以更好地控制要观察的属性。它接受两个函数`data-function`和`side-effect-function`。`data-function`被跟踪并返回用于`side-effect-function`的数据。

这里有一个`autorun`的例子:

```
constructor() {
  super();
  autorun(
    ()=> (this.value > 3) && alert('WIN')
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

[这是原始网站上的交互示例](http://maksimivanov.com/posts/react-native-mobx-tutorial)

### 行动

动作是改变状态的任何东西。所以您可以使用它们用`@action` decorator 显式地标记函数。

这个装饰器将函数包装成`transaction`、`untracked`和`allowStateChanges`。

*   `transaction`用于批量状态更新，因此在该功能完成之前不会通知任何观察者。因此您可以一次更新多个属性。
*   允许你在没有建立观察者的情况下运行代码(就像反应，或者不像计算)
*   `allowStateChanges`用于允许/禁止某些功能的状态改变。默认情况下允许`action`进行更改(不允许`computed`和`observer`)。

## 观察者

严格来说`observers`不是 mobx 核心的一部分。它们由`mobx-react`包提供。但是无论如何…

它们是用来让你的观点“观察”`observables`和重新渲染变化的。

但是我将在下一部分中介绍它。