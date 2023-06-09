# 为什么应该考虑使用类来声明@ngrx/store 的状态

> 原文：<https://dev.to/michaeljota/why-you-should-consider-using-classes-to-declare-the-state-of-ngrxstore--2bjg>

你好。好久不见。我一直在忙着设置我的博客，对不起。我从头开始做 UI，因为我不喜欢任何主题，事实上，我也不喜欢我的，但我们必须从某个地方开始。不管怎样，这是帖子，希望你喜欢。

* * *

> 最初发布在[我的博客](https://michaeljota.github.io/why-you-should-consider-use-classes-to-declarate-the-state-of-the-store/)

* * *

## 简介

这个星期我一直在玩`@ngrx`，它的商店，效果和继续，试图整合到一个英雄之旅的版本。自从我最后一次使用它已经一年多了，我不得不说，我正在成为一个更有生产力的开发者，这也已经发展成为一个更好的平台。observables 现在使用管道操作符，这使得一切都更容易理解。

我看了一些教程，读了很多关于商店最近版本的变化，我不知道。我两天后改变申请状态。你可能认为这是很多时间，但我不知道如何使用效果，而且我想在这一个上遵循棱角风格指南，并且`@ngrx`的示例应用程序不遵循它，并且大多数 redux 示例是为 React 项目制作的。

我对 Angular 应用程序的结构有自己的想法，但它们受到 React 应用程序结构的严重影响。目前的 Angular 代码风格已经改变了我的想法，但这是另一篇文章。

## 推荐方式

在这篇文章中，我想说的是关于声明状态的推荐方法。

```
export interface AppState {
    counter: number;
}

export const initialAppState: AppState {
    counter: 3,
} 
```

Enter fullscreen mode Exit fullscreen mode

为什么？好吧，原因如下。建议您使用 POJO 来声明状态和操作。但是，声明一个不可变的 Typescript 类来声明状态真的有坏处吗？

## 上等

```
export class AppState {
    public readonly counter: number = 3;
} 
```

Enter fullscreen mode Exit fullscreen mode

当在商店配置中设置时，状态也初始化为 POJO，所以这里没有副作用。

```
import { StoreModule } from '@ngrx/store';

export class AppState {
    public readonly counter: number = 3;
}

const reducers = (state = new AppState(), action) => state; // Best reducer ever!

@NgModule({
  imports: [
    StoreModule.forRoot(reducers, {
      initialState: new AppState(),
    })
  ]
})
export class AppStoreModule {} 
```

Enter fullscreen mode Exit fullscreen mode

我觉得这样更清楚。您确保设置了所有必需的属性，并且只需要在需要时调用构造函数。而且，您可以在任何需要的地方使用 AppState 作为接口。

## 为 AOT

如果您想将这种模式与 Angular AOT 一起使用，您需要使用 spread 操作符来引入新的状态，因为 Angular Compiler 需要分析来自 decorators 的所有元数据。你需要使用函数归约器，而不是箭头函数赋值。

```
function reducers(state = new AppState(), action) {
 return state; // Still the best reducer ever!
}

@NgModule({
  imports: [
    StoreModule.forRoot(reducers, {
      initialState: { ...new AppState() },
    })
  ]
})
export class AppStoreModule {} 
```

Enter fullscreen mode Exit fullscreen mode

你认为这是好事还是坏事？为什么？我想看看你的评论。