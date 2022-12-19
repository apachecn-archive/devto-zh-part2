# 将 Storybook 用作可视库和测试代码

> 原文：<https://dev.to/davidimoore/using-storybook-as-both-a-visual-library-and-test-code-3cb4>

在创建 React 组件并对其进行测试之后，您仍然需要查看它的外观。但是您可能没有视图或

父组件来呈现它。

在我之前的[文章](https://dev.to/davidimoore/using-fixtures-for-testing-a-reactredux-app-with-jest--enzyme-3hd0)中，我创建了一个 redux connected `UserListContainer`从远程数据源获取用户。我还创建了一个`UserList`表示组件来呈现获取的用户。另外，我为我们的

组件生成了 jest [快照](https://jestjs.io/docs/en/snapshot-testing#snapshot-testing-with-jest)。

最后，我在测试中使用了可重用的数据或设备。

假设您想要在另一个类似于`UserListView`的组件中呈现`UserList`组件(及其容器)。但是`UserListView`并不完整。

如果你只是想在将`UserList`临时添加到你的应用程序之前看看它是什么样子的呢？

这就是《T1》故事书提供真正实用的解决方案的地方。Storybook 可以让您单独查看组件。所以我们可以看到我们的`UserList`和一些样本用户。

在这篇文章中，我将做到以下几点:

*   将故事书添加到应用程序
*   为我的`UserList`组件创建故事
*   从这些故事中创建快照

如果你想了解这个博客的代码示例“开始”分支:

`git clone -b start git@github.com:davidimoore/blog-article-2.git`

获取已完成的回购

`git clone git@github.com:davidimoore/blog-article-2.git`

### 步骤 1–添加故事书

首先让我们安装故事书。对于这个项目，我在全球范围内安装它。

*   如果你使用`npm`做`npm install -g @storybook/cli`
*   如果你使用`yarn`做`yarn global add @storybook/cli`
*   你还需要为苹果电脑和自制软件安装[守望者](https://facebook.github.io/watchman/docs/install.html)

接下来，让我们将故事书添加到我们的项目中。在项目的根中运行`getstorybook`。

在我们的`package.json`文件中的`scripts`部分，我们应该看到添加了以下内容:

```
 "scripts": {
    ...
    "storybook": "start-storybook -p 9009 -s public",
    "build-storybook": "build-storybook -s public"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器中运行`yarn storybook`，然后转到`http://localhost:9009`。您应该看到以下内容:

 [![](../Images/4c2a40cd8eeeea1d16e1f23fa9d59b4a.png)

T6】](http://dmohq.com/wp-content/uploads/2018/07/storybook-welcome-screen-e1532906435668.png)

故事书还增加了 2 个目录

*   。故事书包含`addons.js`和`config.js`
*   stories 里面有一些例子代码。

### 步骤 2–创建用户列表故事

当我们将用户传递到组件中时，让我们添加一个`UserList`故事。我们可以使用从

导入到`__test__ /components/UserList.test.jsx`
的相同夹具文件

```
// src/stories/UserList.jsx

import React from "react";
import { storiesOf } from "@storybook/react";

import UserList from "components/UserList";
import reducedUsers from " __fixtures__ /reducedUsers";

storiesOf("UserList", module).add("with users", () => (
  <UserList users={reducedUsers} />
)); 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要更新我们的`src/stories/index.js`文件来要求我们的新故事。让我们删除欢迎故事，只加载新的`UserList`故事。

```
require("./UserList"); 
```

Enter fullscreen mode Exit fullscreen mode

故事书网页应该会自动重新加载，我们应该会看到:

 [![](../Images/840b163cc5da0fb78d9e9f4e859ee3ed.png)

T6】](http://dmohq.com/wp-content/uploads/2018/07/user-list-story.png)

这不是一个艺术成就，但我们现在有了一个我们的表格看起来像什么的想法，而不必在我们的应用程序中渲染它。

### 第 3 步–用故事镜头创建故事书生成的快照

除了用我们的测试生成我们的快照，我们还可以用我们的故事呈现的内容来生成它们。这是非常可取的，因为您是基于组件的外观来捕获组件的，而不仅仅是基于它的程序结构。要做到这一点，让我们添加故事镜头。

`npm install --save-dev @storybook/addon-storyshots`或`yarn add -D @storybook/addon-storyshots`

接下来，我们创建一个测试来运行 Storyshots

```
// src/ __tests__ /Storyshots.test.js

import initStoryshots from '@storybook/addon-storyshots';

initStoryshots(); 
```

Enter fullscreen mode Exit fullscreen mode

接下来运行我们的测试，一个新的快照将在我们的`__tests__ / __snapshots__`目录中创建。此时，您可以删除`UserStory`组件的原始快照，因为它呈现了一个相同的结构。

### 总结

故事书是在一个隔离的沙盒中查看组件的好方法。使用 Storyshots，无论您何时运行测试套件，同一个视图都可以生成快照测试。