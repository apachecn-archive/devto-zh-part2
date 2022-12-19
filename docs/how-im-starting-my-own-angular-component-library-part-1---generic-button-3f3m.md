# 我如何开始我自己的角度组件库(第 1 部分-通用按钮)

> 原文：<https://dev.to/chiangs/how-im-starting-my-own-angular-component-library-part-1---generic-button-3f3m>

[![easy button image](../Images/68522307590dc8ab1cc0a82bee8ebf2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JTnwgFDY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h0phh33az9sowfagiamr.jpg)

### [T1】简介](#intro)

作为一名网页开发者，我们制作了很多按钮...a..米制垃圾吨。有了 JavaScript 及其各种框架中可重用组件的奇妙概念，节省编写这些重复任务的时间变得容易多了。我决定在 Angular 6 项目中建立自己的组件库，并共享它；这是系列文章的第一部分。你可以说我的灵感来自模块星期一系列！

### TL；速度三角形定位法(dead reckoning)

直接进入[angularcomponentluble Repo](https://github.com/chiangs/AngularComponentLibrary)

### 我希望这个按钮能够处理什么

TS 访问器(get/set)而不是 ngOnChanges bc 我希望对每个输入进行更细粒度的控制，并在每次发生更改时处理每个更改，而不是每次都立即处理，不管更改是否发生。

按钮用于:

1.  具有标准初始样式的标准按钮，可在我的所有视图中重复使用；向父组件发出它已被单击的消息，业务逻辑由父组件处理。
2.  可以使用基于父处理的逻辑的标准样式来禁用。
3.  可以设置为呈现在列表中，也可以设置为作为项目列表的一部分被选中。
4.  可以显示由父组件激活和重置的加载动画(即，Http 请求)。
5.  根据父组件逻辑加载动画后，如果需要，可以显示更新的成功消息。

### 步骤

#### 通过 Angular CLI 创建组件

创建按钮组件。在生产应用程序中，我通常会将这个组件放在一个*共享模块*中并导出它。

```
$ ng g c button 
```

Enter fullscreen mode Exit fullscreen mode

#### 决定输入和输出

我将跳过本文中的样式，因为它非常简单，而且可能会被更改和设置以匹配正在使用的应用程序，所以您可以通过 repo 链接在代码中看到我的基本样式。我们将关注实际的逻辑。即使有所有的输入，这也是一个非常简单的组件，所以我将把它写成一个内联模板组件，这样在查看逻辑时 HTML 很容易被引用。

我们知道这个按钮将接受来自父组件的输入，这就是它的可重用性。我们还知道这个按钮需要向父组件返回某种信号，让父组件知道这个按钮被点击了。因此，我们需要导入`Input`、`Output`和`EventEmitter`，因为发射器将向父节点发出信号，让其通过点击按钮来做一些事情。

给定上面的列表，我需要按钮能够动态设置它的名称，按钮类型(按钮或提交)，我还希望它的禁用状态被动态设置，并为它设置样式，按钮应该知道什么时候开始/停止加载动画，或者它是否被激活为选择，当然应用正确的样式。这里是 *button.component.ts* 文件的初始内联模板部分。

```
import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-button',
  template: `
  <button type="buttonType" class="button" (click)="onClick()" 
    [disabled]="isDisabled"
    [ngClass]="{'loading' : loading, 'disabled': isDisabled, 'active': isActivated}">{{ buttonText }}
  </button>
  `,
  styleUrls: ['./button.component.scss']
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在我需要定义预期的输入。您会注意到，我使用 TS 访问器(get/set)进行加载、禁用和激活。这是为了让按钮组件可以在 init 之后检测输入更改。另一种方法是使用`ngOnChanges`,但我希望对每个输入进行更精细的控制，并在每次发生变化时进行处理，而不是每次都立即处理，不管特定输入是否发生变化。

我还将默认值设置为 false，这样，如果您不想/不需要[loading]、[isDisabled]、[isActivated]绑定，就不必包含它们。那么您需要的最少的按钮组件应该是:

```
<app-button [buttonText]="someText" (buttonClick)="onClick()"></app-button> 
```

Enter fullscreen mode Exit fullscreen mode

```
export class ButtonComponent implements OnInit {
  @Input() buttonText: string;
  @Input() buttonSubmit = false;
  @Input()
  set loading(loading: boolean) {
    this._loading = loading || false;
  }
  get loading(): boolean {
    return this._loading;
  }
  @Input()
  set isDisabled(isDisabled: boolean) {
    this._isDisabled = isDisabled || false;
  }
  get isDisabled(): boolean {
    return this._isDisabled;
  }
  @Input()
  set isActivated(isActivated: boolean) {
    this._isActivated = isActivated || false;
  }
  get isActivated(): boolean {
    return this._isActivated;
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在是输出，或者是发射器，它通知父组件在按钮被点击后做一些事情。这遵循了单向绑定约定，即父节点应该决定做什么，逻辑更加集中，也更容易推理。因此，我们实例化了一个 EventEmitter 和一个 onClick 监听器的附属函数，该函数只发出按钮的名称和一个字符串，这不是必需的，但对于验证发射器以后是否工作很有用。同样在构造上，如果没有定义[buttonText],我将它设置为一个字符串，告诉我这个按钮需要一个文本值。

```
 @Output() buttonClick: EventEmitter<any>;

  constructor() {
    this.buttonClick = new EventEmitter<any>();
    this.buttonType = this.buttonSubmit ? `submit` : `button`;
  }

  ngOnInit() {
    this.buttonText = this.buttonText ? this.buttonText : `No buttonText`;
  }

  onClick(): any {
    if (this.isDisabled) {
      return;
    } else {
      this.buttonClick.emit(this.buttonText + ` clicked`);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在我们用这样的加载器实例化一个基本按钮:

```
<app-button [buttonText]="buttonTitle" [loading]="buttonLoading" (buttonClick)="onClick()"></app-button> 
```

Enter fullscreen mode Exit fullscreen mode

并且 parent.component.ts 会有这样的内容，其中超时部分是一个异步请求，比如 HTTP POST。(不要忘记在请求成功或失败时重置加载状态。)

```
buttonText = `My new button`;
buttonLoading = false;

buttonClicked(event: any): void {
    console.log(event);
    this.buttonLoading = true;
    this.buttonText = `Component loading`;
    this.httpRequestSvc.someRequest().subscribe(data => {
      if (data) {
        // some logic with the data
        this.buttonLoading = false;
        this.buttonText = `My new button`;
      } else {
        this.buttonLoading = false;
        this.buttonText = `My new button`;
      }
    });
  } 
```

Enter fullscreen mode Exit fullscreen mode

因此，检查回购的全部代码，并查看我为这个组件编写的其他文档。让我知道它对你的作用，或者如果你看到需要改进的地方！

也可以随意要求组件。接下来，我将致力于自动调整图像的图库组件。

### 代码

[按钮组件](https://github.com/chiangs/AngularComponentLibrary/tree/master/src/app/button)