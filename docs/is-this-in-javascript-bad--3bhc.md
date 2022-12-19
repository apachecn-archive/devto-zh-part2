# Javascript 中的‘this’不好吗？

> 原文：<https://dev.to/ycmjason/is-this-in-javascript-bad--3bhc>

最近，我和一个非常讨厌 Javascript 中的关键字`this`的朋友发生了争论。他声称，由于语言在不同的情况下对`this`有模糊的绑定，例如`const f = obj.g`将失去对`obj`的绑定，`obj.g.call(obj2)`将在`obj2`的上下文中调用`g`...，Javascript 中的`this`关键字简直是 Javascript 中最糟糕的事情之一。

他还声称，通过避免使用`this`关键字，Javascript 将更容易编码/维护。他提倡以下的对象创建模式:

```
function Car() {
  const car = {};

  car.position = 0;

  car.move = () => car.position++;

  return car;
}

new Car();
// or
Car(); 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，我对关键字`this`非常熟悉。我经常使用它，并欣赏`bind`、`call`和`apply`的工作方式。然而，我真的找不到反对他的模式的合理论据，因为对于那些不太了解 Javascript 的人来说，这似乎更清楚了？

你怎么想呢?给我点见解！