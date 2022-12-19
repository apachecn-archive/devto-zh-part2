# 使用 griddle-react 创建定制的数据网格

> 原文：<https://dev.to/kris/create-customized-data-grid-using-griddle-react-eeh>

Griddle 是 React 的一个高度可定制的数据网格组件。它支持卡片、嵌套、地图等等。Griddle 不仅仅依赖默认行为，还允许完全控制显示的内容。本教程试图捕捉使用 Griddle 时可以设置的最有用的选项。

几天前，我讨论了使用`reactable`处理表格，Reactable 快速、灵活且简单。

> [在这里](https://dev.to/kris/create-a-table-in-react-using-reactable-4idn-temp-slug-3689087)阅读 reactable 的帖子。

现在，该是`griddle-react`的时候了。Griddle 还支持更高级别的定制插件。你也可以为任何项目创建自己的插件，或者使用现有的插件。

Griddle 作为一个组件存在，其结构类似于许多 React 应用程序。

Griddle 文档还说:

> Griddle 被排列成一系列容器和视图组件。容器组件连接到商店，设置逻辑，并将动作作为道具连接到视图组件。

为了更深入地理解这个包的架构，你可以[访问官方文档](http://griddlegriddle.github.io/Griddle/docs/)。

我将用 griddle 可以做什么的例子来探索一些情况。

教程的资源库在 GitHub 上，所有组件都可以从 App.js 文件中呈现，所有组件都将在最终提交时预导入。您可以根据教程更改从`App`渲染的组件来更改组件。

所有代码片段也可以在 GitHub Gist 上找到。它们被嵌入到任何需要的地方。

**顶级用例:**

*   可定制，开箱即用，但仍极具可定制性。
*   自定义列或无需外部 css 的列自定义。
*   插件，使用现有的或创建自己的插件，并共享或自己使用。

如果你需要查看所有可能的 API，我在本教程中跳过了一些，你可以[在这里](http://griddlegriddle.github.io/Griddle/docs/api/)找到它们。

Griddle 有很多可以使用的道具和配置选项。本教程试图捕捉一些有用的选项，可以在使用 Griddle 时设置。

**数据**:是包含列值的对象数组。

**插件**:插件数组，插件可以简单导出。组件可以增强或替换。

**事件**:事件有 onFilter，onSort，onNext，onPrevious，onGetPage。

**sortProperties** :取一个或多个对象，每个对象都有 id 和 sortAscending。id 是列的名称。

**styleConfig** :获取图标和预定义的类名以及插件添加的那些。还支持自定义样式。

**页面属性**:当前页面和页面大小(记录数)

#### 更多组件

单元格、过滤器、布局、非结果、分页、行等

**按照下面的例子……**

`npm i griddle-react`

`import Griddle from 'griddle-react'`

我将重用上一篇[教程中关于 reactable](https://dev.to/kris/create-a-table-in-react-using-reactable-4idn-temp-slug-3689087) 的大部分 css，并增加一些内容。你可以在这里从本教程的[库获得 css。](https://github.com/therj/griddle-react-tutorial/blob/master/src/index.css)

App 组件将呈现`MyGriddle`，该组件包含表的所有代码。

查看[要点](https://gist.github.com/therj/6a7d311785b4c5faf2388d10b59c4c74)上的代码。

在 MyGriddle 中，从 MOCK_API 导入 fakeData，从`griddle-react`导入 Griddle。

`import Griddle from 'griddle-react'`

`import fakeData from './MOCK_DATA'`

对于初始状态，我将把`pageSize`设置为 5，把`currentPage`设置为 1(第一页)。`recordCount`是数据的全长(`fakeData.length`)。

Griddle 有一些传递给它的属性。需要`data`、`currentPage`、`pageSize`、`recordCount`来显示表格中的数据。插件是可选的，目前只有两个可用。一个用于滚动而不是分页，另一个用于过滤和排序之类的东西。

查看[要点](https://gist.github.com/therj/6a7d311785b4c5faf2388d10b59c4c74)上的代码。

**pageProperties:** 定义当前页面、页面大小和记录计数。

**事件:**定义 onNext、onPrevious、onGetPage 动作。没有这些操作，下一个和上一个按钮将不起作用。

**组件:**隐藏或重新定义过滤器，设置标签选项。

**styleConfig:** 包含所有的样式选项，包括每个组件的类名。

[![](img/ceb6af2010aba68704cd8fc8f8db322f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8rkCQjza--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/reactninja.io/wp-content/uploads/2018/08/img_5b8843791099d.png%3Fssl%3D1)

必须定义 onNext、onPrevious、onGetPage 操作，这些操作才能起作用。

所有这些动作都通过函数`fakeLoadDataFromAPI`来处理。它接收 currentPage、pageSize 和一个回调函数。然后，它根据 pageSize 和 currentPage 的值将下一组/上一组数据发送给回调函数。

回调函数是`updateTableState`。它将状态设置为传递的任何数据。

**_ on next:**fakeLoadDataFromAPI(current page+1，pageSize，this.updateTableState)

**_ on previous:**fakeLoadDataFromAPI(current page–1，pageSize，this.updateTableState)

[![](img/dc1fe12aada42f53dccca8e67efd739b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M8pxSYjL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/reactninja.io/wp-content/uploads/2018/08/previous_next.png%3Fresize%3D1572%252C600%26ssl%3D1)

但是如果您选择页码而不是上一页/下一页，它会使用`onGetPage`来加载特定的页面。

[![](img/a0bd57b400152d2f044b71a4ba7f2458.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0scLCGY---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/reactninja.io/wp-content/uploads/2018/08/get_page.png%3Fresize%3D1528%252C360%26ssl%3D1)

与下一个/上一个一样，数据从`fakeLoadAPI`开始操作，状态从`updateTableState`开始设置。

现在，我们有了一个带有分页和过滤器的工作表。

[![](img/1154e5f1390976d06cae9cbec7556174.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--bDZOdVMN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/reactninja.io/wp-content/uploads/2018/08/img_5b884a4d355b4.png%3Fresize%3D398%252C281%26ssl%3D1) [ ![](img/8c4b21f1fe1f6bf316f970f9d0816a20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p2co8lT5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/reactninja.io/wp-content/uploads/2018/08/img_5b884b15440aa.png%3Fresize%3D398%252C196%26ssl%3D1)

帖子[使用 griddle-react](https://reactninja.io/2018/09/02/create-customized-data-grid-using-griddle-react/) 创建定制数据网格最早出现在[react inja](https://reactninja.io)上。