# 如何将 Microsoft Power BI 图表嵌入您的 React 应用程序

> 原文：<https://dev.to/akshay5995/how-to-embed-microsoft-power-bi-charts-into-your-react-application-897>

使用合适的工具，将 Microsoft Power BI 图表嵌入到 React 应用程序中可以轻而易举。

注意:*本文假设您对 Microsoft Power BI 和一些相关术语有相当不错的了解。*

## TL；速度三角形定位法(dead reckoning)

使用您最喜欢的软件包管理器安装`powerbi-report-component`。

`npm i powerbi-report-component --save-dev`

或者，

`yarn add powerbi-report-component --dev`

基本用法，

```
import Report from "powerbi-report-component";

<Report
  embedType="report"
  tokenType="Embed"
  accessToken=""
  embedUrl=""
  embedId=""
  permissions="All"
  style={myStyleObject}
/> 
```

* * *

## 现在，对于那些留下来的人:)

对于初学者来说，将 Power BI 报告嵌入到 React 应用程序中可能是一件痛苦的事情。尽管微软关于这个主题的文档非常简洁。

我只是从 React 开发人员的角度告诉你需要什么，而不是进入实现或架构的细节。

有两种方法可以嵌入您的 Power BI 报告:

1.  用户拥有数据
2.  应用程序拥有数据

主要区别在于:

用户拥有的数据样本用于为您的组织嵌入，嵌入的报告读者可以是您组织中的 Power BI Pro 或免费用户。用户必须使用他们的 PBI 帐户登录。需要 Power BI Premium 许可证。(例如，用户是你广告的一部分)

App Owns 数据样本是为您的客户嵌入的，嵌入的报告读者可以是您自己的客户(假设您是 ISV 应用提供商)。所以不能登录。需要 Power BI Embedded 许可证。(即您广告之外的用户)

> 来源:[https://community . power bi . com/t5/Developer/App-vs-User-Owns-Data/TD-p/300729](https://community.powerbi.com/t5/Developer/App-vs-User-Owns-Data/td-p/300729)

您必须选择谁将使用包含您的嵌入式报告的应用程序，即内部用户(广告用户)与外部用户(非广告用户)。

既然你已经决定了要选择哪种方法。

希望你已经准备好了你的报告，并且能够在[https://powerbi.microsoft.com](https://powerbi.microsoft.com)查看。

现在，导航到您的工作区下的报告，您的 URL 应该如下所示:

**[https://app . power bi . com/groups/{ workspace-id }/reports/{ report-id }](https://app.powerbi.com/groups/%7Bworkspace-id%7D/reports/%7Breport-id%7D)**

对于仪表板，您可能会看到这样的内容:

**[https://app . power bi . com/groups/{ workspace-id }/dashboards/{ dashboard-id }](https://app.powerbi.com/groups/%7Bworkspace-id%7D/dashboards/%7Bdashboard-id%7D)T3】**

要嵌入您的报告，您需要以下内容:

1.  报告 ID:来自 URL
2.  工作区 ID:同样，来自 URL
3.  令牌:AAD 或嵌入令牌(用于身份验证))

**注意:在采用用户拥有数据方法时使用 AAD 令牌，在采用应用拥有数据方法时使用 Embed 令牌。**

*嵌入 Url:* (将由报告 ID 和工作区 ID 组成)

> [https://app.powerbi.com/reportEmbed?reportId=*报表-id*T6】群组 Id= *工作区-id*T5】](https://app.powerbi.com/reportEmbed?reportId=_report-id_&groupId=_workspace-id_)

将报告嵌入 react 应用程序主要有两种方法。

1.  使用 iframe
2.  使用微软提供的 Power BI JS API

使用 iframe 非常简单，但是它没有 React 组件提供的灵活性，比如回调或事件触发器。

您可以像这样使用 iframe 使用生成的 URL 进行嵌入，

```
<iframe
  width="800"
  height="600"
  src="http://myserver/reports/powerbi/Sales?rs:embed=true"
  allowfullscreen="true"
>
</iframe> 
```

但是，作为 JS 开发人员，我们自然倾向于使用更灵活的 JS API。

可以看看微软的[演示页面](https://microsoft.github.io/PowerBI-JavaScript/demo/v2-demo/index.html#)

他们有一个非常清晰的解释和使用 Javascript API 的普通 JS 演示。

你可能想知道，演示是用普通的 JS 做的，所以如果我必须把它做成一个 React 组件并在我的应用程序中使用它，嗯，那可能需要一段时间🤔

***power bi-report-component 来救援了！✨***

检查[包](https://www.npmjs.com/package/powerbi-report-component)。

用法，

```
import Report from "powerbi-report-component";

<Report
  embedType="report" // or "dashboard"
  tokenType="Embed" // or "Aad"
  accessToken="" // accessToken goes here
  embedUrl="" // embedUrl goes here
  embedId="" // Report or Dashboard ID goes here
  permissions="All" // or "View"
  style={myStyleObject}
/> 
```

**目前支持的功能:**

1.  通过将样式传递给嵌入的报表组件来定制样式。
2.  该组件还允许您传递回调以触发以下事件:

*换页*

```
onPageChange={(data) =>
  console.log(`Page name :{data.newPage.displayName}`)
} 
```

*载入*

```
onLoad={(report) => {
      console.log('Report Loaded!');
      this.report = report;
// Read docs to know how to use the report object that is returned
    }
} 
```

*数据元素被点击*

```
onSelectData={(data) =>
  console.log(`You clicked on chart: {data.visual.title}`)
} 
```

1.  使用返回到父组件的“报告”对象:

全屏

```
setFullscreen = () => this.report.fullscreen() 
```

打印报告

```
printReport = () => this.report.print() 
```

更改报告模式，显示/隐藏可视标题、过滤器(设置、获取、删除)

改变模式

```
// mode can be "view" or "edit"

changeMode = mode => this.report.switchMode(mode) 
```

显示或隐藏可视标题

```
toggleAllVisualHeaders = bool => {
  const newSettings = {
    visualSettings: {
      visualHeaders: [
        {
          settings: {
            visible: bool,
          },
        },
      ],
    },
  }
  this.report
    .updateSettings(newSettings)
    .then(function() {
      console.log(`Visual header was toggled to {bool}`)
    })
    .catch(function(errors) {
      console.log(errors)
    })
} 
```

设置过滤器

```
//example filter object from Microsoft's demo page

    const filter = {
      $schema: "http://powerbi.com/product/schema#basic",
      target: {
        table: "Store",
        column: "Chain"
      },
      operator: "In",
      values: ["Lindseys"]
    };

    // using event handlers

    setFilter = (filter) =>
    this.report.setFilters([filter]).catch(function (errors) {
        console.log(errors);
    });

    // during load of report

    onLoad = (report) => {
      report.setFilters([filter]).catch(function (errors) {
        console.log(errors);
      });
      this.report = report;
    }
  } 
```

获取过滤器

```
getFilter = () =>
  this.report
    .getFilters()
    .then(function(filters) {
      console.log(filters)
    })
    .catch(function(errors) {
      console.log(errors)
    }) 
```

移除过滤器

```
removeFilters = () =>
  this.report.removeFilters().catch(function(errors) {
    console.log(errors)
  }) 
```

更多功能即将推出！⚡️

链接:[演示现场](http://akshay5995.github.io/powerbi-report-component)， [GitHub](https://github.com/akshay5995/powerbi-report-component)

> 我很乐意听到您的想法和反馈(以及对新功能的任何要求，请访问[https://github . com/akshay 5995/power bi-report-component/issues](https://github.com/akshay5995/powerbi-report-component/issues))。