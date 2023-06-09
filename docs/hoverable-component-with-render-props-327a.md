# 具有渲染道具的可悬浮组件

> 原文：<https://dev.to/ruiclarateixeira/hoverable-component-with-render-props-327a>

这个星期，我不得不为一个组件实现一些 onHover 行为，但是我想让它可重用，并且不把它作为我的组件状态的一部分。

在过去的 6 个月里，我一直在全职开发 React，所以一直在努力想出一个干净的方法来做这件事。

幸运的是，我也开始看 [@kentcdodds](https://dev.to/kentcdodds) 的[高级反应模式](https://blog.kentcdodds.com/updated-advanced-react-component-patterns-51c34b44b1df)，他解释了如何使用渲染道具来实现这一点。

所以我用一个渲染道具(children)实现了`Hoverable`。然后，我可以将我的现有组件传递给它，它接受一个属性`hovered`，我可以轻松地将它重新用于其他组件。

[![Demo of usage in CodeSandbox](img/d48040c57b3c55196d0fb4b9219819e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q_hW4wkT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/duco99rsp98pm2931t5i.gif)

下面是我如何实现的一个例子。您也可以在这个[代码沙箱](https://codesandbox.io/s/428k7zr2m9)中找到它

使用 Typescript，我不能使用`children`作为道具，所以我使用了`render`道具...字面意思！你可以看到在这个[代码沙盒](https://codesandbox.io/s/wr07w001k)

```
import React from "react";
import ReactDOM from "react-dom";

import "./styles.css";

class Hoverable extends React.Component {
    state = { hovered: false };
    render() {
        return (
            <div
                onMouseEnter={() => this.setState({ hovered: true })}
                onMouseLeave={() => this.setState({ hovered: false })}
            >
                {this.props.children(this.state.hovered)}
            </div>
        );
    }
}

function App() {
    return (
        <Hoverable>
            {hovered => <div>{hovered ?  "🔥" : "🦄"}</div>}
        </Hoverable>
    );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode