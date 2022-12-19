# 我如何开始我自己的角度组件库(第 2 部分-平滑-滚动-组件)

> 原文：<https://dev.to/chiangs/how-im-starting-my-own-angular-component-library-part-2---smooth-scroll-component-66o>

这是疯狂的几个月，所以这个库的构建一直是最后的优先事项，这里有一个快速、非常简单的组件，它将任何类名的输入作为字符串，单击后，将浏览器窗口平滑地滚动到该类。使用 elementId 进行修改是可能的，但是对于这个用例，我决定使用类。这允许你在一个长列表中插入这些小组件，回到顶部(页眉)，底部(页脚)，或者任何其他地方。

### TL；速度三角形定位法(dead reckoning)

直接进入[angularcomponentluble Repo](https://github.com/chiangs/AngularComponentLibrary)

### 我希望这个组件能够处理什么

我希望这个组件尽可能简单直接，具有一致且可预测的行为。只需输入一个元素类名的字符串，然后将浏览器窗口平滑地滚动到那个位置。

### 步骤

在生成带有内联模板标志的`scroll-to.component`之后，我将首先编写实际的 html 标记并指定输入:

```
import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-scroll-to',
  template: `
  <div class="scrollLinkContainer" (click)="scrollTo()">
    <p class="button">{{ linkText }}<span class="icon"></span></p>
  </div>
  `,
  styleUrls: ['./scroll-to.component.scss']
})
export class ScrollToComponent implements OnInit {
  @Input()
  linkText: string;
  @Input()
  scrollToClass: string;
  @Output()
  onScroll: EventEmitter<string>;

constructor() {
    this.onScroll = new EventEmitter();
}

ngOnInit() {} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个父组件想要给这个组件的文本输入，实际的类名，然后一个事件发射器告诉父组件这个组件被点击了。发射器真的不是必需的，但在某些情况下可能有用。

其余的`scroll-to.component.ts`和实际的肉，也就是 scrollTo click 事件。我们来剖析一下这个。

我们选择所有匹配类名输入的元素作为节点列表。在这个例子中，我想滚动到匹配的第一个实例。人们可以通过使用元素 id 来避免这样做。然后我们利用`scrollIntoView` [api](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) 来滚动窗口。*请注意，api 的选项并不完全受支持，但基本支持相当不错*

最后是可选的事件发射器，如果需要，它会通知父组件它正在滚动。

```
 scrollTo(): void {
    const elementList = document.querySelectorAll('.' + this.scrollToClass);
    const element = elementList[0] as HTMLElement;
    element.scrollIntoView({ behavior: 'smooth' });
    this.onScroll.emit('scrolled to: ' + this.scrollToClass);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是它在父组件中作为返回顶部导航栏的一种方式的外观，当然，如果你有一个跟随屏幕滚动的粘性导航栏，你需要一个不同的类来到达顶部。:

```
<navbar class="nav"></navbar>
<main></main>
<footer>
    <app-scroll-to [linkText]="'Back to top'" [scrollToClass]="'nav'" (onScroll)="someFunction()"></app-scroll-to>
</footer 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，我希望下一部能有更实质性的东西。干杯！