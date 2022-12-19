# Angular:没有额外元素的结构指令，如*ngIf 和*ngFor

> 原文：<https://dev.to/wiaio/angular-structural-directives-like-ngif-and-ngfor-without-the-extra-elements-5c24>

我发现 Angular 的一个缺点是，您经常会发现自己在模板中添加了不必要的 DOM 元素，这样您就可以在一个`*ngIf`语句中包装一个元素块。参见下面的例子:

```
<div *ngIf="someVariable">
  <p>Lorem ipsum dolor sit amet consectetur adipisicing elit.</p>
  <p>Lorem ipsum dolor sit amet.</p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这里不一定需要包装`div`,这些不需要的元素会导致混乱的标记，有时会使编写 CSS 规则更加困难。

解决方案是`ng-container`指令，一个分组元素**根本不会被添加到 DOM** 中，因此不会影响你的布局。这是上面的例子，这次使用了`ng-container`。

```
<ng-container *ngIf="someVariable">
  <p>Lorem ipsum dolor sit amet consectetur adipisicing elit.</p>
  <p>Lorem ipsum dolor sit amet.</p>
</ng-container> 
```

Enter fullscreen mode Exit fullscreen mode

我遇到的另一个问题是，在同一个元素上不能有一个以上的结构指令，例如，不能对同一个元素同时应用`*ngIf`和`*ngFor`。下面的代码将导致一个错误:

```
<ul>
  <li *ngFor="let item of items" *ngIf="item.foo === 'bar'">{{ item.text }}</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

我们将再次使用`ng-container`指令:
，而不是添加额外的包装 DOM 元素

```
<ul>
  <ng-container *ngFor="let item of items">
    <ng-container *ngIf="item.foo === 'bar'">
      <li>{{ item.text }}</li>
    </ng-container>
  </ng-container>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

使用这种技术，可以确保 DOM 中没有不必要的元素。