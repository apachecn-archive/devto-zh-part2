# 角度 2+中的模板参考变量

> 原文：<https://dev.to/wiaio/template-reference-variables-in-angular-2-1dhf>

模板引用变量是 Angular 2+的一个非常有用的特性，它允许您保存对 DOM 元素或 Angular 组件实例的引用。

要声明模板引用变量，请编写一个带有名称的散列符号作为元素的属性，如下所示:

`<input type="text" #myInput>`

现在，您可以在模板中的任何地方访问该变量。在下面的例子中，输入元素的值显示在它的旁边，输入 DOM 元素被传递给`onClick`函数。

```
<input type="text" #myInput> {{ myInput.value }}
<button (click)="onClick(myInput)">Submit</button> 
```

Enter fullscreen mode Exit fullscreen mode

使用`ViewChild`装饰器，模板引用变量也可以在组件中被引用。在下面的例子中，对输入元素的引用将在`this.myInput`中可用。

```
import { ViewChild, ElementRef } from '@angular/core';

@ViewChild('myInput') private myInput: ElementRef; 
```

Enter fullscreen mode Exit fullscreen mode

在组件上使用引用变量会给你一个对实际组件实例的引用，让你可以访问它的所有方法和属性。

`<my-component #myComponent></my-component>`