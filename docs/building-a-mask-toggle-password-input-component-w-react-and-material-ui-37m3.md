# 使用 React 和 Material UI 构建“Mask Toggle”密码输入组件

> 原文：<https://dev.to/sreisner/building-a-mask-toggle-password-input-component-w-react-and-material-ui-37m3>

众所周知，允许用户切换密码输入可见性消除了摩擦并改善了用户体验。我将在本教程中向你展示如何构建一个可切换的密码输入！

[![](../Images/af9878bc0a04e08c8bb28bd6a283f2db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xiz5AT36--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/497/1%2AHOz8wr_pxG5OIrZKL_rFrQ.gif)

### 设置/依赖关系

本文假设你已经有了一个 React 应用，并安装了 **@material-ui/core 1.0.0+** 和 **@material-ui/icons 1.0.0+** 作为依赖项。如果你需要这方面的帮助，从这里的[开始](https://reactjs.org/docs/add-react-to-a-new-app.html)，然后到这里的。

### 构建组件

首先，我们从呈现材质 UI 文本字段的 **PasswordInput** 类组件开始。