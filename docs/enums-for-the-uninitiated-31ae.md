# 外行人的列举

> 原文：<https://dev.to/kahlil/enums-for-the-uninitiated-31ae>

在 TypeScript 之前，我从未使用或听说过枚举。所以不用说，当我在文件中看到他们时，我很困惑。

我太习惯于用接口来描述数据结构的形状，以至于我不明白使用枚举有什么好处。

我也很困惑，因为枚举不仅是一种类型，也包含值。我不认为 TypeScript 给了我保存值的东西。

所以为了理解它，我不得不做更多的研究，以下是我对枚举的理解:

枚举给你一个简单的方法来对常量进行逻辑分组，以使你的代码更容易阅读和理解。

所以如果你的程序中有一堆逻辑上属于一起的常量值。例如，像一周中的日期、方向(上、下、左、右)或异步操作的返回类型，您可以使用枚举将它们分组并在代码中使用。

枚举一口气给你类型和数据结构，而不需要做很多工作。

如果您声明了以下枚举:

```
enum Directions {
  Up,
  Down,
  Left,
  Right
} 
```

Enter fullscreen mode Exit fullscreen mode

你正在一气呵成地做一堆事情:

*   声明类型`Directions`
*   将成员分配给枚举
*   向枚举成员隐式赋值

默认情况下，分配的值是数字。从 0 开始，随后的每个属性递增。

这有点像 JavaScript 对象，只是值是自动赋值的。

您可以通过将自定义编号分配给第一个属性来更改分配编号的起点。

```
enum Directions {
  Up = 1,
  Down,
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着增量从 1 而不是 0 开始。如果您想确保第一个值永远不会为 falsy，这可能很有用。

您也可以手动为枚举的所有属性赋值。它们可以是类型`number`或`string`。

所以现在你可以在你的代码中使用这些常量，比如:

```
if (direction === Directions.Up) {
  // Do things.
} 
```

Enter fullscreen mode Exit fullscreen mode

简单方便。

TypeScript 文档是这样说的:

> 枚举允许我们定义一组命名的常量。使用枚举可以更容易地记录意图，或者创建一组不同的事例。

在最近的项目中使用了一点枚举之后，我发现它们非常方便和有用。我认为特别是“记录意图”是他们以一种方便的方式做得很好的事情。

关于带数值的枚举，一个很酷的事情是，你可以通过用数字访问它来检索数值的成员名，在我们的例子中，用数字`1`访问一个`Directions`成员会返回字符串`"Up"`。

```
Directions[1] // returns "Up" 
```

Enter fullscreen mode Exit fullscreen mode

这很酷，但迄今为止，我从未需要过它。为了在运行时实现这一点，枚举被编译成:

```
var Directions;
(function (Directions) {
Directions[Directions["Up"] = 0] = "Up";
})(Directions || (Directions = {}));
var direction = Directions.Up;
var nameOfA = Directions[direction]; // "Up" 
```

Enter fullscreen mode Exit fullscreen mode

这是一个被物体传递的生命。然后，该函数将数字分配给成员名称，并将成员名称分配给数字。

这条线同时做两件事。

```
Directions[Directions["Up"] = 0] = "Up"; 
```

Enter fullscreen mode Exit fullscreen mode

`Directions["Up"] = 0`将数字分配给成员名，因为这一行是一个 JavaScript 表达式，所以它会计算出一个值，这个值就是分配的数字。

这意味着整行求值为`Directions[0] = "Up";`。

所以在 JavaScript 中你最终得到的是:

```
{
  0: "Up",
  Up: 0
} 
```

Enter fullscreen mode Exit fullscreen mode

很漂亮吧？但是假设资源稀缺，你不需要访问成员名，那么你可以使用 *`const`枚举*。

他们根据各自的价值观进行评估。枚举代码在编译过程中被完全移除。这意味着:

```
const enum Directions {
  Up = 1,
  Down
}
if (direction === Directions.Up) {
  // Do things.
} 
```

Enter fullscreen mode Exit fullscreen mode

被编译为:

```
if (direction === 1) {
  // Do things.
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我对伊努斯的看法。在习惯使用枚举之后，我发现它们总是有用的地方，我真的很喜欢它们让意图变得非常明确的方式。枚举可以让你的代码更容易阅读。