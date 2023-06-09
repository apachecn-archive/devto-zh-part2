# 简化反应状态和样式组件

> 原文：<https://dev.to/independnt/simplifying-react-state-and-styled-components-1om0>

我正在学习如何创建一个加密货币仪表板，这对于学习 React 组件组织的新技巧以及如何以一种有组织、高效的方式构建代码非常有用。主要是，如何处理状态，以及如何重用将在整个编码过程中使用的常见样式。

应用程序想要做的一件事是检查这是否是访问者第一次访问应用程序，这是通过将状态对象设置为“first visit”来实现的。我们目前的状态是:

```
this.state = {
      page: "settings",
      ...checkFirstVisit()
    } 
```

您可以看到，我目前没有“firstVisit”属性，只有一个 checkFirstVisit 函数，前缀为...传播运算符。这是因为使用 checkFirstVisit，我们希望更新多个状态属性。' checkFirstVisit '是这样实现的:

```
const checkFirstVisit = () => {
  let cryptoDashData = localStorage.getItem('cryptoDash')
  if(!cryptoDashData){
    return {
      firstVisit: true,
      page: 'settings'
    }
  }
  return {}
} 
```

这里我们检查一个 localStorage 项，它表明我们以前来过这里，如果它存在，那么 checkFirstVisit 函数返回一个空对象，保持状态不变。但是，如果它不存在，我们返回两个属性来更新我们的状态，firstVisit 设置为 true，page 属性设置为' settings ',指示我们应该到达哪个页面。所以现在，第一次访问的访问者总是会被定向到设置，我们所要做的就是确保每次加载状态时，我们运行 checkFirstVisit，这是通过将它包含在状态中来完成的。我一直试图在我的代码中更多地实现 spread 操作符，以及类似这样的函数，以便根据某些需求设置状态。

我也一直试图通过不重复自己的话来让我的代码更友好，尤其是在设计部门。在这个应用程序中，我有几个想要重用的样式，我没有使用样式化的组件，也没有在每次需要颜色或阴影时重新编写，而是添加了一个 Style.js，其中包含了我要重用的样式的几个导出。

```
export const fontColorGreen = `color: #03A9F4`;
export const lightBlueBackground = `background-color: #061a44`;
export const subtleBoxShadow = `box-shadow: 0px 0px 5px 1px #121d5b`;
export const greenBoxShadow = `box-shadow: 0px 0px 4px 2px #5fff17`;
export const redBoxShadow = `box-shadow: 0px 0px 2px 2px #e41111`; 
```

现在可以将这些样式导入到所需的组件中，如下所示:

```
import {subtleBoxShadow, lightBlueBackground, greenBoxShadow} from './Style'; 
```

一旦我创建了一个样式化的组件，我就可以将它们实现到代码中，这样来样式化所述组件:

```
const CoinTile = styled.div ` ${subtleBoxShadow}  ${lightBlueBackground} padding: 10px;
  &:hover{
      cursor: pointer; ${greenBoxShadow} }
` 
```

现在，每当我需要这些样式时，我只需在我的任何样式组件中使用${}调用它们，我需要做的就是从我的 style.js 文件夹中导入它们。这使得我的代码更有条理，更容易编辑和调试。就这些了，再见！