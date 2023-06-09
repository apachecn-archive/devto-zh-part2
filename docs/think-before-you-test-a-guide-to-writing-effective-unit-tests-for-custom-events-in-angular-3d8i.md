# 测试之前先思考:用 Angular 编写有效的自定义事件单元测试指南

> 原文：<https://dev.to/vincecampanale/think-before-you-test-a-guide-to-writing-effective-unit-tests-for-custom-events-in-angular-3d8i>

Angular 是为可测试性而构建的。像依赖注入、`TestBed` API 和与 Jasmine 的开箱即用集成这样的强大工具让我们能够彻底可靠地测试我们的 Angular 应用。问题是学习这些 API 需要一些时间。再加上 Angular、Jasmine 和 RxJS 术语的混合，测试应用程序中最复杂的部分是一场艰苦的战斗，当然这也是最重要的测试部分。在这篇文章中，我将介绍几种不同的方法来测试 Angular 中的自定义事件。如果这对你有帮助或者感兴趣，你可以看看我的 [twitter 页面](https://twitter.com/_vincecampanale)，我在那里分享类似的内容。另外，这里有一个[链接指向我在起草这篇文章时使用的 Stackblitz 应用](https://stackblitz.com/edit/testing-event-emitter)。可能会派上用场。

## 什么是`@Output`属性？

属性是一个角度工具，用于创建自定义事件。An `@Output`是 an [`EventEmitter`](https://angular.io/api/core/EventEmitter) ，意思是它有两种方法:`emit`和`subscribe`。你可能不需要直接`subscribe`到它，因为 Angular 用它的事件绑定语法处理它(例如`<div (someEvent)="onSomeEvent()"></div>`)。`emit`方法允许您通知事件的父进程并向上传递数据。

## 自定义事件的单元测试应该做什么？

当您正在测试的组件负责发出自定义事件(子组件)时，单元测试应该针对两件事:1)在应该的时候调用`@Output`属性的`emit`方法，以及 2)`emit`方法发出预期的数据。

当测试组件监听`@Output`(父/容器组件)时，单元测试应该检查发出的数据是否被正确处理(例如，传递给正确的方法)。

## 组件

示例子组件:

```
@Component({
  selector: 'counter',
  template: `
    <div>
      <button (click)="onClick()">1</button>
    </div>
  `
})
export class CounterComponent {
  @Output() change = new EventEmitter<number>();

  onClick() {
    this.change.emit(1);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`change`属性是要测试的`EventEmitter`。

我们监听`AppComponent`中的变化事件，使计数器增加发出的量:

```
@Component({
  selector: 'my-app',
  template: `
  <counter (change)="onChange($event)"></counter>
  `
})
export class AppComponent  {
  count = 0;

