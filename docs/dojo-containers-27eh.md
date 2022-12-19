# Dojo 容器

> 原文：<https://dev.to/odoenet/dojo-containers-27eh>

一旦您开始构建开始组合多个小部件的应用程序，并且您试图跨这些小部件管理状态，您可能想要开始查看 [Dojo 容器](https://github.com/dojo/framework/blob/master/src/widget-core/README.md#containers--injectors)。容器允许您将值注入到小部件属性中，而不必将状态直接导入到小部件中。

为此，Dojo 提供了一个更高阶的组件，类似于您可能使用的 [React](https://reactjs.org/docs/higher-order-components.html) 。那个 HOC 位于`@dojo/framework/widget-core/Container`。

假设您想使用流式 API，并在流返回新数据时更新您的小部件。我们希望在一个简单的列表中显示这些数据。

```
// src/widgets/Items.ts
export class Items extends WidgetBase<ItemsProperties> {
  protected render() {
    const { items } = this.properties;
    return v(
      "ul",
      { classes: css.root },
      items.map((x, idx) =>
        v("li", { innerHTML: x.name, key: `${x.name}-${idx}` })
      )
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个小部件在属性中有一个`items`数组。您可以将这个小部件直接绑定到一个数据存储中，并在新数据进入时更新小部件，但是同样，我们可能希望数据在父小部件或其他正在使用的小部件中可用。

让我们创建一个父应用程序容器来呈现这个小部件。

```
// src/containers/AppContainer.ts
class AppContainer extends WidgetBase<ItemsProperties> {
  protected render() {
    return v("div", {}, [w(Items, { items: this.properties.items })]);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个特定的容器除了将其属性传递给 child Items 小部件之外，没有做太多事情。

为了使用 Dojo 容器，我们需要创建一个`getProperties`函数来定义返回给容器的属性。

```
// src/containers/AppContainer.ts
function getProperties(inject: Context, properties: any) {
  const { items } = inject;
  return { items };
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在 Dojo 容器中包装我们的`AppContainer`。

```
// src/containers/AppContainer.ts
export default Container(AppContainer, "state", { getProperties }); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`"state"`是我为我的上下文提供的名称，我称之为我的注入器，因为它允许我将值注入到我的小部件中。

此时，您可以选择如何管理您的状态。您可以使用 [Dojo stores](https://github.com/dojo/framework/tree/master/src/stores) 或者您可以创建一个接受`invalidator`的类，并且您可以使用这个`invalidator`让高阶组件知道状态已经改变，并且它将把它传递给它所包装的小部件。

现在，让我们使用一个接受无效化器的类，称它为容器的上下文。我们可以在另一篇文章中讨论道场商店。

```
// src/context.ts
export default class Context {
  private _items: Item[];

  private _invalidator: () => void;

  constructor(invalidator: () => {}, items: Item[] = []) {
    this._items = items;
    this._invalidator = invalidator;
    // subscribe to updates from our stream
    stream.subscribe((a: Item) => {
      this._addItem(a);
    });
  }

  get items(): Item[] {
    return this._items;
  }

  private _addItem(item: Item) {
    this._items = [...this._items, item];
    // call the invalidator to update wrapped container
    this._invalidator();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个`Context`中，我订阅了我的数据流，并在新数据流入时更新了`items`数组。

好了，让我们在启动整个应用程序的`main.ts`中将它们联系在一起。

```
// src/main.ts
const registry = new Registry();
// the `defineInjector` will provider the invalidator
registry.defineInjector("state", (invalidator: () => any) => {
  // create a new context and return it
  const context = new Context(invalidator);
  return () => context;
});

const Projector = ProjectorMixin(AppContainer);
const projector = new Projector();
// pass the registry to the projector
projector.setProperties({ registry });

projector.append(); 
```

Enter fullscreen mode Exit fullscreen mode

当注册表被传递到投影仪时，它将确保一切都按照需要连接起来。

这看起来像是几个步骤，但是它使得小部件中的状态管理非常灵活，而不必将小部件绑定到数据源，这使得它们非常可重用。

你可以为应用程序中的每个小部件创建容器，并独立管理它们的状态，这将是非常强大的！

你可以在上面的 [CodeSandbox](https://codesandbox.io/embed/j31zl4mwvy) 中看到这个应用的例子。

请务必[订阅时事通讯](https://learn-dojo.com/sign-up/)，了解最新内容！