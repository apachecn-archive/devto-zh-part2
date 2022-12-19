# 反应:不是所有事情都是确定的

> 原文：<https://dev.to/ganderzz/react-not-everything-is-state-36hl>

[快速提示]

当使用 React 时，我们引入了状态作为存储组件数据的一种方式。还有一种方式通常会被忽略:类属性。

#### 举例

```
class AnnoyingButton extends React.Component {
    intervalId = null; // Here!
    id = generateUniqueId(); // Here!

    state = {
        isDisabled: false
    };

    componentDidMount() {
        this.intervalId = setInterval(() => {
            this.setState(({ isDisabled }) => ({
                isDisabled: !isDisabled
            }));
        }, 100);
    }

    componentWillUnmount() {
        console.log(`Unmounting ID: ${this.id}`);
        clearInterval(this.intervalId);
    }

    render() {
        return <button disabled={this.state.isDisabled} />;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

组件没什么特别的；有趣的是`id`和`intervalId`变量。当组件卸载时，我们需要一些方法来防止内存泄漏。在 componentDidMount 中，我们将间隔的 ID 保存到一个类属性中。然后，使用属性清除间隔和`console.log`组件的唯一 ID。

您可能会想，“为什么我们不将这些信息存储在组件的状态中呢？”当状态改变时，React 重新呈现组件和任何子组件。如果 render 方法从不使用状态的一部分，它可能会导致不必要的渲染——使您的应用程序变慢！

**何时使用类属性:**如果状态需要被存储，但是从来没有被用作组件的视图/呈现的一部分。