  onChange(event: number): void {
    this.count += event;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 测试孩子

首先，我们将做一些设置:

```
describe('CounterComponent', () => {
  let fixture: ComponentFixture<CounterComponent>;
  let component: CounterComponent;
  let de: DebugElement;
  let button: ElementRef;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [CounterComponent]
    });
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(CounterComponent);
    component = fixture.componentInstance;
    de = fixture.debugElement;
    button = de.query(By.css('button'));
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我不会详细讨论这个设置是如何工作的，因为这超出了本文的范围。 [Angular 的测试教程](https://angular.io/guide/testing)是了解更多信息的绝佳资源。重要的是，我们可以使用`component`和`button`测试所有需要测试的东西。

每个客户`@Output`必须由另一个事件触发。无论该事件是 DOM 中的一次单击、来自服务器的一个响应，还是另一个嵌套子组件上的自定义事件，都必须有一个调用`emit`方法的原因。第一步是模拟这个原因，并确保`EventEmitter`实际上发射。

我们从组件代码中知道，按钮上的点击事件应该会使`onClick()`方法运行。当`onClick()`执行时，应该调用`change`属性的`emit`方法。我们可以用两种方式让`onClick()`执行:在按钮上模仿一个`click`，或者直接调用`component.onClick()`。

下面是模仿按钮上的`click`的许多方法之一:

```
button.nativeElement.click(); 
```

Enter fullscreen mode Exit fullscreen mode

为了检测`@Output`何时发射，我们可以创建一个间谍:

```
spyOn(component.change, 'emit'); 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经拥有了有效测试`@Output`所需的一切。

单元测试可能看起来像这样:

```
describe('change', () => {
  it('should emit when the button is clicked', () => {
    spyOn(component.change, 'emit');
    button.nativeElement.click();
    expect(component.change.emit).toHaveBeenCalled();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。现在，让我们瞄准目标#2:确保`@Output`向父节点发出预期的数据。

利用`toHaveBeenCalledWith()`，我们可以一举两得:

```
describe('change', () => {
  it('should emit when the button is clicked', () => {
    spyOn(component.change, 'emit');
    button.nativeElement.click();
    expect(component.change.emit).toHaveBeenCalledWith(1);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，在一个单元测试中，您要确保`emit`方法在应该被调用的时候被调用，并且发出正确的数据。有两种其他方法可以实现这一点，值得一提。

我认为可以肯定地说 Angular 已经停止了`click`事件，所以我们不需要担心它不会像预期的那样工作。正因为如此，直接调用`onClick()`方法是安全的，而不是模仿点击按钮。

```
describe('change', () => {
  it('should emit when the button is clicked', () => {
    spyOn(component.change, 'emit');
    component.onClick();
    expect(component.change.emit).toHaveBeenCalledWith(1);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这有点简单，因为我们不必担心查询`DebugElement`或模仿点击事件，我们只需直接调用该方法，并信任 Angular 来处理其余的。

测试`EventEmitter`的最后一种方法是实际订阅它并触发事件，在订阅块中进行断言。

```
describe('change', () => {
  it('should emit when the button is clicked', () => {
    component.change.subscribe(next => {
      expect(next).toEqual(1);
    });

    component.onClick(); // or button.nativeElement.click()
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我不推荐这种方法，有几个原因:

1.  很奇怪。通常，单元测试在*结束*时断言。这种方法打破了这种模式，并将导致未来的开发人员不得不侧着眼睛去理解测试是如何工作的。单元测试应该易于阅读和理解。
2.  陈述的顺序很重要。如果您在订阅`change`发射器之前调用`component.onClick()`，您将不会进入 subscribe 块并做出断言。更糟糕的是，你的测试会通过！一个错误的、通过的测试比根本没有测试更糟糕。

# 测试父代

从父组件(监听事件的组件)的角度来看，我们可以采用三种方法来测试`EventEmitter`的行为:

1.  调用`@Output`属性的`emit`方法(因为`EventEmitter`是公共属性)
2.  深入研究计数器的`DebugElement`并模拟点击按钮
3.  直接调用函数(相信 Angular 会工作)

这是设置的样子:

```
describe('AppComponent', () => {
  let fixture: ComponentFixture<AppComponent>;
  let component: AppComponent;
  let de: DebugElement;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [AppComponent, CounterComponent]
    });
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(AppComponent);
    component = fixture.componentInstance;
    de = fixture.debugElement;
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

为了调用`@Output`属性的`emit`方法，我们必须在测试模块中用`@Output`声明组件。

现在，我们可以使用`AppComponent`的`DebugElement`来获得计数器组件:

```
describe('onChange', () => { 
  it('should be called with whatever the counter change event emits', () => {
    spyOn(component, 'onChange');
    const counter = de.query(By.directive(CounterComponent));
    const cmp = counter.componentInstance;
    cmp.change.emit(1);
    expect(component.onChange).toHaveBeenCalledWith(1);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的单元测试中，我们窥探了`onChange`方法(当`change`发出时应该调用的方法)。然后，我们基于其指令类查询计数器组件 fixture，并通过`componentInstance`属性获取组件本身。*现在*，我们可以访问`change`属性，并可以告诉`emit`一个`1`的值。为了测试我们是否正确地处理了事件，我们将检查是否用`change`事件发出的值调用了`onChange` spy。这是矫枉过正，但远不如下一个测试矫枉过正。

```
describe('onChange', () => {
  it('should be called with whatever the counter change event emits', () => {
    spyOn(component, 'onChange');
    const counter = de.query(By.directive(CounterComponent));
    const button = counter.query(By.css('button'));
    button.nativeElement.click();
    expect(component.onChange).toHaveBeenCalledWith(1); 
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们正在查询实际的物理按钮的子元素的 fixture，并向按钮分派一个`click`事件。这个`click`事件将引发连锁反应，最终导致我们的`AppComponent`的`onChange`方法被从`change`事件发出的值调用。但是等等，让我们检查一下我们在这里实际测试的内容。一个单元测试应该负责一个*单元*的功能。我们刚刚编写的测试是测试 1)按钮的点击是否有效，2)Angular 对点击事件的处理是否有效，3)我们在`CounterComponent`中的`onClick`方法是否被正确的数据调用，并适当地调用`change`属性的`emit`方法，4)Angular 对`change`事件的处理是否有效，5)我们的`onChange`方法是否有效。那不是单元测试。

现在你已经看到了你可以用这套强大的测试工具做的所有疯狂的事情，你将会很欣慰地看到你真正需要 T2 做的事情:T4

```
describe('onChange', () => {
  it('should increment the count by the amount provided', () => {
    component.count = 2;
    component.onChange(2);
    expect(component.count).toEqual(4);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这一端唯一需要测试的是`onChange`方法本身。这是我们写的唯一的逻辑。其他一切都由 Angular 处理。如果你持怀疑态度，请随时复查[和`EventEmitter`测试](https://github.com/angular/angular/blob/master/packages/core/test/event_emitter_spec.ts)。

# 外卖

测试很好。在 Angular 中，我们有许多强大的测试工具，因此很容易确保我们的组件正常工作。最后，理解我们能够测试的和实际需要测试的之间的区别是很重要的。