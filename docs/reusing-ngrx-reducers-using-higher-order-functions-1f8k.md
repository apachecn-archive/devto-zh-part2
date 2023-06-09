# 使用高阶函数重复使用 ngrx 减速器

> 原文：<https://dev.to/joanllenas/reusing-ngrx-reducers-using-higher-order-functions-1f8k>

Reducers 是纯函数，它指定应用程序的状态如何改变，以响应发送到存储的操作。

通常，store 键和 reducer 之间是一对一的关系，但是当您想要在多个地方使用相同的 reducer 逻辑时会发生什么呢？

# 高阶函数

那么，什么是高阶函数呢？

> 对其他函数进行操作的函数，无论是将它们作为参数还是返回它们，都被称为高阶函数。 <small>( [来源](https://eloquentjavascript.net/05_higher_order.html#p_cao2fH68Tj) )</small>

这没什么了不起的，但是这条语句使得这些函数很特别:

> 高阶函数允许我们抽象动作，而不仅仅是值。 <small>( [来源](https://eloquentjavascript.net/05_higher_order.html#p_+cgNTV2i2y) )</small>

高阶函数允许你声明东西是什么，而不是定义改变某种状态的步骤。虽然高阶函数的组合会带来语义过载，但对值的顺序操作会带来认知过载。你选一个。

# HoF 和减速器

高阶归约器是返回(配置的)归约器的函数。

高阶减速器允许我们对减速器接受的动作进行参数化。有几种策略可以实现这一点。我们将看看其中的几个，看看每一个的优点和缺点。

首先，我鼓励你看一看我们将要使用的基本(没有高阶缩减器)规范示例:计数器应用程序。( [stackblitz 链接](https://stackblitz.com/edit/angular-vhvefd?file=src/app/app.module.ts)

### 动态创建动作名称

我们的第一个高阶减速器将使用一种简单的技术。我们将通过动态定义接受哪些动作类型来配置缩减器。( [stackblitz 链接](https://stackblitz.com/edit/angular-udcj8y?file=src/app/app.module.ts)

让我们看看修改后的`counter.component.ts`是什么样子:

```
@Component({
  selector: 'counter',
  template: `
    <button (click)="increment()">Increment</button>
    <div>Current Count: {{ count$ | async }}</div>
    <button (click)="decrement()">Decrement</button>

    <button (click)="reset()">Reset Counter</button>
  `,
})
export class CounterComponent implements OnInit {
  @Input() counterName: string;
  count$: Observable<number>;

  constructor(private store: Store<AppState>) {}

  ngOnInit() {
    this.count$ = this.store.pipe(select(this.counterName));
  }

  increment() {
    this.store.dispatch({ type: `${this.counterName}.${INCREMENT}` });
  }

  decrement() {
    this.store.dispatch({ type: `${this.counterName}.${DECREMENT}` });
  }

  reset() {
    this.store.dispatch({ type: `${this.counterName}.${RESET}` });
  }
} 
```

这里的关键变化是我们的组件现在接受了一个`counterName`输入，它将定义这个组件将用作数据源的存储键的名称，以及动作`type`将具有的前缀。

这就是我们如何在`app.component.html`模板中使用计数器组件:

```
<counter [counterName]="'counter1'"></counter>
<hr>
<counter [counterName]="'counter2'"></counter> 
```

注意，我们已经添加了组件的两个实例。每个都将使用自己动态创建的缩减器。

让我们看看高阶减速器是什么样子的:

`counter.reducer.ts`

```
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';
export const RESET = 'RESET';

export function counterReducer(counterName: string) {
  const initialState = 0;

  return function reducer(state: number = initialState, action: Action) {
    switch (action.type) {
      case `${counterName}.${INCREMENT}`:
        return state + 1;

      case `${counterName}.${DECREMENT}`:
        return state - 1;

      case `${counterName}.${RESET}`:
        return 0;

      default:
        return state;
    }
  }
} 
```

高阶缩减器在三个方面不同于原始实现:

*   它返回 reducer 函数，而不是新状态。
*   它将其状态包装在闭包范围内。
*   匹配的 reducer 动作类型是通过在原始动作`type`前面加上参数化的`counterName`来定义的(这是灵活的，其他人更喜欢后缀，你必须保持一致)。没什么好说的了。

最后但同样重要的是，我们需要改变`Store`配置。我们需要添加存储键，并使用高阶缩减器来分配它们的值。

`app.module.ts`

```
@NgModule({
  imports: [
    BrowserModule,
    StoreModule.forRoot({ 
      counter1: counterReducer('counter1'),
      counter2: counterReducer('counter2')
    })
  ],
  declarations: [AppComponent, CounterComponent],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

差不多就是这样。
`counterReducer('...')`函数每次被调用都会返回一个新的 reducer，这个 reducer 以`[actionName].[action.type]`的形式接受动作类型。

### 按其有效载荷过滤动作

很快，现在我们理解了机制，让我们看看另一个更类型安全的策略来实现高阶减速器。基本原理是相同的，但是我们将使用动作创建器而不是动态动作类型名称，并且我们还将向表中添加更多的类型。( [stackblitz link](https://stackblitz.com/edit/angular-uyr8sx?file=src/app/app.module.ts)

这个项目与更具动态性的项目之间的区别仅在于两个文件:

`counter.reducer.ts`

```
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';
export const RESET = 'RESET';

// Action interface for higher-order reducers

interface CounterAction extends Action {
  actionPrefix: string;
}

// Action creators

export class Increment implements CounterAction {
  type = INCREMENT;
  constructor(public actionPrefix: string) { }
}

export class Decrement implements CounterAction {
  type = DECREMENT;
  constructor(public actionPrefix: string) { }
}

export class Reset implements CounterAction {
  type = RESET;
  constructor(public actionPrefix: string) { }
}

export type CounterActions =
  | Increment
  | Decrement
  | Reset;

// Reducer

export function counterReducer(actionPrefix: string) {
  const initialState = 0;

  function reducer(state: number = initialState, action: CounterActions) {
    switch (action.type) {
      case INCREMENT:
        return state + 1;

      case DECREMENT:
        return state - 1;

      case RESET:
        return 0;

      default:
        return state;
    }
  }

  return (state: number = initialState, action: CounterActions) => {
    switch (action.actionPrefix) {
      case actionPrefix:
        return reducer(state, action);
      default:
        return state;
    }
  }
} 
```

这里发生了很多变化。幸运的是，另一个文件没有太多变化。

`counter.component.ts`

```
@Component({
  selector: 'counter',
  template: `
    <button (click)="increment()">Increment</button>
    <div>Current Count: {{ count$ | async }}</div>
    <button (click)="decrement()">Decrement</button>

    <button (click)="reset()">Reset Counter</button>
  `,
})
export class CounterComponent implements OnInit {
  @Input() counterName: string;
  count$: Observable<number>;

  constructor(private store: Store<AppState>) {}

  ngOnInit() {
    this.count$ = this.store.pipe(select(this.counterName));
  }

  increment() {
    this.store.dispatch(new Increment(this.counterName));
  }

  decrement() {
    this.store.dispatch(new Decrement(this.counterName));
  }

  reset() {
    this.store.dispatch(new Reset(this.counterName));
  }
} 
```

主要的区别是我们引入了 action creators，它满足了新添加的`CounterAction`接口，并使`actionPrefix`属性对其所有实现者都是强制性的。
此外，`CounterActions`型允许我们更具体地了解减速器接受哪些动作。

# 利弊

### 动态创建动作名称

*   赞成的意见

    *   很直白。
*   骗局

    *   它缺乏类型安全。

### 按其有效载荷过滤动作

*   赞成的意见

    *   打字安全。
*   骗局

    *   更啰嗦。

# 点链接

*   阅读 Javascript 书籍中关于高阶函数的章节
*   Redux 文档[对更高级的减速器是什么和做什么有一个很好的描述](https://redux.js.org/recipes/structuring-reducers/reusing-reducer-logic#customizing-behavior-with-higher-order-reducers)。