# 在 Angular 应用程序中实现 Chart.js 折线图

> 原文：<https://dev.to/wiaio/implementing-a-chartjs-line-chart-in-your-angular-application-19d7>

[![alt text](img/a8b6a4befc4f69cef58c8bcb11e8d326.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w1vRHkRQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-06-07/1528382411-789265-chart-js-angular.png)

Chart.js 是一个开源库，用于用 Javascript 构建好看的图表。以下是如何在 Angular 应用程序中轻松使用它来显示数据。

### **安装库**

首先，您需要安装 Chart.js 库:

`npm install chart.js --save`

接下来，在组件中导入模块:

`import { Chart } from 'chart.js';`

### **准备好你的资料**

要呈现您的图表，每个数据集应该是一个数字数组，或者是一个对象数组，格式如下，其中`x`和`y`指的是图表的轴:

```
[{
  x: 1,
  y: 10
}, {
  x: 2,
  y: 20
}] 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用的是[时标](https://www.chartjs.org/docs/latest/axes/cartesian/time.html)，那么您可以用`t`替换`x`或`y`属性。
使用 [Array.map()方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)为 Chart.js 构建数据。

你也可以为 x 轴指定标签——这也必须是一个数组。

### **模板**

在组件的模板中，添加一个带有您选择的[引用变量](https://community.wia.io/d/24-template-reference-variables-in-angular-2)的`canvas`元素。在 canvas 元素内部，绑定到变量`chart`。我们将在下一步创建变量。

`<canvas #lineChart>{{ chart }}</canvas>`

### **配置图表**

回到组件中，从 angular core 导入`ViewChild`,并将引用作为私有成员添加到 canvas 元素中。然后，添加一个公共变量`chart`——它将被分配给一个 Chart.js 实例，并将被绑定到我们的模板:

```
import { Component, OnInit, ViewChild } from '@angular/core';

  @Component({
    ...
  })

  export class myComponent implements OnInit {
    @ViewChild('lineChart') private chartRef;
    chart: any;
    ...
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经准备好了数据和对 canvas 元素的引用，让我们为折线图添加基本的 Chart.js 配置:

```
this.chart = new Chart(this.chartRef.nativeElement, {
  type: 'line',
  data: {
    labels: labels, // your labels array
    datasets: [
      {
        data: dataPoints, // your data array
        borderColor: '#00AEFF',
        fill: false
      }
    ]
  },
  options: {
    legend: {
      display: false
    },
    scales: {
      xAxes: [{
        display: true
      }],
      yAxes: [{
        display: true
      }],
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意,`data.datasets`选项接受一个数组——您可以在同一个折线图上显示许多数据集。

图表现在将显示在您的页面上。如果你想让你的图表适合一个容器，你可以设置`maintainAspectRatio`选项为假。更多配置选项参见 [Chart.js 文档](http://www.chartjs.org/docs/latest/)！