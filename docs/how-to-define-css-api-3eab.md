# 如何定义 CSS API

> 原文：<https://dev.to/clabuxd/how-to-define-css-api-3eab>

我们**建造积木**到**建造**网络。当您构建一个可分发组件的集合时，您可能必须定义组件 API 并编写文档来与组件消费者共享。在 2018 年，我们有很多方法可以做到这一点，但 CSS 呢？虽然 Javascript 是发展最快的语言，但 CSS 仍然是处理和定义组件风格的最佳——也是唯一的——方法，即使你在 js 中编写它，你也会得到 CSS，没人能阻止你学习它！

有了最新的 CSS 规范，我们可以使用许多令人惊叹的功能来处理我们的风格、行为和响应事物。打个招呼！到 [CSS 自定义属性](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)和 CSS API。

## 定义元素

让我们开始用一个简单的属性绑定来定义我们的`html`组件。

```
<progress my-progress value="50" max="100"></progress> 
```

*你也可以使用类将元素绑定到 CSS*

现在，让我们按照原生 css 嵌套语法为元素添加一些样式:

```
[my-progress] {
  position: relative;
  width: 100%;
  border: none;
  vertical-align: baseline;
  appearance: none;
  overflow: hidden;
  display: block;
  height: 5px;
  border-radius: 10px;
  background: hsl(0, 0%, 97%);
  box-shadow: inset 0 0 0 1px hsl(0, 0%, 89%);

  &::-webkit-progress-bar {
    border-radius: 10px;
    background: hsl(0, 0%, 97%);
    box-shadow: inset 0 0 0 1px hsl(0, 0%, 89%);
  }

  &::-webkit-progress-value {
    border-radius: 10px;
    background: hsl(0, 0%, 89%);
  }

  &::-moz-progress-bar {
    border-radius: 10px;
    background: hsl(0, 0%, 89%);
  }
} 
```

现在我们有了一个漂亮的进度条，使用了原生的 html 元素，但是我们怎样才能允许定制和处理风格，而不使消费者进入一个混乱的类和特性问题呢？

## 自定义属性和 API

在这一点上，我们可以定义一组[自定义属性](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)，让消费者知道他们如何在没有问题的情况下自定义我们的组件，并将其与他们的自定义类集成。

让我们选择之前的 CSS 示例，然后添加一些自定义属性。我们必须定义您希望从“外部”定制/访问的属性，并将它们转换为定制属性。

```
[my-progress] {
  position: relative;
  width: 100%;
  border: none;
  vertical-align: baseline;
  appearance: none;
  overflow: hidden;
  display: block;
  height: var(--progressHeight);
  border-radius: var(--progressBorderRadius);
  background: var(--progressBackground);
  box-shadow: inset 0 0 0 1px var(--progressBorderColor);

  &::-webkit-progress-bar {
    border-radius: var(--progressBorderRadius);
    background: var(--progressBackground);
    box-shadow: inset 0 0 0 1px var(--progressBorderColor);
  }

  &::-webkit-progress-value {
    border-radius: var(--progressBorderRadius);
    background: var(--progressFilledColor);
  }

  &::-moz-progress-bar {
    border-radius: var(--progressBorderRadius);
    background: var(--progressFilledColor);
  }
} 
```

现在我们有了一个很好的方法来定制我们的元素，而不用担心类和冲突的 css 范围，但是我们忘记了一些事情...

如何将内部自定义属性从公共属性中分离出来？

现在我们已经耦合了消费者可以直接操作的 css 属性。如果你想扩展/重构你的组件，而不是每次都做破坏性的改变，或者，如果你不想让消费者知道你在里面用了什么，这不是一个好办法。这个例子的另一个问题是缺少自定义属性回退。

因此，我们可以做得更好，改进我们的代码:

```
[my-progress] {
    --progressBorderColor: var(--my-progress-border-color, hsl(0, 0%, 89%));
    --progressBackground: var(--my-progress-background, hsl(0, 0%, 97%));
    --progressFilledColor: var(--my-progress-filled-color, var(--progressBorderColor));
    --progressHeight: var(--my-progress-height, 5px);
    --progressBorderRadius: var(--my-progress-radius, 10px);

    position: relative;
    width: 100%;
    border: none;
    vertical-align: baseline;
    appearance: none;
    overflow: hidden;
    display: block;
    height: var(--progressHeight);
    border-radius: var(--progressBorderRadius);
    background: var(--progressBackground);
    box-shadow: inset 0 0 0 1px var(--progressBorderColor);

      &::-webkit-progress-bar {
        border-radius: var(--progressBorderRadius);
        background: var(--progressBackground);
        box-shadow: inset 0 0 0 1px var(--progressBorderColor);
      }

      &::-webkit-progress-value {
        border-radius: var(--progressBorderRadius);
        background: var(--progressFilledColor);
      }

      &::-moz-progress-bar {
        border-radius: var(--progressBorderRadius);
        background: var(--progressFilledColor);
      }
} 
```

创建另一个内部层，我们现在可以分离公共和内部 css api，然后公开一组命名空间自定义属性，这些属性将直接应用于元素根标记。我们还可以在一个地方定义属性列表中的回退值，在代码的顶部，而不是分散在代码中。

## 使用和定制

现在我们已经有了我们的组件和他闪亮的 css api，让我们继续看看如何使用它们。

```
progress {
  --my-progress-filled-color: linear-gradient(to right, deeppink, tomato);
}

.mySpecialProgress {
  --my-progress-filled-color: linear-gradient(to right, lightgreen, greenyellow);
} 
```

[https://codepen.io/equinusocio/embed/NygNvG/?theme-id=10535&default-tab=html,css,result%20?height=500&default-tab=result&embed-version=2](https://codepen.io/equinusocio/embed/NygNvG/?theme-id=10535&default-tab=html,css,result%20?height=500&default-tab=result&embed-version=2)

* * *

## 额外的

如果你使用 **Web 组件**和**阴影 DOM** (你应该这样做)你可以使用这个方法在你的组件`shadow root`之外暴露 css 属性，然后使它们可编辑，因为自定义属性穿透了阴影 DOM 边界。
有很多工具可以帮助你使用 web 组件，比如 [Polymer Skeleton](https://github.com/PolymerX/polymer-skeleton) ，这是基于 Polymer 3 构建 PWA 最完整的入门套件。

如果你对这篇文章感兴趣，请查看我的[原生元素](https://github.com/equinusocio/native-elements)项目。它只是处于 WIP 状态，但你已经可以看到一些活的。