# 从反作用风扇的角度看有角度-第 3 部分

> 原文：<https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-3-5dnj>

### 角级数

*   [第 1 部分-引导和类型脚本](https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-1-188p)
*   [第 2 部分-测试](https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-2-7bg)
*   [**第三部分-状态管理**](https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-3-5dnj)

当构建一个复杂的应用程序时，考虑数据如何在其中流动是非常重要的。我记得，在 jQuery 的日子里，有一些代码可以修改页面中多个不相关的部分。幸好我们当时没有做大量的单元测试，因为那些代码基本上是不可能测试的。

我仍然饱受创伤后应激障碍的折磨，一旦我真正理解了它是如何工作的，我立刻成为了 [redux](https://redux.js.org/) 的粉丝。不管怎样，[单向流模式](https://redux.js.org/basics/data-flow)是以可预测的方式改变应用程序状态的一种非常好的方式。

*Angular* 有一堆不同的选项来处理状态。我正在做的项目在建立的时候没有人在这方面有丰富的经验，所以我看到了很多这样的尝试，保存在它的历史中。他们中的一些人没能让我们的生活变得更轻松。我仍然在寻找一个好的方法来治疗*角*的状态，但至少我已经学到了一些教训(艰难的方式)，我想分享一下。这是我见过并使用过的一些在 *Angular* 中建模状态的例子。

## 输入/输出

[输入/输出](https://angular.io/guide/component-interaction#pass-data-from-parent-to-child-with-input-binding)基本相当于 React 中的[道具。状态不是保存在组件中，而是从外部注入的。每当组件需要触发一个通知时，一个*输出*可以和一个回调一起使用。](https://reactjs.org/docs/components-and-props.html)

这是管理状态和创建主要是表示性的、不管理自己状态的组件的好方法。它有一个与 *props* 相同的缺点，那就是如果你的组件层次非常深，你可能会有很多组件上下传递数据和回调，即使他们自己并没有使用它。

我真的很喜欢看到小而简单的组件，在那里你可以立即理解需要什么数据，以及从中触发哪些操作。

```
class SimpleComponent {
  @Input() selectedDate: Date;
  @Output() onJump = new EventEmitter<Date>();

  jumpBack(date: Date) {
    this.onJumpto.emit(date);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，国家必须在某个地方得到管理。至少需要一个组件来承担这个责任。但是如果大多数组件不需要关心状态，那么推理它们，并且，我敢说，**重用它们就变得容易多了。**

## ViewChild

一个 [ViewChild](https://angular.io/api/core/ViewChild) 把一个子组件的引用给了一个父组件，这允许它打破单向流中的每一条规则，直接调用方法。很容易误解它来构建一个你永远无法重构的深度连接的组件网络。你可以这样做

```
// Bad idea
class Evil {
  @ViewChild(PoorChildrenComponent)
  sayGoodbyeToEncapsulation: PoorChildrenComponent; 

  ngOnInit() {
    this.sayGoodbyeToEncapsulation.changeStuffExternally();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我确信这个特性是有原因的，也有使用它的正确方法，但是我看到它主要是将组件紧密地耦合在一起。除非我看到一个令人信服的理由来使用这个特性，而不会导致设计的失败，否则我看不到这个特性在组件测试之外的位置。

## 嵌套表单

另一种在组件间创建某种隐藏连接的方法是[嵌套表单](https://angular.io/guide/reactive-forms)。据我所知，嵌套表单的思想是将不同组件的多个表单组合成一个可以作为一个单元进行验证和提交的表单。那听起来确实不错。然而，如果没有纪律，你可以从很远的地方改变表单的值。

事实上，在没有真正考虑状态如何更新的情况下使用这种模式会导致一些更新丢失，最终通过在代码中粘贴`detectChanges`得到解决。调试那个问题一点也不好玩。

## 数据服务

在 *Angular* 社区，通过数据服务进行交流似乎是一种非常普遍的做法。它的要点是定义一个服务，该服务属于层次结构中的一个组件，它对子组件可用，而不需要创建新的实例。该服务提供了许多方法来以明确定义的方式改变状态。通过[观察器](https://angular.io/guide/observables)获取更新。这有很多好处:

*   改变状态的逻辑集中在一个地方，在任何组件之外。
*   状态只能通过这个 API 来修改，这样更容易控制和理解变化。
*   任何对某个主题的更新感兴趣的人都可以订阅 observable。这就把动作和反应分开了。

一个简单的服务可能是这样的:

```
@Injectable()
export class TimeStore {

  private timeslotSubject = new BehaviorSubject<Date>(null);
  timeslot$ = this.timeslotSubject.asObservable();

  selectTimeslot(timeslot: Date) {
    this.timeslotSubject.next(timeslot);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是迄今为止我在 *Angular* 中发现的最令人满意的处理状态的方式。它允许您很好地解耦，如果对某些更新感兴趣的组件数量增加，它的伸缩性也很好。然而，这并不是没有警告的 T2。这往往也需要大量的样板文件。不过话说回来， *Angular* 总体上似乎需要很多样板代码。

另一件需要记住的重要事情是，你必须避免创建没有好的 API 的服务，或者更糟糕的是，只有一些公共字段的服务。否则，您只是创建了一个半全局可变共享状态。在这一点上，我们还不如在全局名称空间中定义东西(避免这种情况)。

## ngrx 存储

[ngrx store](https://github.com/ngrx/platform/blob/master/docs/store/README.md) 就是 *Angular* 相当于 *redux* 。与 *React* 社区不同，它似乎不像一个状态管理解决方案那样被接受，至少根据我与其他开发人员交谈过的情况。到目前为止，我还没有在任何真正的代码中使用过它，尽管它看起来确实像是 *redux* ，可能有点冗长。虽然它和 *redux* 有相同的问题:作为第一个不容易掌握。把它介绍给一个没有经验的团队会导致很多茫然的表情。

## 结论

我的结论是我没有结论。你如何处理*角度*中的状态？我并没有觉得自己比几个月前聪明多少。我已经看到了很多我不想做的事情，但这并不像我可以提供一个坚实的总体战略。我的**TL；博士**将使用*数据服务*，并希望一切顺利。

这些是我现在关于*角度*的主要观点。我想我还有其他的小观点，也许我可以汇编成另一个条目。