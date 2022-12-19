# 我如何用 Angular 编写 RxJS 可观测量的大理石测试

> 原文：<https://dev.to/mokkapps/how-i-write-marble-tests-for-rxjs-observables-in-angular-4l0k>

我是一个非常热情的[反应式扩展](http://reactivex.io/)用户，主要在 [RxJS](https://github.com/Reactive-Extensions/RxJS) 中使用它们，RxJS 被集成到 [Angular 框架](https://angular.io)中。

在 Angular 中，我经常在服务中使用 observables，因此需要为这些异步数据流编写测试。

不幸的是，测试可观察性很难，老实说，我经常需要更多的时间来为我的流编写单元测试，而不是在产品代码中实现它们。但是幸运的是，RxJS 有一个集成的解决方案可以帮助编写这种测试:所谓的大理石测试。

如果您已经熟悉了将异步数据流表示为大理石图，那么大理石测试并不困难。在这篇博文中，我想向你介绍大理石图的概念，大理石测试的基础知识，以及我如何在我的项目中使用它们的例子。

> 在这篇文章中，我不会提供 RxJS 的一般介绍，但是我强烈推荐[这篇文章](https://dev.to/sagar/reactive-programming-in-javascript-with-rxjs-4jom)来更新基础知识。

## 大理石检测

对于 RxJS 用户来说，有一个关于 marble 测试的官方文档,但是这很难开始，因为从 v5 到 v6 有很多变化。因此，我想先解释一下基础知识，展示一个示例性的角度测试实现，最后谈谈 RxJS 6 的一些新特性。

## 大理石图

为了更容易地可视化 RxJS 观察值，引入了一种新的领域特定语言，称为“大理石图”。

> 大理石图是运算符如何工作的可视化表示，包括输入可观察值、运算符及其参数以及输出可观察值。

来自官方文档的下图描述了大理石图的解剖结构:

[![Marble Diagram Anatomy](../Images/8401878cd036974c3e306699d934914d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yta4hE6A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.mokkapps.de/marble-diagram-anatomy-70a8819bfbc13c46a956a06eccee9614.svg)

> 在弹珠图中，时间向右流动，该图描述了值(“弹珠”)是如何在可观察的执行上发出的。

### 大理石语法

在 RxJS marble 测试中，marble 图被表示为一个字符串，该字符串包含一个特殊的语法，表示在虚拟时间内发生的事件。任何大理石字符串中的时间起点(也称为零帧)总是由字符串中的第一个字符表示。

*   `-`时间:时间流逝的 10 个“帧”。
*   完成:可观察到的成功完成。这是可观察到的生产者信令完成()。
*   误差:终止可观察的误差。这是可观察到的生产者信号错误()。
*   `"a" any character`:所有其他字符代表生产者发出的值，发出 next()。
*   同步分组:当多个事件需要同步出现在同一个帧中时，括号用于对这些事件进行分组。您可以用这种方式对嵌套值、完成或错误进行分组。初始(的位置决定其值发出的时间。
*   `^`订阅点:(仅限热观测值)显示测试的观测值将订阅热观测值的点。这是可观测的“零帧”,在^之前的每一帧都是负的。

#### 例子

`-`或`------`:相当于 Observable.never()，或者从不发射或完成的可观察对象

`|`:相当于 Observable.empty()

`#`:相当于 Observable.throw()

`--a--`:一个等待 20“帧”的可观察对象，发出值 a，然后永不完成。

`--a--b--|`:在第 20 帧发射 a，在第 50 帧发射 b，在第 80 帧，完成

`--a--b--#`:在第 20 帧发射 a，在第 50 帧发射 b，在第 80 帧，错误

`-a-^-b--|`:在热可观测中，在第 20 帧上发射 a，然后在第 20 帧上发射 b，在第 50 帧上，完成。

`--(abc)-|`:在第 20 帧，发射 a，b，c，然后在第 80 帧完成

`-----(a|)`:在第 50 帧，发射 a 并完成。

### 一个实用的角度例子

既然你现在知道了理论基础，我想给你看一个现实世界的角度例子。

在这个 [GitHub 库](https://github.com/Mokkapps/rxjs-marble-testing-demo)中，我实现了一个基本的测试设置，现在我将详细解释它。Angular CLI 项目由以下组件和服务组成:

#### 用户服务

该服务提供了一个公共 getter `getUsers()`，它返回一个每秒发出一个新用户名的可观察对象。

*user.service.ts*

```
import { Injectable } from '@angular/core';
import { Observable, interval } from 'rxjs';
import { take, map } from 'rxjs/operators';

@Injectable({
  providedIn: 'root',
})
export class UserService {
  private readonly testData = ['Anna', 'Bert', 'Chris'];

  get getUsers(): Observable<string> {
    return interval(1000).pipe(
      take(this.testData.length),
      map(i => this.testData[i])
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### AllMightyService

这个服务注入了上面介绍的`UserService`并提供了公共 getter `getModifiedUsers`。这个 getter 也返回一个 Observable，并映射从`userService.getUsers`发出的用户名，使它们更“强大”。

*全能服务*

```
import { Injectable } from '@angular/core';
import { map } from 'rxjs/operators';
import { Observable } from 'rxjs';

import { UserService } from './user.service';

@Injectable({
  providedIn: 'root',
})
export class AllMightyService {
  get getModifiedUsers(): Observable<string> {
    return this.userService.getUsers.pipe(map(user => `Mighty ${user}`));
  }

  constructor(private userService: UserService) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

#### AppComponent

在我们的`app.component.ts`中，我们注入`UserService`并在每次从`getUsers`观察对象发出新用户名时更新一个列表。

*app.component.ts*

```
import { Component, OnDestroy, OnInit } from '@angular/core';
import { Subscription } from 'rxjs';

import { UserService } from './services/user.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss'],
})
export class AppComponent implements OnInit, OnDestroy {
  title = 'MarbleDemo';

  users: string[] = [];

  private subscription: Subscription | undefined;

  constructor(private userService: UserService) {}

  ngOnInit() {
    this.subscription = this.userService.getUsers.subscribe(user => {
      this.users.push(user);
    });
  }

  ngOnDestroy() {
    if (this.subscription) {
      this.subscription.unsubscribe();
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*app.component.html*T2】

```
<div style="text-align:center"><h1>Welcome to {{ title }}!</h1></div>
<h2>Here will users pop in asynchronously:</h2>
<ul>
  <li class="user" *ngFor="let user of users"><h2>{{user}}</h2></li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以为这个项目编写不同的单元测试:

*   测试 AppComponent 是否显示了正确的用户名列表
*   测试 AllMightyService 是否正确映射和发出用户名

让我们从 AppComponent 的单元测试开始。

在这些测试中，我使用 npm 包 [jasmine-marbles](https://www.npmjs.com/search?q=jasmine%2Dmarbles) ，这是一个帮助器库，如果您使用 jasmine(在 Angular 中默认使用)，它将为 marble 测试提供一个简洁的 API。

基本思想是模拟所提供服务的公共可观察性，并以同步方式测试我们的异步数据流。

基本上，我们嘲笑用户服务和`getUsers`可观察对象。在测试用例中，我们通过调用`getTestScheduler().flush()`来清除所有可观察的对象。这意味着在这一行被执行之后，我们的模拟可观察对象已经发出了它的所有事件，我们可以运行我们的测试断言了。在这个例子之后，我将更多地讨论 TestScheduler。

```
 import { TestBed, async } from '@angular/core/testing';
import { getTestScheduler, cold } from 'jasmine-marbles';

import { AppComponent } from './app.component';
import { UserService } from './services/user.service';
import { By } from '@angular/platform-browser';

describe('AppComponent', () => {
  let userService: any;

  beforeEach(async(() => {
    // Here we mock the UserService to a cold Observable emitting three names
    userService = jasmine.createSpy('UserService');
    userService.getUsers = cold('a-b-c', { a: 'Mike', b: 'Flo', c: 'Rolf' });

    TestBed.configureTestingModule({
      declarations: [AppComponent],
      providers: [{ provide: UserService, useValue: userService }],
    }).compileComponents();
  }));

  it('should correctly show all user names', async () => {
    const fixture = TestBed.createComponent(AppComponent);
    fixture.detectChanges(); // trigger change detection

    getTestScheduler().flush(); // flush the observable
    fixture.detectChanges(); // trigger change detection again

    const liElements = fixture.debugElement.queryAll(By.css('.user'));
    expect(liElements.length).toBe(3);

    expect(liElements[0].nativeElement.innerText).toBe('Mike');
    expect(liElements[1].nativeElement.innerText).toBe('Flo');
    expect(liElements[2].nativeElement.innerText).toBe('Rolf');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

下一步，让我们分析一个服务测试，在这个例子中是 AllMightyService。

*全能.服务.规格. ts*

```
 import { hot, cold } from 'jasmine-marbles';
import { TestScheduler } from 'rxjs/testing';

import { AllMightyService } from './all-mighty.service';
import { fakeAsync } from '@angular/core/testing';

describe('AllMightyService', () => {
  let sut: AllMightyService;
  let userService: any;

  beforeEach(() => {
    // we mock the getUsers Observable of the UserService
    userService = jasmine.createSpy('UserService');
    userService.getUsers = hot('^-a-b-c', {
      a: 'Hans',
      b: 'Martin',
      c: 'Julia',
    });

    sut = new AllMightyService(userService);
  });

  it('should be created', () => {
    expect(sut).toBeTruthy();
  });

  it('should correctly return mighty users (using jasmine-marbles)', () => {
    // Here we define the Observable we expect to be returned by "getModifiedUsers"
    const expectedObservable = cold('--a-b-c', {
      a: 'Mighty Hans',
      b: 'Mighty Martin',
      c: 'Mighty Julia',
    });
    expect(sut.getModifiedUsers).toBeObservable(expectedObservable);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### [T1】test scheduler](#the-testscheduler)

正如我们在第一个 AppComponent 测试中已经看到的，RxJS 为“时间操作”提供了一个 TestScheduler。

RxJS 中事件的发射顺序由内部调度程序控制。大多数时候我们不必关心调度程序，因为它们大部分是由 RxJS 内部处理的。但是我们可以为操作符提供一个调度器，正如我们在[“延迟”操作符](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-delay) :
的签名中看到的

```
delay(delay: number | Date, scheduler: Scheduler): Observable 
```

Enter fullscreen mode Exit fullscreen mode

最后一个参数是可选的，默认为异步调度程序。RxJS 包括以下调度程序:

*   异步调度程序
*   动画帧调度程序
*   aspxscheduler
*   队列调度程序
*   测试调度程序
*   虚拟日程表

因此，为了避免在测试中使用实时，我们可以将`TestScheduler`(从`VirtualTimeScheduler`派生而来)传递给我们的操作者。`TestScheduler`允许我们操纵测试用例中的时间，并使我们能够以同步的方式编写异步测试。

## 新 RxJS 6 大理石测试功能

在 RxJS v5 中，几乎没有关于`TestScheduler`的文档，因为它主要由库作者内部使用。从 RxJS 6 开始，这已经改变了，我们现在可以使用 TestScheduler 来编写 marble 测试。

#### testScheduler.run(回调)

在以前的 RxJS 版本中，我们必须将调度程序传递给生产代码中的操作员，以便能够用虚拟时间操作来测试它们。

```
getUsers(scheduler) {
    const dummyData = Observable.from(['Anna', 'Bert', 'Chris']);
    return dummyData.delay(1000, scheduler); // each user is emitted after 1 second
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们现在将我们的生产代码与我们只需要测试的逻辑混合在一起。scheduler 参数仅被添加并用于测试。

新的 run 方法解决了这个问题。每个使用 AsyncScheduler 的 RxJS 操作符(例如“timer”或“debounce”)在 run 方法内部执行时将自动使用 TestScheduler，因此使用虚拟时间而不是实时时间。

通过这种方式，可以在没有调度器参数的情况下重写上面的相同方法，并且在生产代码中不再有测试代码:

```
getUsers() {
    const dummyData = Observable.from(['Anna', 'Bert', 'Chris');
    return dummyData.delay(1000); // each user is emitted after 1 second
} 
```

Enter fullscreen mode Exit fullscreen mode

使用新的 run 方法对 AllMightyService 的 getModifiedUsers 方法进行的单元测试可以是这样的:

```
it('should correctly return mighty users (using RxJS 6 tools)', () => {
  const scheduler = new TestScheduler((actual, expected) => {
    // asserting the two objects are equal
    expect(actual).toEqual(expected);
  });

  scheduler.run(helpers => {
    const { expectObservable } = helpers;

    const coldObservable = scheduler.createHotObservable('^-a-b-c', {
      a: 'Hans',
      b: 'Martin',
      c: 'Julia',
    });
    userService.getUsers = coldObservable;
    sut = new AllMightyService(userService);

    const expectedMarble = '--a-b-c';
    const expectedVales = {
      a: 'Mighty Hans',
      b: 'Mighty Martin',
      c: 'Mighty Julia',
    };
    expectObservable(sut.getModifiedUsers).toBe(expectedMarble, expectedVales);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

它看起来与我们上面的`jasmine-marble`测试非常相似，但是新的 run 方法提供了一些有趣的新特性，比如[时间进程语法](https://github.com/ReactiveX/rxjs/blob/master/doc/marble-testing.md#time-progression-syntax)。

> 此时，TestScheduler 只能用于测试使用计时器的代码，如 delay/debounceTime/etc(即，它使用延迟> 1 的 AsyncScheduler)。如果代码消耗了一个承诺或者用 asap scheduler/animation frame scheduler/etc 进行了调度，那么它就不能用 TestScheduler 进行可靠的测试，而应该用更传统的方法进行测试。有关更多详细信息，请参见已知问题部分。

### 结论

弹球图是一个用来可视化异步数据的既定概念，我们可以在流行网站 [RxMarbles](http://rxmarbles.com/) 上看到。使用大理石弦，我们现在也可以用这种干净的方式来测试我们的可观测量。

我建议从使用像`jasmine-marbles`这样的助手库开始，因为它们对初学者更友好。您可以在同一个项目中将 jasmine-marble 测试与新的 RxJS 6 特性结合起来，就像我在[我的示例项目](https://github.com/Mokkapps/rxjs-marble-testing-demo/blob/master/src/app/services/all-mighty.service.spec.ts)中演示的那样。

根据我的经验，我可以告诉你，学习大理石测试是值得的，因为这样你就能以一种可理解的方式测试非常复杂的可观察的数据流。

我希望你现在能够开始在你的项目中使用 marble 测试，并且开始享受为可观测量编写单元测试的乐趣。*