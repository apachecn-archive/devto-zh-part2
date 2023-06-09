# 角度基础-管道

> 原文：<https://dev.to/kritner/angular-basics--pipes-180>

[![](img/167376f98eeda2bf06590901f05908e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aIaj3CFg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/250/0%2AlJSPbTA4XhBThbML.png)

最近，我开始为我的太阳能投影页面[http://www.kritner.com/solar-projection/](http://www.kritner.com/solar-projection/)摆弄 Angular，并认为我应该记录一些我摆弄“新”技术的经历——至少相对而言。

在我的日常工作中，我是一名 c#开发人员，目前 ***单独*** 是一名 c#开发人员。在这份工作之前，我精通 c#、db 和一些前端的东西。

不管怎样，上一篇文章我们讨论了基本的数据绑定，这篇文章我想在这一点上做一些扩展。具体来说，我们在视图上绑定了大量的数字(我仍然不知道这个词是否正确)，所以一些格式应该是好的！货币格式和/或千位分隔符是 IMO 的好东西。

为了应用这样的格式，我们可以使用管道—[https://angular.io/guide/pipes](https://angular.io/guide/pipes)。幸运的是，它们很容易使用，并且有一些已经存在，可以用于我的用例！

从[https://angular.io/api](https://angular.io/api)我找到了 CurrencyPipe 和 NumberPipe，这两个我都会用在我上一篇文章的模型上。

作为起点:

```
<h1>Solar Projection</h1>

<p *ngIf="!solarProjection"><em>Loading...</em></p>

<div *ngIf="solarProjection">

<h4>Solar Estimate (Guaranteed for 90% of 17k Kw/year Production</h4>

<ul>
    <li>Total Cost/year: {{ solarProjection.solarEstimate.totalCost }}</li>
    <li>Total Kw/h/year: {{ solarProjection.solarEstimate.totalKiloWattHours }}</li>
    <li>Avg Cost/month: {{ solarProjection.solarEstimate.averageCostPerMonth }}</li>
    <li>Avg Cost/kw/h {{ solarProjection.solarEstimate.averageCostKiloWattHour }}</li>
    <li>Total Years Financed: {{ solarProjection.financeYears }} </li>
  </ul>

<table class='table'>
    <thead>
      <tr>
        <th>Year</th>
        <th>Total Cost Utility</th>
        <th>Avg. Cost/month (Utility)</th>
        <th>Cost/year 100% gen.</th>
        <th>Savings/year 100% gen.</th>
        <th>Cost/year 90% gen.</th>
        <th>Savings/year 90% gen.</th>
        <th>Cost/year 80% gen.</th>
        <th>Savings/year 80% gen.</th>
      </tr>
    </thead>
    <tbody>
      <tr *ngFor="let projection of solarProjection.futureProjection">
        <td>{{ projection.purchaseYear }}</td>
        <td>{{ projection.totalCost }}</td>
        <td>{{ projection.averageCostPerMonth }}</td>
        <td>{{ projection.costSolar100Percent }}</td>
        <td>{{ projection.totalSavings100Percent }}</td>
        <td>{{ projection.costSolar90Percent }}</td>
        <td>{{ projection.totalSavings90Percent }}</td>
        <td>{{ projection.costSolar80Percent }}</td>
        <td>{{ projection.totalSavings80Percent }}</td>
      </tr>
    </tbody>
  </table>

</div> 
```

这些数字的很大一部分(像“成本”s)可以认为是“货币”，其他的一般是 kw/h 的单位，可以认为是“数字”。例如，要应用管道，只需将绑定数据“管道化”到正在使用的“管道”中(类似于 bash 中的做法)`{{ myData | currency }}`。

将上面的内容应用到我们的页面，现在看起来像这样:

```
<h1>Solar Projection</h1>

<p *ngIf="!solarProjection"><em>Loading...</em></p>

<div *ngIf="solarProjection">

  <h4>Solar Estimate (Guaranteed for 90% of 17k Kw/year Production</h4>

  <ul>
    <li>Total Cost/year: {{ solarProjection.solarEstimate.totalCost | currency }}</li>
    <li>Total Kw/h/year: {{ solarProjection.solarEstimate.totalKiloWattHours | number }}</li>
    <li>Avg Cost/month: {{ solarProjection.solarEstimate.averageCostPerMonth | currency }}</li>
    <li>Avg Cost/kw/h {{ solarProjection.solarEstimate.averageCostKiloWattHour | currency }}</li>
    <li>Total Years Financed: {{ solarProjection.financeYears }} </li>
  </ul>

  <table class='table'>
    <thead>
      <tr>
        <th>Year</th>
        <th>Total Cost Utility</th>
        <th>Avg. Cost/month (Utility)</th>
        <th>Cost/year 100% gen.</th>
        <th>Savings/year 100% gen.</th>
        <th>Cost/year 90% gen.</th>
        <th>Savings/year 90% gen.</th>
        <th>Cost/year 80% gen.</th>
        <th>Savings/year 80% gen.</th>
      </tr>
    </thead>
    <tbody>
      <tr *ngFor="let projection of solarProjection.futureProjection">
        <td>{{ projection.purchaseYear }}</td>
        <td>{{ projection.totalCost | currency }}</td>
        <td>{{ projection.averageCostPerMonth | currency }}</td>
        <td>{{ projection.costSolar100Percent | currency }}</td>
        <td>{{ projection.totalSavings100Percent | currency }}</td>
        <td>{{ projection.costSolar90Percent | currency }}</td>
        <td>{{ projection.totalSavings90Percent | currency }}</td>
        <td>{{ projection.costSolar80Percent | currency }}</td>
        <td>{{ projection.totalSavings80Percent | currency }}</td>
      </tr>
    </tbody>
  </table>

</div> 
```

看起来像这样:

[![](img/2472c58bf2e05b385089b1b50284e3c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9GI5RAx0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/702/1%2AkWFCTJX0jRB3nX753Nb8rw.png) 

<figcaption>管道应用数据</figcaption>