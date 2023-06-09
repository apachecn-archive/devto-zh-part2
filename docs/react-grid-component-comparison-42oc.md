# 反应网格组件比较

> 原文：<https://dev.to/ngduc/react-grid-component-comparison-42oc>

像往常一样，在选择 React 组件时有很多选择，在本例中，选择 Grid 组件。这篇文章试图比较这些选择。

下面是流行的网格组件列表:

*   react-虚拟化网格(bvaughn) - MIT [演示](https://bvaughn.github.io/react-virtualized/#/components/Grid)

*   react-tools/react-table - MIT [演示](https://react-table.js.org/#/story/readme)

*   react-data-grid(adazzle)-MIT[演示](http://adazzle.github.io/react-data-grid/#/examples/all-features)

*   react-bootstrap-table2 - MIT [演示](https://react-bootstrap-table.github.io/react-bootstrap-table2/storybook/index.html)

*   olifolkerd/制表器(react 版本:[react-制表器](https://github.com/ngduc/react-tabulator) ) -麻省理工学院[演示](http://tabulator.info/examples/4.0)

它们是真正的高品质和受欢迎的组件。因此支持大多数常见的用例。但是在这里，我只是基于一些不太常见但“很好拥有”的特性进行比较。

### 好听有特点:

#### 虚拟化渲染

虚拟化意味着只呈现可见的行。当你想渲染大量数据时，它非常有用。如果没有这个，UI 会因为创建了许多 DOM 元素而变慢。

反应-制表器-是
反应-虚拟化网格-是
反应-数据-网格-是

react-table - no info(支持分页)
react-bootstrap-table 2-no info(支持分页)

#### 可调整大小的列

反应-制表-是
反应-数据-网格-是
反应-表格-是

react-虚拟化网格-无信息
react-引导-表 2 -无信息

#### 单元格编辑

这也是一个常见的用例。有时您希望用户编辑网格中的数据。

反应-制表-是
反应-数据-网格-是
反应-表格-是
反应-引导-表格 2 -是

react-虚拟化网格-无信息

#### 嵌套表格

不太常见，但在一些报告屏幕中，当我们想要按组显示数据时，需要嵌套表。

反应-制表-是
反应-数据-网格-是
反应-表格-是

react-虚拟化网格-无信息
react-引导-表 2 -无信息

#### 按列过滤

大多数用户都会喜欢的便利功能。

反应-制表-是
反应-数据-网格-是
反应-表格-是
反应-引导-表格 2 -是

react-虚拟化网格-无信息

### 结论

最后，我发现这些网格组件满足了我需要的所有好的需求: [react-data-grid](https://github.com/adazzle/react-data-grid) 和[react-制表器](https://github.com/ngduc/react-tabulator)。

你呢？你最喜欢的 React 网格组件是什么，为什么？