# 用故事书简化你的前端开发

> 原文：<https://dev.to/kayis/streamline-your-front-end-development-with-storybook--4m22>

*[Flickr 上 Victor R. Ruiz 的封面图片](https://www.flickr.com/photos/rvr/)*

我经常改变一些东西**或**更新我使用的库，一些东西工作了，但是它们看起来不再漂亮了。

例如，我更新了`react-native-svg`，他们将`rotate`道具重命名为`rotation`，突然一个滑块不再旋转 90 度。它仍然工作，但看起来很奇怪。

所以我想，我需要一种快速找到这些问题的方法。自动也很好，但第一步应该让我看到这些问题，而不需要点击整个应用程序。

### 进入故事书

[Storybook](https://storybook.js.org/) 将自己标榜为**一个 UI 组件的开发环境**。

### 什么

Storybook 允许你在自己的 UI 中呈现不同状态的组件，这是一个组件目录。这允许你**快速浏览所有的组件**，例如当你想写一个新的来看看你是否已经有了可以完成工作的东西，或者如果你想在更新后检查一些是否坏了。

由于组件是在你的应用程序之外呈现的**,这也应该导致更好的组件架构**,因为它们必须是真正独立的。

## 如何

首先，将它添加到您的项目中。为此，您需要**安装 CLI** 来设置所有的依赖项，并创建一些您稍后使用的文件:

```
$ npm i -g @storybook/cli
$ cd your-project
$ getstorybook 
```

Enter fullscreen mode Exit fullscreen mode

命令确定你有什么样的项目，并尝试安装预先配置好的故事书。我用 [create-react-app](https://github.com/facebook/create-react-app) 创建了一个 React 应用。

在这之后，你的`package.json`中有**个新脚本**

*   `storybook`用于开发
*   `build-storybook`用于创建目录的静态构建

您还有一个名为`stories`的新目录，在我的`create-react-app`中，它是在`your-project/src`中创建的

你现在可以为你的组件编写**故事**，这有点像你从单元测试中了解到的测试。

让我们尝试一个简单的例子，我想要一个`<Text>`组件，让我通过一个布尔属性将它的`text-decoration`设置为`underline`。

```
// src/Text.js

import React from "react";

export default ({ children, underline }) => (
  <span style={{ textDecoration: underline ? "underline" : "none" }}>
    {children}
  </span> ); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我尝试在 Storybook 中呈现这个组件的两种道具状态。为此我将`Text`组件`import`转换成`src/stories/index.js`并使用故事书`storiesOf()`功能。

```
// src/stories/index.js

import React from "react";
import { storiesOf } from "@storybook/react";
import Text from "../Text";

storiesOf("Text", module)
  .add("without underline", () => <Text>Hello, world!</Text>)
  .add("with underline", () => <Text underline>Hello, world!</Text>); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我可以通过运行`storybook`脚本并在`http://localhost:9009`
打开浏览器来检查一切是否正常

```
$ yarn run storybook 
```

Enter fullscreen mode Exit fullscreen mode

在左边你可以看到一个树形导航。每次调用`storiesOf()`函数都会创建一个下拉列表，其中包含通过`add()`方法添加的故事链接。

我现在可以点击这个目录来查看我的`Text`组件的不同状态。

## 结论

故事书是一种有趣的前端开发方法。它允许您独立地创建和测试您的组件，因此您可以一次专注于一个问题。目录 UI 允许您快速浏览组件，并检查在您更改应用程序中的某些内容后它们是否仍然工作。