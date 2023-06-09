# React-kindness:React 的可定制屏幕指南

> 原文：<https://dev.to/piglovesyou/react-kindness-a-customizable-screen-guide-for-react--3nn0>

我刚刚发布了 React 的一个简单的屏幕向导组件。请看一看。

[👉react-善良演示](https://piglovesyou.github.io/react-kindness/)
[👉react-善良自述](https://github.com/piglovesyou/react-kindness#react-kindness-)

开发的动机是认为使用 SVG mask 和`pointer-events: none`应该可以简单的实现屏幕向导。其他库通常在聚焦元素周围放置 4 个绝对定位的暗`div`,这决定了它不能在覆盖图中绘制圆形斑点。SVG 掩码当然可以。

另一个实现是将目标元素设为`position: relative; z-index: 9999999 !important;`，让用户“触摸”该元素，并用覆盖图`div`隐藏任何其他元素，乍一看这似乎是一个很酷的解决方案。然而，我想知道我们是否必须在屏幕引导期间阻止用户交互。相反，用户可能想点击一个锚链接或点击一个铃铛图标来查看页面上最近的通知，不是吗？

react-kindness 是一个简单的 UI 指南，不会阻碍用户交互。只是在一个覆盖图中显示一个点，一个包含“上一页”和“下一页”按钮的信息面板，就是这样。

下面是一个简单的用法示例。

```
import {KindnessPanel, Kindness} from 'react-kindness';
import 'react-kindness/dist/index.css';

// ...
    <KindnessPanel
        enabled={this.state.show}
        onExit={() => this.setState({show: false})} /> 
```

Enter fullscreen mode Exit fullscreen mode

您可能想要打开/关闭向导，所以保持组件中的状态如上`this.state.show`所示。请不要忘记在一个`onExit`事件挂钩上禁用它。

```
<Kindness>
    <input type="text" {...} /> </Kindness> 
<Kindness message="Click here to submit your post!">
    <button type="submit">
        Submit
    </button> </Kindness> 
```

Enter fullscreen mode Exit fullscreen mode

然后用一个点将跟踪的`<Kindness />`包裹你的元素。其他属性如`title`和`order`也是可用的。请参考[自述文件](https://github.com/piglovesyou/react-kindness#react-kindness-)获取文档。

关于屏蔽用户交互，如果你觉得喜欢，可以在`onClickOutside`中返回`false`。react-kindness 会负责为您禁用用户操作。

```
 <KindnessPanel
        // ...
        onClickOutside={() => false} /> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，这是我想尝试的另一件事，React 组件开发的“作为组件的功能”模式。使用它，react-kindness 的面板内容是完全可定制的。

在内部，面板内容被实现为呈现消息和按钮的`<KindnessPanelContent />`。您可以在面板中呈现任意元素，而不是使用默认内容。

```
import Button from '@material-ui/core/Button';

// ...

<KindnessPanel enabled={true}>
    {
        ({message, totalSize, currentIndex, goNext, goPrev}) => (
            <div>
                <h3>{message}</h3>
                <h4>{`You are seeing ${currentIndex + 1} of ${totalSize} in the guide.`}</h4>
                <Button action={goPrev}>Go previous</Button>
                <Button action={goNext}>Go next</Button>
            </div>
        )
    }
</KindnessPanel> 
```

Enter fullscreen mode Exit fullscreen mode

此外，面板和聚光灯的样式可以根据您的意愿进行修改。

最后，我想说我很欣赏 react-kind 非常依赖的一个项目 [Popper.js](https://popper.js.org/popper-documentation.html) 。它的 DOM 定位功能全面而复杂，因此我可以轻松地阅读和跟踪代码，并利用 Popper.js 为像我这样的外部开发人员导出的函数。在`modifiers`中一步一步修改定位的架构很酷，而且可扩展。如果你面临 DOM 定位的问题，试试 [Popper.js](https://popper.js.org/popper-documentation.html) 👍