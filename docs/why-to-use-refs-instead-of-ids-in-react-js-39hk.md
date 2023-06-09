# 为什么在 React JS 中使用 refs 而不是 id

> 原文：<https://dev.to/skptricks/why-to-use-refs-instead-of-ids-in-react-js-39hk>

帖子链接:[https://www . skptricks . com/2018/06/why-to-use-refs-instead-of-ids . html](https://www.skptricks.com/2018/06/why-to-use-refs-instead-of-ids.html)

让我们从 react js 中的 ID 属性开始，这里我们使用 ID 属性来访问 react 组件中的一个元素。实际上，在这个 IdComponent 组件中，我们正在执行以下操作:
当用户关注文本字段时，它会将边框颜色改为蓝色，背景颜色改为浅黑色。
当用户焦点离开文本字段时，它会将边框颜色和边框颜色重置为初始状态。

[为什么在 React JS 中使用 refs 而不是 id](https://www.skptricks.com/2018/06/why-to-use-refs-instead-of-ids.html)

[![](img/637a4dc84410c77d671e85cf768428a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lSIotZEq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3.bp.blogspot.com/-HKgUdqbVmE4/WyUTkluy0tI/AAAAAAAABn0/ZDTMz41XKnIXGJgGT92oHROXCzUxNpI_gCLcBGAs/s400/ref.jpg)

可重用性问题
当你不止一次使用同一个组件时，你会遇到的问题。当您多次尝试创建同一个组件时，这里有一个关于 ID 属性可重用性问题的演示。点击输入字段，看看会发生什么。这里我们创建了多个具有相同 ID 的对象。

解决方案是 Ref
这个组件使用 Ref 属性来克服上述问题。