# 了解可选和可空在 TypeScript 中的用法

> 原文：<https://dev.to/apdharshi/understanding-the-usage-of-optional-and-nullable-in-typescript-3ii4>

<figure>[![](img/9e12a4bf1f1ada4f7fefa2ec20a5de51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g8Pol-OE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3jpAbmyZn6TozMQpw-XSxw.jpeg) 

<figcaption>由樊建华/ Freepik 设计</figcaption>

</figure>

Angular 作为一个前端 web 应用开发平台，正在被开发者广泛使用。作为一个开源平台，Angular 是用 JavaScript 的超集 TypeScript 构建的。对于 Angular 开发人员来说，理解 TypeScript 的工作原理是很有好处的。因此，在本文中，让我们讨论一下**可空的**和**可选的**参数之间的一些差异。

#### 常见的误解

大多数开发人员都误解了可空参数和可选参数。困惑出现在哪里使用“**？**”。看看这个代码块。

```
function example(x: number?, y?: string) {
 // …
} 
```

#### [后缀](#-suffix)

请仔细注意“？”有后缀。在第一个参数中，它的后缀是参数的类型，而在第二个参数中，它的后缀是参数的名称。你认为两者是一样的吗？

**答案是否定的**。第一个指示一个**可空的**参数，它也可以是一个数字、*未定义的*或*空的*。但是，第二个参数是一个**可选的**，它可以是一个字符串或者*未定义的*。

```
x // =\> number | undefined | null
y // =\> number | undefined 
```

#### Undefined vs Null

同样，有些人可能不知道“**未定义的**”和“**空值**”之间的区别。Undefined 代表可能不存在的东西。Null 用于可为 null 的事物。所以两者完全不同。

#### [@Optional]

那么 angular 中的这个**@可选**是怎么回事呢？“？”有什么区别还有@可选？正如官方文档所说，Optional 是一个构造函数参数修饰器，它将一个依赖项标记为可选。这清楚地说明了@Optional 是在 **DI** (依赖注入)的上下文中使用的。这里有一个来自[官方文件](https://angular.io/api/core/Optional)的例子。

```
@Injectable()
class Car {
  constructor(@Optional() public engine: Engine) {}
} 
```

当使用@Optional 时，即使注入的依赖项未定义，也不会发生异常。它将依赖关系视为可选的。如果我们用“**”替换**@可选的**注释，也会发生同样的事情**。注入器将搜索依赖项，当它未定义时，它将抛出异常。

遵循最佳实践和学习新东西总是好的:)