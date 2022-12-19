# 重置错误边界错误状态

> 原文：<https://dev.to/dance2die/resetting-error-boundary-error-state-125k>

对于 [bunpkg](https://bunpkg.com) ，我使用一个向导组件来显示生成 [UNPKG](https://unpkg.com) 链接的一系列步骤。

当对 Web API 的请求失败时，一个错误被一个[错误边界](https://reactjs.org/docs/error-boundaries.html)组件捕获，并在`ErrorBoundary.FallbackComponent`中显示一条错误消息(来自 [react-error-boundary](https://github.com/bvaughn/react-error-boundary) ，一个简单但更好的实现，可在 [React 文档](https://reactjs.org/docs/error-boundaries.html)中找到)。

但是当用户移动到向导中的不同步骤时，错误边界不会被重置。

我想分享我失败的尝试和重置错误边界组件的正确解决方法。

但是这可以应用到你的自定义 ErrorBoundary 组件。

## 😪TL；速度三角形定位法(dead reckoning)

更新错误边界键属性，让 React 重置错误状态。

## ℹ讲述 Demo

下面的演示有一个组件，它随机抛出一个错误，错误边界显示捕获的错误消息。

***信用:*** *演示程序由 [Brian Vaugn](https://twitter.com/brian_d_vaughn/) 在 [CodeSandBox](https://codesandbox.io/s/k9znx9kj9r) 上创建。*

<figure>[![Error Boundary Reset Demo](../Images/7b7cd96132e5666dd38e03d10b5e58d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ig2Iqvuw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i2.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/09/demo.gif%3Fw%3D1170%26ssl%3D1) 

<figcaption>错误边界重置演示</figcaption>

</figure>

## 🙅‍♂️第一次尝试(错误的解决方法)

`react-error-boundary`只提供[跟随道具](https://github.com/bvaughn/react-error-boundary/blob/master/src/ErrorBoundary.js#L8)(省略`children`)，无法清除被捕获的错误。

*   `FallbackComponent`–出错时显示的组件
*   `onError`–出错时触发的回调

[跟随](https://github.com/bvaughn/react-error-boundary/blob/master/src/ErrorBoundary.js#L46)就是`ErrorBoundary.render`是如何实现的。