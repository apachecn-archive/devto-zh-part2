# 使用 React D3 在 React 中实现数据可视化

> 原文：<https://dev.to/bnevilleoneill/data-visualization-in-react-using-react-d3-1ki0>

[![](img/49c98edbd1c294d646471e7444789b7c.png)T2】](http://www.reactd3.org/)

数据可视化是指使用图表、图形等可视化对象以更清晰、更高效的方式交流数据的技术。

在 web 上，有许多可以用来可视化数据的库，但是最突出的是 D3js 库。它已经成为数据可视化的事实，并赢得了许多开发人员的信任。

React 是一个被许多开发者使用的库。在本文中，我们将看到如何使用 React 和 D3 来创建既可重用又美观的组件。

首先，让我们简单地检查一下这些库。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

#### 做出反应

React 是一个用于构建用户界面的 JavaScript 库。它使得创建可重用的组件变得容易，这些组件可以放在一起形成更复杂的组件。

这些组件能够自己保持它们的状态。

#### D3

D3.js 是一个基于数据操作文档的 JavaScript 库。它使用 HTML、CSS 和 SVG 来赋予数据生命。

D3 试图提供一种基于数据的高效数据操作方法，而不是提供框架来实现每一个可能的特性。

它速度快，支持大型数据集和动态行为的动画和互动。

让我们看看如何一起使用这两个库来创建动态数据可视化组件。

#### 设置反应

设置 react 最简单的方法是使用 react 团队的 create-react-app 样板文件。

要在本地机器上全局安装它以便重用，请在您的终端上运行这个命令:

```
npm install -g create-react-app 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们使用 create-react-app 模板创建一个新的应用:

```
create-react-app react-d3 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将目录更改为新创建的项目:

```
cd react-d3 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置 D3

你可以使用 CDN 或者通过 NPM 安装将 D3 库添加到你的应用中。

在这种情况下，我们将使用 NPM:
进行安装

```
npm install d3 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经准备好开始使用 D3 在 React 中进行数据可视化。

要在默认浏览器上预览刚刚创建的应用程序，运行下面的代码:

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

#### 用 D3 构建条形图

用您喜欢的文本编辑器打开创建的项目，并导航到 src/App.js。

这是当前在浏览器中呈现的组件。我们需要删除 render()方法的内容，以便用我们自己的内容替换它。

在 src 文件夹中，新建一个 js 文件，命名为 ***BarChart.js*** 。我们将在这里构建将要呈现的条形图。

首先，将以下代码添加到文件中:

```
import React, {Component} from 'react';
import * as d3 from "d3";

class BarChart extends Component {

}

export default BarChart; 
```

Enter fullscreen mode Exit fullscreen mode

当条形图组件已经安装在 DOM 中时，我们将使用 ComponentDidMount 生命周期方法来显示条形图。

将以下内容添加到条形图组件:

```
class BarChart extends Component {

  componentDidMount() {
    this.drawChart();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

drawChart 是我们施展所有 D3 魔法的方法。

通常，在没有 React 的情况下使用 D3 时，您不必将 D3 代码放在方法中，但是这在 React 中很重要，以确保只有当组件已经安装在 DOM 上时才显示图表。

接下来，我们创建 drawChart 方法:

```
drawChart() {

  const data = [12, 5, 6, 6, 9, 10];

  const svg = d3.select("body").append("svg").attr("width", 700).attr("height", 300);

} 
```

Enter fullscreen mode Exit fullscreen mode

这是怎么回事？

首先，我们定义了一个变量 data，它包含了我们想要可视化的数据。

接下来，我们使用 D3 方法定义了一个 SVG。我们使用 SVG 是因为它是可扩展的，也就是说，无论屏幕有多大，或者你放大多少来查看数据，它都不会出现像素化。

d3.select()用于从文档中选择一个 HTML 元素。它选择与传递的参数匹配的第一个元素，并为其创建一个节点。

在本例中，我们传递了 body 元素，稍后我们将对其进行更改，以使组件更加可重用。

append()方法将一个 HTML 节点附加到所选项中，并返回该节点的句柄。

attr 方法用于向元素添加属性。这可以是您通常添加到 HTML 元素中的任何属性，如类、高度、宽度或填充。

然后，我们将一个 SVG 元素附加到 body 元素，宽度为 700，高度为 300。

在我们创建的 SVG 变量下，添加以下代码:

```
svg.selectAll("rect").data(data).enter().append("rect") 
```

Enter fullscreen mode Exit fullscreen mode

就像 select 方法一样，selectAll()选择与传递给它的参数相匹配的元素。因此，匹配参数的所有元素都会被选中，而不仅仅是第一个元素。

接下来，data()方法用于将作为参数传递的数据附加到选定的 HTML 元素。

大多数时候，这些元素是找不到的，因为大多数可视化处理的是动态数据，并且几乎不可能估计将要表示的数据量。

enter()方法将我们从瓶颈中解救了出来，因为它与 append 方法一起使用来创建缺失的节点，并且仍然可视化数据。

到目前为止，我们已经为每个数据点创建了节点。剩下的就是让它可见。

为了使它可见，我们需要为每个数据集创建一个条，设置宽度并动态更新每个条的高度。

attr 方法允许我们使用一个回调函数来处理动态数据:

```
selection.attr("property", (d, i) => {}) 
```

Enter fullscreen mode Exit fullscreen mode

其中 d 是数据点值，I 是数组中数据点的索引。

首先，我们需要将每个数据点设置在条形图的 x 轴和 y 轴上的特定点。我们使用“x”和“y”属性来实现这一点，其中“x”表示条形沿 x 轴(水平)的位置,“y”表示条形沿 y 轴的位置。

此外，我们需要设置每个数据点的宽度和高度。每个数据点的宽度是恒定的，因为条形的宽度相同。

另一方面，高度取决于每个数据点的值。我们必须使用回调函数来使条形图显示每个数据点的值。

我们将 SVG 变量修改为:

```
svg.selectAll("rect")
  .data(data)
  .enter()
  .append("rect")
  .attr("x", (d, i) => i * 70)
  .attr("y", 0)
  .attr("width", 25)
  .attr("height", (d, i) => d)
  .attr("fill", "green"); 
```

Enter fullscreen mode Exit fullscreen mode

对于“x ”,数组中数据点的每个索引乘以一个常数整数 70，将每个条形的位置移动 70。

“y”有一个常量值 y，我们很快就会改变它。

宽度也有一个常数值 65，它小于图表上每个元素的位置，以便在每个元素之间创建一个空间。

条形的高度取决于数据集中每个条目的值。

利用这一点，我们创建了一个条形图。然而，我们有两个问题:

1.  图表中的条形很小
2.  图表也是倒置的

为了解决上述问题，我们将每个数据乘以一个常量值，比如 10，在不影响数据的情况下增加每个条形的大小:

```
.attr("height", (d, i) => d * 10) 
```

Enter fullscreen mode Exit fullscreen mode

*条形图放大但仍然倒置*

<figure>[![](img/d265a0bb6af03bc085796e07caf7a15d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GfDvgqTK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A4isoGrZofpkZwasOpvONIw.png) 

<figcaption>条形图放大后仍然倒置</figcaption>

</figure>

接下来，我们解决棒线反转的问题，但在此之前，让我们先了解为什么图表是反转的。

SVG 位置从上到下开始，因此使用 y 属性 0 会将每个条形放在 SVG 元素的顶部边缘。

为了解决这个问题，我们从 SVG 元素的高度中减去每个条的高度:

```
.attr("y", (d, i) => h - 10 * d) 
```

Enter fullscreen mode Exit fullscreen mode

其中(10 * d)是我们之前计算得到的高度。

将所有这些放在一起，条形图组件将是:

```
class BarChart extends Component {
  componentDidMount() {
    this.drawChart();
  }

  drawChart() {
    const data = [12, 5, 6, 6, 9, 10];

    const svg = d3.select("body")
    .append("svg")
    .attr("width", w)
    .attr("height", h)
    .style("margin-left", 100);

    svg.selectAll("rect")
      .data(data)
      .enter()
      .append("rect")
      .attr("x", (d, i) => i * 70)
      .attr("y", (d, i) => h - 10 * d)
      .attr("width", 65)
      .attr("height", (d, i) => d * 10)
      .attr("fill", "green")
  }

  render(){
    return <div id={"#" + this.props.id}></div>
  }
}

export default BarChart; 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![](img/7f6e6e727e132e3c44ac73f52a0b508c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w-5OsgJt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AW0xzQ56HggkcHRvGd-Cy-Q.png) 

<figcaption>条形图中的</figcaption>

</figure>

我们现在有了一个基本的条形图。让我们做一点额外的工作，添加标签。

#### 给条形图添加标签

为了添加标签，我们将以下代码添加到 drawChart 函数中:

```
svg.selectAll("text")
  .data(data)
  .enter()
  .append("text")
  .text((d) => d)
  .attr("x", (d, i) => i * 70)
  .attr("y", (d, i) => h - (10 * d) - 3) 
```

Enter fullscreen mode Exit fullscreen mode

这类似于我们对条形所做的，但这一次，文本被追加。

条形图现在应该是这样的:

<figure>[![](img/b1db4fe8d51843731705137bee074eaf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XqoOM7pq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATuAQYhi9Bx1AKmG4WLG1-w.png) 

<figcaption>带标签的条形图</figcaption>

</figure>

#### 使条形图可重复使用

React 的一个重要部分是制造可重用的组件。

为此，我们需要移除提供的数据，然后通过 props 将其传递给组件。

SVG 的宽度和高度也将通过 props 传递:

```
const data = [12, 5, 6, 6, 9, 10]; 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
const data = this.props.data; 
```

Enter fullscreen mode Exit fullscreen mode

宽度和高度属性从:

```
const svg = d3.select("body").append("svg").attr("width", 700).attr("height", 300); 
```

Enter fullscreen mode Exit fullscreen mode

至

```
const svg = d3.select("body").append("svg")
  .attr("width", this.props.width)
  .attr("height", this.props.height); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的 App.js 文件中，我们现在可以使用组件并从父组件传递我们想要的数据:

```
class App extends Component {

  state = {
    data: [12, 5, 6, 6, 9, 10],
    width: 700,
    height: 500,
    id: root
  }

  render() {
    return (
      <div className="App">
        <BarChart data={this.state.data} width={this.state.width} height={this.state.height} />
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们可以在 React 应用程序中的任何地方重用条形图。

干杯！！！

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

使用 React D3 在 React 中进行数据可视化的帖子[首先出现在](https://blog.logrocket.com/data-visualization-in-react-using-react-d3-c35835af16d0/)[的 LogRocket 博客](https://blog.logrocket.com)上。