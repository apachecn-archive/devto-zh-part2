# 我如何发布我的第一个 npm 模块

> 原文：<https://dev.to/chandrasekarg/how-i-published-my-first-npm-module-foc>

[![](img/8d5f9ffc97c8f773f6ed6ea9a5c695df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NHwe2KBA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ABdlffvrMtr-f-3Z4NGhMhw.png)

发布一个 **npm 模块**将会是大多数初露头角的 javascript 开发者的清单。毕竟，我们自己会使用大量的 npm 模块，为我们节省数百个开发时间，让我们的生活变得更加轻松。显而易见，我们应该考虑回报我们所欠下的社区。

说真的，出版一本还是比较简单的。但是得到一个对社区有用的想法(至少在理论上；)相对更难。对于我们能想到的大多数问题，已经存在一个 npm 模块。事实上，就像苹果公司如何注册“**有一个应用程序用于那个**”一样，Npmjs 可以申请“**有一个 npm 模块用于那个**”的商标。

回到实际的故事…几周前，我正在开发一个 react 本地应用程序。我必须开发一个自定义组件，这将使我能够搜索一个`FlatList`，因为没有内置的搜索支持。然后，将它作为 npm 模块发布的想法困扰着我。因为最后，我有了一些对面临类似问题的人有点帮助的东西。

所以，我开始让这个组件更加通用，并且增加了对`SectionList`的支持。然后我开始探索释放这个包的方法。

第一步是使用`npm init`初始化一个 npm 项目，并为其提供必要的信息。

下一步是确定我们的包将依赖的模块。对我来说，只有`react`和`react-native`。但是因为任何使用这个 npm 模块的人都会在他们的 react-native 项目中这样做，他们必须已经安装了`react-native`。因此，这些必须添加到**对等依赖**而不是**依赖**。除了这些，我不需要使用任何其他模块，因为问题相对简单。

然后，我们将不得不担心我们的模块版本，以便我们可以发布错误修复，功能更新..轻松地。标准是遵循**语义版本化。**

[![](img/65f00325f31042cc7b94d75b4344fcbc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PS4EJX48--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A64X80QhKOUWDCpm-lRVaqg.png)

做 **bug 修复**时**补丁**版本必须递增。**次要**版本，我们在不破坏 API 的情况下做了一些次要的功能**增强**。**主要**版本变更发生在我们对模块进行**重大变更**的时候。

下一步是组织我们的代码。入口点将是我们在 package.json 中指定的入口点。默认情况下，它将是 **index.js.** 我们可以用任何我们认为合适的方式来组织它。

最后一步是准备一个有用的 README.md，这样，那些真正想使用我们的软件包的人就放心了。

一旦一切就绪，我们将不得不检查我们的包是否真的有效。因为它还没有发布，我们必须通过安装来测试它——给它文件路径而不是包的名称。类似于`npm install <file-path>`的东西会达到目的。

如果一切正常，我们就可以把它发布给 npm 了。如果我们已经有了 npmjs 的账户，就像`npm login && npm publish`一样简单。

下面是 npmjs 中模块的[链接](https://www.npmjs.com/package/react-native-searchable-list)。

我还进一步创建了一个 react-native 应用程序来演示这个模块的功能。这个应用程序对我的文档工作也有很大帮助。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [钱德拉塞卡-G](https://github.com/Chandrasekar-G)/[rnsearcablelistdemo](https://github.com/Chandrasekar-G/RNSearchableListDemo)

<article class="markdown-body entry-content p-5" itemprop="text">

# RNSearchableListDemo

react 本地应用程序演示`react-native-searchable-list`的特性

这里有 [github 回购](https://github.com/Chandrasekar-G/react-native-searchable-list)和 [npm 页面](https://www.npmjs.com/package/react-native-searchable-list)

## 设置

```
git clone https://github.com/Chandrasekar-G/RNSearchableListDemo.git
cd RNSearchableListDemo
react-native run-ios 
```

[![Flat List](img/8bf1c3c11445dc97bd2c7b6da44aed85.png)T2】](https://github.com/Chandrasekar-G/RNSearchableListDemo/blob/master/Assets/FlatList-1.gif)

[![Flat List](img/23cc086d4892405f9e1022cc585d27b1.png)T2】](https://github.com/Chandrasekar-G/RNSearchableListDemo/blob/master/Assets/FlatList-2.gif)

[![Flat List](img/996b08067f0f843a7f5471994d64e391.png)T2】](https://github.com/Chandrasekar-G/RNSearchableListDemo/blob/master/Assets/FlatList-3.gif)

[![Flat List](img/239da308b6ea78ecb6817fc2d3b28ba7.png)T2】](https://github.com/Chandrasekar-G/RNSearchableListDemo/blob/master/Assets/SectionList-1.gif)

[![Flat List](img/3c5d1574a02001983c3ee6020cba8841.png)T2】](https://github.com/Chandrasekar-G/RNSearchableListDemo/blob/master/Assets/SectionList-2.gif)

[![Flat List](img/e5d8e0a57a15c680455cf3879e389e83.png)T2】](https://github.com/Chandrasekar-G/RNSearchableListDemo/blob/master/Assets/SectionList-3.gif)

</article>

[View on GitHub](https://github.com/Chandrasekar-G/RNSearchableListDemo)

**待办事项**

1.  我将不得不使用 jest 编写单元测试。我开始用 jest 快照测试我的组件。但是我只是想确定我写的单元测试实际上是有用的，而不是为了有用而写。
2.  一旦 jest 测试准备就绪，添加一个 CI，最好是 Travis。
3.  增加了对高亮显示匹配搜索项的文本的支持。

请随意试用本模块。欢迎任何建设性的反馈/拉动请求:)

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [钱德拉塞卡-G](https://github.com/Chandrasekar-G)/[react-native-searchable-list](https://github.com/Chandrasekar-G/react-native-searchable-list)

### 具有搜索功能的 FlatList 和 SectionList 的包装器。

<article class="markdown-body entry-content p-5" itemprop="text">

# react-本地可搜索列表

React Native FlatList 和 SectionList 的强大包装器提供了内置的搜索功能`react-native-searchable-list`,旨在简化 React Native 的 FlatList 和 SectionList 组件，并为其提供搜索功能。

### 装置

```
npm i react-native-searchable-list --save
```

### 1.可搜索平面列表

#### 应用程序接口

| 小道具 | 描述 | 数据类型 | 是必需的 |
| --- | --- | --- | --- |
| `data` | 平面列表的数据 | `Array` | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">✅</g-emoji> |
| `searchTerm` | 用户输入的搜索项。通常这是一个绑定到文本输入的状态变量 | `String` | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">✅</g-emoji> |
| `searchAttribute` | 在对象数组的情况下要搜索的属性。在简单数组数据的情况下，这将是空的 | `String` | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">❌</g-emoji> |
| `ignoreCase` | 区分大小写/不区分大小写的搜索。默认情况下，这将被设置为 true | `Boolean` | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">❌</g-emoji> |

默认情况下，`SearchableFlatList`也继承了 React Native `FlatList`的所有属性。事实上，你可以使用任何你想在平面列表中使用的道具。

#### 使用

```
import
```

…</article>

[View on GitHub](https://github.com/Chandrasekar-G/react-native-searchable-list)