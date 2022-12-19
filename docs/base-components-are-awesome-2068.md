# 基本组件包括...牛逼？

> 原文：<https://dev.to/mcallegari10/base-components-are-awesome-2068>

在过去的一年半时间里，我一直在与 React 合作，我真的很喜欢它。现在，我开始在 Vue 上走自己的路(真的很神奇的框架，试试吧)。但是我不喜欢 JS 组件方法:构建按钮、输入、文本等“基本组件”的“需要”。

## 我来解释一下

我知道，你可能会问“你在说什么？基础组件棒极了”。是的，他们*可能*很棒，但这就是问题所在。基础组件对于处于这种模式下的应用程序来说非常重要**，但是**做错了，这将是一个非常棘手的问题。

## 选项

让我们来看看按钮的 React 基础组件:

```
const ButtonBase = props =>
  <button
    type={props.type}
    className={props.styles}
    onClick={props.handleClick}
    disabled={props.disabled}
  >
    {children}
  </button>

ButtonBase.defaultProps = {
  type: 'button',
  disabled: false
};

ButtonBase.propTypes = {
  type: PropTypes.string,
  disabled: PropTypes.bool,
  styles: PropTypes.string.isRequired,
  handleClick: PropTypes.function.isRequired
};

export default Button; 
```

Enter fullscreen mode Exit fullscreen mode

这并不是真的那么糟糕，但也不是真的完整，总会有一个用例或一些场景需要你改变组件的行为。

但是，假设你去做，你不在乎额外的工作或需要改变它。让我们看看你如何使用这个组件:

```
<ButtonBase styles="main-button" handleClick={someHandlingMethod}>
  Do something
</ButtonBase> 
```

Enter fullscreen mode Exit fullscreen mode

等等！我想我以前见过这个代码

```
<button type="button" className="main-button" onClick={someHandlingMethod}>
  Do something
</button> 
```

Enter fullscreen mode Exit fullscreen mode

完全相同的事情可以用“本地组件”来完成。我说本机组件是因为你必须记住，在 React 的情况下，JSX 只是那些真正丑陋的`React.createElement`函数的语法糖。所以实际上你只是包装了一个 React 已经知道的函数。文本、输入和 React 已经提供的所有原生元素也是如此。

## 这有错吗？

不，一点也不。如果你认为值得花时间，那就去做吧。但是它不会在你需要的时候给你额外的帮助，而且如果你和一个团队一起工作，它可能会减慢每个人努力得到一个好的组件的速度。

## 另辟蹊径

为什么要创建基本按钮组件？因为你有很多不同的按钮(在这种情况下，按钮可以是文本、输入等。)，而且你觉得你是在用那些原生元素重复代码。我已经说过这是一个错误的前提，所以唯一的问题是，那些真的是“不同的按钮”吗？

我真的没有想到不同类型的按钮，那些不是`button`、`submit`就是`reset`，所以区别只是在样式上。

如果你问我，我总是去亲吻。我真正喜欢的方法是使用这些原生元素，就好像你在编写纯 HTML，用 CSS/SASS 制作组件，以保持组件的概念，但同时，不需要重新定义原生元素。

```
%button-base {
  border-radius: 5px;
  height: $button-height;
  font-size: $font-normal;
  width: $button-width;
}

.main-button {
  @extend %button-base;
  background-color: $white;
  border: 1px solid $light-blue;
  color: $light-blue;
} 
```

Enter fullscreen mode Exit fullscreen mode

这些可能不会马上看起来像组件，但考虑到可重用性，CSS/SASS 是很棒的，会为您提供很好的服务。

## 结论？

我真的不认为有结论。但是我确实认为基础组件需要很长时间才能达到满足所有场景的完成点。如果你没有那么多时间，或者像我一样，觉得那些组件不是真正需要的，你应该避免构建它们，并解释为什么。相反，我会选择 CSS/SASS 组件，并保留 React 已经提供给我们的“本地元素”。

我想知道你是怎么想的！如果你对此有其他想法，请在评论中留下:)