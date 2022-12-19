# 使用 Vue 组件和 D3 将数据绑定到图表

> 原文：<https://dev.to/ignoreintuition/binding-data-to-charts-using-vue-components-and-d3-4i27>

没有什么比做出改变并看到这种改变在某个地方迅速传播更令人满意的了。最近，我一直在尝试将 D3 图表集成到 Vue 中。我试着用[助手](https://github.com/ignoreintuition/d3vue)、指令和最近的[组件](https://github.com/ignoreintuition/v-chart-plugin)来做这件事。因为 Vue 声明组件是他们推荐的可重用组件的实现方法，所以我在这里将重点放在那个版本上。

如果你对 [Vue](https://vuejs.org/) 不熟悉，它是一个流行的双向数据绑定的 JavaScript 框架。 [D3](https://d3js.org/) 是一个在 DOM 中建模数据的库。我们要关注的是在 Vue 中构建一个可重用的组件，利用 D3 库来创建一个与 Vue 中的数据保持同步的图表。

我们将从 [vue-cli](https://www.npmjs.com/package/vue-cli) 开始，这将允许我们为我们的新应用程序创建一个模板。一旦我们有了入门应用程序，我们将在 src 目录下创建一个 JavaScript 文件，名为 v-chart-plugin.js。这将需要导入并在我们的 main.js
中使用

```
import Chart from './v-chart-plugin.js'

Vue.use(Chart); 
```

Enter fullscreen mode Exit fullscreen mode

这里的想法是，呈现图表和绑定数据的所有功能都将在组件中处理，与 Vue 实例完全分离。这样，当添加组件时，开发人员只需添加带有适当数据绑定的元素。

```
<v-chart v-bind:chartData="chartData"></v-chart> 
```

Enter fullscreen mode Exit fullscreen mode

只要被传递的对象(在本例中是 chartData)遵循具有所需值的适当结构，对 dev 就没有进一步的要求。组件本身的结构如下:

*   导入依赖库(D3)
*   定义组件
*   导出组件
*   使用组件(如果 vue 是在全局范围定义的)

这个框架看起来会像这样

```
// Limited to just those dependencies we are going to need for the example
var d3 = Object.assign({}, 
    require("d3-selection"));

const Chart = {
...
}

export default Chart;

if (typeof window !== 'undefined' && window.Vue) {
    window.Vue.use(MyPlugin)
} 
```

Enter fullscreen mode Exit fullscreen mode

插件的大部分将位于图表对象本身的定义中。如果你熟悉 Vue，那么你应该认识很多这些方法和属性。如果你熟悉 React 和 Angular 等其他框架，那么其中的一些术语应该也很熟悉。在我们进入细节之前，让我们检查一下组件的结构

```
const Chart = {
    install(Vue, options) {
        Vue.component('v-chart', {
            props: ['chartData'], 
            data: function () {
                ...
            },
            methods: {
                ...
            },
            // lifecycle events
            mounted: function () { 
                ...
            },
            // watch functions
            watch: {
                ...

            },
            template:
                ...
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你应该认识到垂直图表的价值。这是我们的 Vue 模板中用来将组件添加到应用程序中的值。在那下面我们引用道具。这与我们通过 v-bind 指令绑定的对象相关。您可以传递多个参数，而不是一个对象，但我将使用一个对象，因为它更容易配置。跟在道具后面的是熟悉的数据和方法，它们是组件范围内的值和函数。我们不会在这个例子中使用数据，因为所有的数据都来自道具，但我们将广泛使用方法。请注意，对于组件，数据需要是一个函数。

```
Vue.component('v-chart', {
    props: ['chartData'], 
    data: function () {
        return {
        }
    },
    methods: {
        initalizeChart: function () {
            this.drawChart();
        },
        refreshChart: function () {
            this.clearCanvas();
            this.drawChart();
        },
        drawChart: function () {
            ...
        },
        clearCanvas: function () {
            d3.select(this.chartData.selector).selectAll("*").remove();
        },
        getHeight: function () {
            return this.chartData.height || 200;
        },
        getWidth: function () {
            return this.chartData.width || 200;
        }
    }, 
```

Enter fullscreen mode Exit fullscreen mode

在我们的方法之外，我们有几个额外的函数用于一些特定的目的。首先是[救生圈挂钩](https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram)。这些函数在应用程序中以特定的时间间隔被调用。这允许我们将功能与这些事件相关联，例如当实例被创建、更新、销毁等时。我们将使用`mounted`钩子来确保在呈现图表时页面被加载。

[![Lifecycle Hooks](../Images/ed1ae2928867708fe605294ba0ca53fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L0NIOsGR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gowj25um09ko6yrvfpqz.png)T3】

```
mounted: function () { // <-- lifecycle events
    this.initalizeChart();
}, 
```

Enter fullscreen mode Exit fullscreen mode

我们将添加的其他特殊方法是观察函数。监视函数的唯一目的是在数据更新时执行。这将允许我们在数据发生变化时重新呈现图表。这样做将确保图表始终与数据同步。

```
watch: { // <-- watch functions
    'chartData': {
        handler: function (val) {
            this.refreshChart();
        },
        deep: true
    }
}, 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们有了模板。这是我们打算在页面上呈现的内容的标准 Vue 模板。在这个例子中，它只是一个 SVG，因为我们将使用它来绘制我们的图形。我们使用内插法根据对象中的配置获得宽度和高度的值。

```
template:
    ` ` 
```

Enter fullscreen mode Exit fullscreen mode

如果你一直跟着我，你会注意到我故意省略了 drawChart 函数的细节。这是使用 D3 在模板中创建的 SVG 画布上绘制图表的部分代码。这将在很大程度上依赖于我们从 props 传入的数据:特别是 chartData.selector，用于标识图表的唯一 id，以及 chartData.data，它是在 Vue 实例中配置的数据数组。剩下的是一个样板 D3，它绑定数据并附加长度等于数组中每个值的矩形。这可以扩展到创建任何数据可视化。

```
drawChart: function () {
    d3.select(this.chartData.selector)
        .append("text")
        .attr("x", 20)
        .attr("y", 20)
        .style("text-anchor", "left")
        .text(this.chartData.title)

    d3.select(this.chartData.selector)
        .selectAll("g")
        .data(this.chartData.data)
        .enter().append("g")
        .append("rect")
        .attr("width", function (d) {
            return d;
        }).attr("height", 20)
        .attr("y", function (d, i) {
            return (i + 1.5) * 20 + i
        }).attr("x", 0);
}, 
```

Enter fullscreen mode Exit fullscreen mode

[![screenshot](../Images/da055f0cac7bc73ae99544234e183cfe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nBmlCUJ2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7bu2p3740cfn3cq3qygm.png)

如果你想看到完整的代码并克隆本教程所基于的 Github Repo。希望这能为您提供一个基础，让您了解如何将来自 Vue 的数据与组件绑定，并在可视化中对其建模。