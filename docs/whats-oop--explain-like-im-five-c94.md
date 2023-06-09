# OOP 是什么？像我五岁一样解释

> 原文：<https://dev.to/superfola/whats-oop--explain-like-im-five-c94>

**为什么**我会写这篇文章？面向对象编程非常有用，但也是一个广泛的主题，在我的工程学校，我发现理解这个概念有点困难。

# 一个类描述一个对象

## ...但不是物体本身。

当你在 OOP 中编写我们称之为`class`的东西时，它基本上是一个构建*对象*的指导手册。让我们考虑一把椅子:椅子本身就是对象——有时人们说它是类的一个*实例*——而帮助你构建椅子的手册就是类。

## 一个类也描述一个对象的行为

既然你已经理解了一个*类*和一个*对象*之间最原始的区别，我必须给一个*类*的定义添加一些东西:它不仅仅**描述了一个对象的形状，它还描述了它的行为。为了解释这一点，我拿一种动物来说:鸭子。它的手册应该是这样的:** 

```
Manual of: Duck
    - 2 webbed feet
    - 1 beak on a head at the end of a neck, linked to the body
    - feather on the body
    - 2 wings in feather, one on each side of it
    - rectangular shape for the body 
```

Enter fullscreen mode Exit fullscreen mode

我知道，我们的鸭子会是这个样子，但是，它看起来很好，不是吗？

```
 ___
   ___/   \
  /__  O   |
  \__     /
     \   /
    _|   |_      _____
   |       \\||//     |
   |       \\||//     |
   |___  __ __________|
      /..\ \\ 
```

Enter fullscreen mode Exit fullscreen mode

它的行为应该遵循这些规则:

```
Behaviour of: Duck
    - cackle with its beak
    - swim with its webbed feet
    - eat and drink with its beak
    - fly with its wings
    - walk with its feet 
```

Enter fullscreen mode Exit fullscreen mode

所以*类*是这两者的结合:

*   手册
*   行为

让我们把这本手册放在你的脑海里，我们以后会需要它的。

# 一些技术词汇

我们看了手册，造了一只鸭子，以及它应该有的行为。所有这些都属于一个单独的*类*:

*   手册中描述的鸭子的所有部分都将在我们的鸭子*类*中有一个对应的*属性*
*   它的行为将被翻译成同一个*类*中的*方法*鸭子

所以，有了这个新词汇，我们现在有了这个:

```
class: Duck
    attributes:
        - number of feet = 2
        - number of beak = 1
        - beak position = end of neck
        - number of neck = 1
        - position of neck = top left corner of body
        - material covering body = feather
        - number of wings = 2
        - wings material = feather
        - position of wing 1 = left side of body (centered)
        - position of wing 2 = right side of body (centered)
        - body shape = rectangular

    methods:
        - cackle with beak
        - eat with beak
        - drink with beak
        - swim with feet
        - walk with feet
        - fly with wings 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有了我们的*类*鸭的定义！我们可以创建尽可能多的鸭子，但是你可能已经注意到它们看起来都是一样的。事实上，我们没有为鸭子的年龄、颜色和大小添加属性...

# 扩展我们的类

## 继承的概念

我们已经看到我们可以用一个类来描述一只鸭子。有时候，编写这些类会花很长时间，因为我们需要很多属性和方法，而且通常其他人已经为我们编写了这些类。

这意味着我们可以获得代码并使用它！但是...如果我想要一个“超级鸭子”类，它可以用嘴里射出的激光摧毁建筑物，那该怎么办？我是否需要用与类 Duck 相同的属性和方法重写整个类 SuperDuck，但还要添加与 laser 相关的属性和方法？

答案是否定的！由于继承，我们可以说“SuperDuck 是一个类。它的妈妈是鸭子。超级鸭子的嘴里射出一束激光”。这意味着我们可以定义一个类作为另一个类的子类，它将获取另一个类(称为“父类”)的所有属性和方法，并添加我们的属性和方法。

```
class: SuperDuck
    parents: Duck

    attributes:
        - laser emitter
        - batteries for laser

    methods:
        - emit laser
        - reload laser's baterries 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经注意到我写了“parents”而不仅仅是“parent”:是的，一个类可以有多个父类！这样，我们可以有一个动物类，一个以动物为父类的鸭子，另一个超级英雄类，以及一个从鸭子类和超级英雄类继承而来的超级鸭子类，并且我们不需要为这个超级鸭子编写任何代码:)