# 带有 stencil . js-decorator 的组件库

> 原文：<https://dev.to/johnbwoodruff/component-libraries-with-stenciljs---decorators-1b9c>

*这是关于使用 Stencil.js 创建 web 组件库的系列文章的第五篇——请看第一篇文章*

至此，我们已经构建了一个相当基本的组件，看起来很棒，功能也正常。我们现在将转向一个具有更复杂功能和交互的新组件。选项卡是每个图书馆都需要的基本组件之一。每个选项卡实际上是一个按钮，但这些按钮一起工作，并保持一个共享状态，即哪个选项卡当前是活动的。这是我们下一个组件的完美候选，因为它可以使用[decorator](https://stenciljs.com/docs/decorators#decorators)模板提供的所有其他组件。

## 积木

让我们首先创建一个非常简单的组件，没有样式或功能。首先创建一个新的文件夹`tabs`，其中包含三个文件:`tabs.tsx`、`tab.tsx`和`tabs.scss`用于我们的样式。让我们从构建块选项卡组件开始。

这个会有点不一样。它将是一个组件，但没有呈现功能。相反，它会为我们跟踪几个道具。将以下内容添加到您的`tab.tsx`文件中:

```
@Component({
  tag: 'mtn-tab',
  shadow: true
})
export class Tab {
  @Prop() label: string;

  @Prop() active: boolean;

  @Prop({ reflectToAttr: true }) disabled: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经为标签准备了道具，标签将会显示这些道具，一个显示标签是否活动的标志，当然还有一个禁用的属性。如果我们想保存更多与每个选项卡相关的元数据，我们可以在这里添加它。

现在让我们深入研究一下`tabs.tsx`中的主要标签组件。我们首先需要呈现组件的基本结构。先说以下:

```
export class Tabs {
  render() {
    const classMap = this.getCssClassMap();

    return (
      <div class={classMap}>
        <button role="tab">
          Tab 1
        </button>
        <button role="tab">
          Tab 2
        </button>
        <button role="tab">
          Tab 3
        </button>
      </div>
    );
  }

  private getCssClassMap(): CssClassMap {
    return {
      'tabs-list': true
    };
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们也为我们的主机组件和`.tabs-list`类设置一些非常基本的样式。在我们的`tabs.scss`文件中，我们将放入几个块:

```
@import '../../styles/variables.scss';

:host {
  display: block;
  box-sizing: border-box;
}

.tabs-list {
  border-bottom: 1px solid #dddddd;
  padding: 0 5px;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![ugly tabs](img/b9c5e7fbaac5d82ff2574afb602bb18a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DdRIshr5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/letmyd1b9om047ggsik3.png)

这显然看起来很丑，一点也不像选项卡，但这是一个开始，我们只需要将它们设计得更好一点。我们再加几个简单的款式，让它们看起来很美。

让我们给每个按钮一个`.tab-button`类，给第一个按钮一个`.active`类。现在为类定义:

```
.tab-button {
  border: 0;
  font-size: 14px;
  font-family: $font-family;
  padding: 8px;
  outline: none;
  cursor: pointer;

  &:hover:not(:disabled):not(.active) {
    border-bottom: 3px solid $light-dark;
  }

  &:disabled {
    opacity: 0.4;
  }

  &.active {
    border-bottom: 3px solid $blue-steel;
    font-weight: 600;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做了一些你可以解决的事情，但是基本上我们移除了按钮的样式，调整了字体，然后在标签激活时在底部添加了一个漂亮的边框。我们还做了一个有趣的小悬停边框，当悬停在一个既没有禁用也没有激活的标签上时，它会变成浅灰色。我们当然也定义了一个被禁用的标签看起来像什么。

[![](img/36e7648641c83985fa6a5018a7597b48.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v5rNc0rw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ffj2pxio671oda9zd78c.png)

现在我们的标签看起来像真正的标签，但它们也是硬编码的。我们希望能够指定我们想要多少选项卡，以及定制他们的标签。这里是我们进入其他装饰者的地方。

## 状态和元素装饰者

首先让我们定义选项卡并动态创建它们。这些选项卡定义了我们如何呈现我们的组件，所以我们将使用`@State()`装饰器:
来定义它们

```
export class Tabs {
  @State()
  tabs: HTMLMtnTabElement[] = [];

  //...
} 
```

Enter fullscreen mode Exit fullscreen mode

`@State()`装饰器用于管理组件内部的数据。对该属性的任何更改都将导致组件重新呈现，因为状态已经更改。

您还会注意到我对选项卡使用了`HTMLMtnTabElement[]`类型。这是一个由你的`components.d.ts`文件中的模板自动创建的接口。它为您的组件提供了一些很好的智能感知，包括标准的 HTML 元素属性和方法，以及您在组件上定义的属性和方法。

对于我们的标签，我们想使用下面的语法来定义它们:

```
<mtn-tabs>
  <mtn-tab active label="Tab 1"></mtn-tab>
  <mtn-tab label="Tab 2"></mtn-tab>
  <mtn-tab label="Tab 3" disabled></mtn-tab>
  <mtn-tab label="Tab 4"></mtn-tab>
</mtn-tabs> 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用`componentWillLoad()`生命周期钩子来抓取我们指定的选项卡并呈现它们。我们还必须使用`@Element()`装饰器，这样我们就可以在我们的方法中引用它。

`@Element()`方法非常简单。它返回组件的主机`HTMLElement`的一个实例。我们想要查询我们自己的组件中的元素，所以我们将在我们的`componentWillLoad()`方法中使用它，比如:

```
export class Tabs {
  // Our host element
  @Element()
  el: HTMLElement;

  @State()
  tabs: HTMLMtnTabElement[] = [];

  componentWillLoad() {
    // Grab tabs from this component
    this.tabs = Array.from(this.el.querySelectorAll('mtn-tab'));
    if (this.tabs.length === 0) {
      throw new Error('[mtn-tabs] Must have at least one tab');
    }
  }
  //...
} 
```

Enter fullscreen mode Exit fullscreen mode

这个代码片段获取所有的`mtn-tab`元素，并将它们粘贴到我们的`tabs`属性中。然后让我们调整我们的`render()`函数来使用动态抓取的标签。

```
export class Tabs {
  // ...

  render() {
    const classMap = this.getCssClassMap();

    return (
      <div class={classMap}>
        {this.tabs.map((tab: HTMLMtnTabElement, index: number) => {
          const tabClassMap: CssClassMap = {
            'tab-button': true,
            active: tab.active
          };

          return (
            <button
              role="tab"
              disabled={tab.disabled}
              class={tabClassMap}
              onClick={() => this.openTab(index)}>
              {tab.label}
            </button>
          );
        })}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您曾经使用过 React，您会认识到我们在这里做什么。请记住，TSX 是打字稿，而不是实际的 HTML，所以我们可以做的事情，如转换数组到其他渲染元素。我们在函数中这样做。我们使用`this.tabs`并使用`map()`函数将这些选项卡转换成我们想要在屏幕上呈现的元素。在这种情况下，我们使用`.tab-button`类来呈现按钮，并且有条件地将`.active`类应用于标记为活动的选项卡。我们确保将禁用属性有条件地分配给那些我们标记为禁用的选项卡。我们还指定了一个点击处理程序`onClick`，现在我们将实现它。

## 方法装饰器

我们希望在选项卡上实现我们的单击处理程序，这样我们就可以在单击时正确地更改选项卡。为此，我们将使用`@Method()`装饰器。这个装饰器在组件的公共 API 上公开类方法。换句话说，您可以简单地通过查询元素并调用它的方法来调用这些方法，就像这样:

```
const tabs = document.querySelector('mtn-tabs');
tabs.openTab(2); 
```

Enter fullscreen mode Exit fullscreen mode

为了保持组件内部的方法，我们想简单地省略掉`@Method()`装饰器。根据我的判断，方法应该保持在内部，除非有充分的理由公开它们。在这个`openTab()`函数的例子中，有明确的理由公开它，所以让我们现在实现它。根据文档，[公共方法必须是异步的](https://stenciljs.com/docs/methods#public-methods-must-be-async)，所以我们确保将其标记为异步的。

```
export class Tabs {
  // ...

  @Method()
  async openTab(index: number) {
    if (index >= this.tabs.length) {
      throw new Error(
        `[mtn-tabs] Index ${index} is out of bounds of tabs length`
      );
    }
    if (!this.tabs[index].disabled) {
      this.tabs = this.tabs.map((tab, i) => {
        tab.active = i === index;
        return tab;
      });
    }
  }

  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们首先确保请求的索引存在。我们无法选择不存在的选项卡。然后，我们检查被点击的选项卡没有被禁用。我们不希望禁用的标签有任何功能。否则，我们改变每个选项卡上的`active`标志，这取决于选项卡的索引是否与我们传递给方法的索引相匹配。如果匹配，我们将`active`设置为`true`，否则我们将它设置为`false`。

如果你保存并检查，你的标签现在会改变，当你点击它们！他们开始按照我们希望的方式工作。然而，在完成这个组件之前，我们还想做一些事情。

## 事件装饰器

我们在组件的公共 API 上公开了`openTab()`方法。这对于能够通过编程来改变标签页显然是有用的。如果能够确定选项卡何时发生变化并对变化做出反应呢？我们将使用带有`@Event()`装饰器的定制事件。

使用我们的组件，我们可以定义一个`EventEmitter`来发出[自定义 DOM 事件](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events)。使用模板这真的很简单。现在让我们定义我们的方法，并在我们的`openTab()`方法:
中发出更改后的选项卡

```
export class Tabs {
  // ...

  @Event({ eventName: 'change' })
  onChange: EventEmitter;

  @Method()
  openTab(index: number) {
    if (index >= this.tabs.length) {
      throw new Error(
        `[mtn-tabs] Index ${index} is out of bounds of tabs length`
      );
    }
    if (!this.tabs[index].disabled) {
      this.tabs = this.tabs.map((tab, i) => {
        tab.active = i === index;
        return tab;
      });
      this.onChange.emit({ tabId: index });
    }
  }

  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们创建了我们的`onChange`,这是一个从模板中导入的`EventEmitter`。我们还使用装饰器中的`eventName`配置属性更改了它的名称。你可以改变它，如果你想或保持不变，这取决于你的喜好。唯一的建议是保持命名的一致性。

然后我们调用`EventEmitter`上的`emit()`函数，并发出一个带有已更改标签的`tabId`的对象。我们现在可以在这个组件之外监听这个事件，并对其做出反应，如下所示:

```
const tabs = document.querySelector('mtn-tabs');
tabs.addEventListener('change', event => {
  // You'll find your emitted object under event.detail
  console.log(`CHANGED TABS TO INDEX ${event.detail.tabId}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以做一些事情，比如当当前激活的标签改变时交换视图！对于任何好的组件库来说，事件都是非常方便的。我们可以在组件中添加各种事件。然而，对于我们的选项卡，我们将把它留给这一个事件。

## 倾听装饰者

`@Listen()`装饰器与`@Event()`装饰器密切相关。基本上，这是一种监听 DOM 事件并对其做出反应的速记方式。您可以监听自己定义的组件事件，也可以监听标准事件，比如 scroll 事件。它是这样工作的:

```
export class MyComponent {
  @Listen('body:scroll')
  handleScroll(ev) {
    console.log('The body was scrolled', ev);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们没有在 tabs 组件中使用这个装饰器，但是它使用起来非常简单。其他用例可能是一个`keyup`监听器，例如，你可以用它在你的组件上实现 [Konami 代码](https://en.wikipedia.org/wiki/Konami_Code)来解锁一个有趣的复活节彩蛋。

## 手表装饰工

Stencil 的工具箱中还有一个主要的装饰器，那就是`@Watch()`装饰器。它用于监视组件类的特定属性，并修饰一个方法，该方法在该属性被更改时被调用。它调用带有`oldValue`和`newValue`的方法，因此您可以记住其中的任何一个值。我们不会在我们的选项卡组件中使用它，但是如果我们愿意，我们可以使用它。我们可以不在`openTab()`函数中发出新的索引，而是监听 tabs 属性，并从那里发出新激活的选项卡，可能像这样:

```
export class Tabs {
  // ...

  @Watch('tabs')
  tabsChangeHandler(newValue: HTMLMtnTabElement[]) {
    const newIndex = newValue.findIndex(tab => tab.active);
    this.onChange.emit({ tabId: newIndex });
  }

  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个函数中，我们不关心旧值，我们只想返回新标签。这和我们之前做的一样，当标签改变时，它仍然发出新的标签。然而，我们没有在组件中使用这个方法，因为我们必须在每次标签更新时搜索新标签的索引，而在我们的`openTab()`方法中我们知道哪个标签是新标签，所以我们将坚持使用这个方法。这就是你使用`@Watch()`装饰器的方式，它对其他情况和组件也很有用，所以值得放在你的工具箱里。

## 下一步

恭喜你！您的选项卡组件现在应该完全正常工作了！我们有一个改变标签的公共 API，一个我们可以监听和响应的事件，以及帮助我们在需要时适当地重新呈现的内部状态。我们在本文和之前的文章中讨论的装饰者构成了使用模板构建 web 组件的基础。在这个系列中，我们已经介绍了大量的内容，但是还有更多的内容要介绍！到目前为止，开发软件的一个最重要的方面已经消失了，那就是测试你的代码。我们将在下一篇文章中讨论这个问题，很快就来！

*单纯想看最终结果回购？来看看[这里](https://github.com/johnbwoodruff/mountain-ui)T3】*