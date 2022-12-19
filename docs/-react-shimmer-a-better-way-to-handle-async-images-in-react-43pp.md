# 🌠React-shimmer:React 中处理异步图像的更好方法！

> 原文：<https://dev.to/gokcan/-react-shimmer-a-better-way-to-handle-async-images-in-react-43pp>

# 问题😐

我需要一个自定义图像组件来异步加载 React 中的图像。在尝试了可能的想法后，我想在我的自定义图像组件中应用脸书最初的占位符效果(例如[微光](https://github.com/facebook/Shimmer))。挑战在于:它还必须能很好地适应**不同的图像尺寸**和**比例**。

# 介绍解🧠

`react-shimmer`是一个强大的，**高度可定制的**反应`<img>`组件，模拟微光🌠加载图像时的效果。(零依赖！).

*   完全用下一代 JS 编写。
*   完全零依赖！只有几千字节。
*   动画持续时间，延迟和颜色选项可以微调。
*   与默认`<img>`相比，更好的错误处理。
*   不同长宽比和尺寸的微光效果感觉相同。
*   (P.S .寻找新贡献者，随时 ping 我！)

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png) [戈克灿](https://github.com/gokcan) / [反应-微光](https://github.com/gokcan/react-shimmer)

### 🌠React.js 的异步加载、高性能图像组件

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Logo](../Images/9f77ca42e381ffe3b293405534d134c9.png)T2】](https://camo.githubusercontent.com/f4bc0d165384e2fb85029768b0204622f7e9bc0a/68747470733a2f2f63646e2e7261776769742e636f6d2f676f6b63616e2f72656163742d7368696d6d65722f6d61737465722f6d656469612f6c6f676f2e706e67)

> 一个强大的、可定制的、类似悬疑的`<img>`组件，在**加载**时(可选)模拟一个 [**微光**](https://github.com/facebook/Shimmer) 效果。(零依赖！).

[![NPM](../Images/3e10ce21108546d84afbfc65e9c37372.png)](https://www.npmjs.com/package/react-shimmer)[![JavaScript Style Guide](../Images/dff6a929f64c70517ecb251810e37f0b.png)](https://standardjs.com)[![Github Actions CI Status](../Images/a3c752dac0f0fcb3db453077167528ea.png)](https://github.com/gokcan/react-shimmer/actions?query=workflow%3A%22Node.js+CI%22)[![Maintainability](../Images/ef67d032125fb8f4a236dab64de40ad4.png)T11】](https://codeclimate.com/github/gokcan/react-shimmer/maintainability)

[![Header](../Images/de8c844e360a9bce98cb855d774b794b.png)T2】](https://camo.githubusercontent.com/26b5a0815731fc9d282a9299af0f922567232e15/68747470733a2f2f63646e2e7261776769742e636f6d2f676f6b63616e2f72656163742d7368696d6d65722f6d61737465722f6d656469612f6865616465722e706e67)

### [**现场试玩**](https://codesandbox.io/s/nh9x1)

[![](../Images/45a1d79562b775e730ee025f833cc015.png)T2】](https://camo.githubusercontent.com/92f836c3865f034ba3d5df879c43cf4d0facc3ff/68747470733a2f2f63646e2e7261776769742e636f6d2f676f6b63616e2f72656163742d7368696d6d65722f6d61737465722f6d656469612f64656d6f2e676966)

## 安装

```
npm i react-shimmer
```

或者

```
yarn add react-shimmer
```

## 使用

```
import React from 'react'
import Image, { Shimmer } from 'react-shimmer'
function App() {
  return (
    <div&gt
      <Image
        src='https://source.unsplash.com/random/800x600'
        fallback={<Shimmer width={800} height={600} />}
      /&gt
    </div>
  )
}
```

或者您可以使用自定义 React 组件作为后备:

```
import React from 'react'
import Image from 'react-shimmer'

import Spinner from './Spinner'

function App(props) {
  return (
    <div>
      <Image
        src="https://example.com/test.jpg"
        fallback={<Spinner />}
      />
    </div>
  )
}
```

### 性能

| 财产 | 类型 | 需要 | 缺省值 |
| :-- | :-- | :-- | :-- |

…</article>

[View on GitHub](https://github.com/gokcan/react-shimmer)

[![react-shimmer](../Images/2fd11e5c20652c29de2713b8487263e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7oH8_JZt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h47vgbiq6qbc8o2yvr4e.png)

*   你也可以在推特上联系我。