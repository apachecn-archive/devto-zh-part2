# 发现反应悬念

> 原文：<https://dev.to/topheman/discover-react-suspense-ml>

[https://www.youtube.com/embed/Nj4q2fHulqc](https://www.youtube.com/embed/Nj4q2fHulqc)

**观看截屏【en】**/[观看对话【fr】](http://dev.topheman.com/decouvrir-react-suspense-video-talk-fr/)

随着 React v16 的推出，脸书团队推出了名为“光纤”的新版本内核。多亏了这次完全重写，我们已经看到了像片段、门户和错误边界这样的新特性。其他一些功能仍在开发中:

*   焦虑
*   时间分片

在过去的几个月里，facebook 的工程师们已经对这些功能的可能性做了一些演示。可用的例子很少(在撰写本文时，这些仍然是不稳定的/未记录的 API)。

在 Ryan Florence 在 React 集会上的[演讲](https://youtu.be/X-kA8B2QzjY)呈现悬念以及 [@reach/router](https://github.com/reach/router) 如何为此做好准备之后，我决定做一个小项目:

*   去钻研那些新概念
*   共享代码示例
*   展示一个简单的工具，可以帮助解释使用悬念的好处

### T2】👉试试 top Heman/react-fiber-实验👈

### 悬疑解决什么问题？

*   它允许您在所有数据准备就绪之前开始渲染(可以在数据开始加载时暂停渲染，并在数据完成时继续渲染)
*   它可以让您避免旋转器的级联，这可能会由于无意的重排和重画而导致闪烁
*   代码分割成为一个内置的特性，易于使用(您也可以在加载脚本时暂停渲染)

这意味着:

*   将数据提取移动到更靠近呈现数据的位置——数据封装
*   摆脱加载状态——组件中更干净的状态(在某些用例中甚至可能是无状态的——不再需要 componentDidMount 生命周期挂钩来获取数据)
*   对旋转器进行更精细的颗粒控制(感谢`<Placeholder>`)

### 今天怎么考

悬念仍在积极开发中，请注意 API 可能会改变和不稳定，尽管它足够先进，可以用来破解项目。你需要做出自己的反应。

托弗

这是对 **React 悬念**的简短介绍，通过观看视频和与**[top Heman/React-fiber-experiments](https://react-fiber-experiments.surge.sh/)**一起在线玩游戏来更进一步。

这篇文章最初发表在我的博客上。