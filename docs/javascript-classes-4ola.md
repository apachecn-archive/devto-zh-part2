# JavaScript 类

> 原文：<https://dev.to/mcarpenter/javascript-classes-4ola>

#### 什么是 JavaScript 类？

JavaScript `class`是一个函数，它可以像函数表达式或函数声明一样被定义。`class`语法有两个部分:

1.  类别声明`class Car {}`
2.  类表达式*未命名* `let Car = class {}`
3.  类表达式*命名为* `let Car = class Toyota {}`

*类名应该以大写字母*开头

要声明一个类，可以在类名(“Car”)中使用`class`关键字。类表达式可以命名(“Toyota”)或未命名(“Car”)。给类表达式起的名字对于类的主体是局部的。

`class`功能使用`constructor`和`new`关键字为要创建的对象创建模板。使用`class`功能有好处。少写代码，避免错别字，可读性更好，避免重复自己(干)。

#### 构造函数

驻留在类中的构造函数方法用于创建和初始化用`class`创建的对象。每个`class`只能有一个构造函数方法。构造函数根据您设置的预定义标准构建您的对象。参见下面的例子，

```
class Car{
    constructor(make, model, color) {
        this.make = make;
        this.model = model;
        this.color = color;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我稍后调用这个函数时，我传递给它的参数将是我的汽车对象的品牌、型号和颜色。由此创建的对象将类似于下面的示例，

```
const camry = new Car('Toyota', 'Camry', 'Blue');

console.log(camry);
//Car {make:"Toyota", model: "Camry", color: "Blue"} 
```

Enter fullscreen mode Exit fullscreen mode

#### 新增

`new`操作符创建一个用户定义的对象类型或一个具有构造函数的内置对象类型的实例。`new`操作符实际上是说他们创建了我们上面构建的这个`class`的一个新实例。`constructor`告诉`new`操作符使用传入的参数来构建对象。

#### 结论

使用`class`功能有助于创建对象。如果我们必须制造 20 种不同颜色和年份的汽车，那会怎么样！不仅创建要花一段时间，如果我们想更新一个的话，我们会有太多的代码要看。

查看下面这些有用的资源-
[MDN 类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
[JavaScript.info](https://javascript.info/class)