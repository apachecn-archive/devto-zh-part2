# Stencil.js 组件库——更深入

> 原文：<https://dev.to/johnbwoodruff/component-libraries-with-stenciljs---going-deeper-5apm>

*这是关于使用 Stencil.js 创建 web 组件库的系列文章的第四篇——请看第一篇文章*

在上一篇文章中，我们创建了一个非常简单的按钮组件。我们现在将扩展它，增加更多选项和功能，使其功能更加全面。我个人希望这个按钮有几个颜色选项，几个按钮形状和几个尺寸。

## 组件道具

首先，我们需要一些道具来允许用户指定他们想要什么。让我们称我们的道具为`color`、`shape`和`size`。我们也可以有多种按钮类型，如提交和重置，就像一个普通的 HTML 按钮一样，所以我们也将添加它作为一个道具。

```
@Prop()
type: 'button' | 'reset' | 'submit' = 'button';

@Prop()
color: 'primary' | 'accent' | 'light' = 'primary';

@Prop()
shape: 'square' | 'round' = 'square';

@Prop()
size: 'small' | 'default' | 'large' = 'default'; 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前很少/没有使用过 TypeScript，你会注意到，我没有为这些属性定义类型为类型`string`，从技术上来说它们是类型`string`，而是更进一步，定义每个属性可以是几个特定字符串中的一个。当您使用该组件时，这允许更有效的智能感知。我预先定义了一些我将要实现的特定大小、颜色、形状和类型。最重要的是，我已经将这些属性初始化为我喜欢的默认值，这样，如果您只是调用我的按钮组件而没有定义属性，它看起来就像一个默认按钮。

## 类地图

接下来，我将使用这些道具来构建 CSS 类的映射，以应用于我的组件。为此，我通常喜欢定义一个名为`CssClassMap`的接口。让我们在`src/utils/interfaces.ts`创建这个文件，因为我将在我的所有组件中重用它，并为文件使用以下内容:

```
export type CssClassMap = { [className: string]: boolean }; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我将这个类映射类型定义为一个键、类名和值为布尔值的映射。实际上，我将指定我是否想要应用类。现在我们有了这个接口，让我们写一个在 render 上构建这个类映射的方法。

```
export class Button {
  // ...

  render() {
    const classMap = this.getCssClassMap();

    return (
      <button type={this.type} class={classMap} disabled={this.disabled}>
        <slot />
      </button>
    );
  }

  private getCssClassMap(): CssClassMap {
    return {
      [this.color]: true,
      [this.shape]: true,
      [this.size]: true
    };
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我从 render 方法中调用我们的函数来获取类别映射，在本例中是一个相当简单的映射，并将其应用于`button`。你可能也注意到了，我也将`type` prop 传递给了按钮。现在我们已经应用了正确的类，让我们实际实现这些类。首先我们需要正确的颜色，所以让我们添加一些 SCSS 变量到我们的`src/styles/variables.scss`文件中。

```
//...

// Brand Colors
$blue-steel: #4571c4;
$blue-steel-dark: #315db0;
$coral: #c75943;
$coral-dark: #a83a24;
$light: #f0f1f2;
$light-dark: #e2e3e4; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了可用的变量，让我们实现类本身。我们不需要实现默认类，因为它们是我们已经实现的默认类。

```
button {
  //...

  // Color variations
  &.accent {
    background-color: $coral;

    &:hover {
      background-color: $coral-dark;
    }
    &:active {
      background-color: darken($coral-dark, 10%);
    }
  }
  &.light {
    background-color: $light;
    color: rgba(0, 0, 0, 0.7);

    &:hover {
      background-color: $light-dark;
    }
    &:active {
      background-color: darken($light-dark, 10%);
    }
  }

  // Shape variations
  &.round {
    border-radius: 50px;
  }

  // Size variations
  &.small {
    padding: 2px 8px;
    font-size: 12px;
  }
  &.large {
    padding: 8px 20px;
    font-size: 16px;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于颜色变化类，我们需要重新实现我们的`hover`和`active`状态。我们不需要重新实现我们的`disabled`状态，因为我们现有的状态只是应用了较低的不透明度，在任何颜色下看起来都很棒。把按钮做成圆形是很简单的，然后我们最后调整按钮的`padding`和`font-size`，使其看起来像一个大的或小的按钮。

现在我们已经实现了这些类，我们有了一些看起来非常不可思议的按钮 web 组件！

[![buttons](../Images/3b7e2bf7b9808553c52a14bf6289d023.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9ggSMouO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m0yrzxl4jfk994sj6a7b.png)

## 下一步

我们现在有了一个很棒的按钮组件，可以在任何地方使用！这是一个简单的第一个组件，所以我们不需要处理很多其他的装饰者，比如`@Watch()`、`@Method()`或者`@State()`。在我们的下一篇文章中，我们将讨论那些带有更复杂的新组件的问题。下一集再见！

*单纯想看最终结果回购？来看看[这里](https://github.com/johnbwoodruff/mountain-ui)T3】*