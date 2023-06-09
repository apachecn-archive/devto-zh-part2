# 莫纳德说什么？(第一部分)

> 原文：<https://dev.to/evilsoft/monad-say-what-part-1-4a61>

最近，函数式编程和复合技术引起了人们的热议，像`Functor`和`Monad`这样的术语可能会出现在你的提要中，让你不禁想，“这些人到底在说些什么？”。有了这些奇怪的名字，甚至可能有更多的外国解释，需要对代数(抽象的那种)和范畴理论有深入的理解，可能很难看出这些类型的构造如何适合我们舒适的日常 JavaScript 代码。

在这个系列中，我们将从 JavaScript 程序员的角度探索什么是`Monad`,以及它们如何在我们的日常代码中发挥巨大作用。我们将主要关注这些类型的用法，而不是它们背后的理论。

举个例子，与其努力去理解下面的定义:

> A `Monad`是`Endofunctors`的`Category`中的一个`Monoid`

我们将努力理解一个更实际的定义:

> `Monad`是一种数据类型，它允许在映射底层数据时顺序应用其“效果”或“修饰”。

现在，虽然第二个定义现在可能还不清楚，但我认为我们可以同意，努力理解这些词以及它们是如何组合在一起的含义似乎更容易理解。

当我们冒险创造自己的类型时，理解第一个定义是至关重要的。不过，如果你和我一样，我喜欢亲自动手，通过先玩一玩东西，然后在我对如何使用它们有了良好的直觉时应用理论来建立理解。在野外已经实现了许多类型，我们可以愉快地玩...却不理解背后的数学原理。

这些文章假设读者不仅理解 Javascript 语言，而且理解 JavaScript 是如何完成“currying”、“部分应用”和“函数组合”的。如果你对这些话题感到有点模糊，网上有很多资源可以帮你理清头绪。

所以，事不宜迟，让我们开始吧。

## 第一部分:代数数据类型(ADT)

很多时候当人们说“我用这个`Monad`做这个，或者那个`Monad`做那个”的时候，他们真正的意思是:“我用这个代数数据类型 *(ADT)* 做这个，那个 ADT 做那个”。当查看他们正在呈现的代码时，你会发现他们从未接触过类型的`Monad`部分，或者在某些情况下，类型甚至不是`Monad`。

我想先跟我澄清这个争论点。这似乎是一件小事，但是我发现当我们开始围绕`Monad` s 和数据类型的其他方面建立我们最初的直觉时，当我们真正意味着一些 ADT 时，将事物称为`Monad`往往会导致混乱。

在我们开始理解是什么使一个代数数据类型成为`Monad`之前，我们需要先了解一下什么是 ADT。我能想到的讨论这个话题的最好方法是提供一个关于 ADT 的简单定义。然后演示如何使用 JavaScript 中的 ADT，与(希望)更熟悉的命令式实现进行对比。

让我们来看看我们将通过示例处理的数据:

```
// data :: [ * ]
const data = [
  { id: '9CYolEKK', learner: 'Molly' },
  null,
  { id: 'gbdCC8Ui', learner: 'Thomas' },
  undefined,
  { id: '1AceDkK_', learner: 'Lisa' },
  { id: 3, learner: 'Chad' },
  { gid: 11232, learner: 'Mitch' },
] 
```

数据是混合的`Array`，可以包含任何类型的值。在这个特定的实例中，我们有三种类型:形状不同的`POJO` s (Plain ol' JavaScript Object)、一个`Null`实例和一个`Undefined`实例。

我们的示例将根据以下需求列表进行定义:

*   在其输入端接受任何类型的任何值。
*   除非数据是一个至少有一条有效记录的`Array`，否则将返回一个空的`Object`。
*   从包含的记录中返回一个以有效的`id`为关键字的有效记录的`Object`，有效地过滤掉所有无效记录。
*   我们将有效记录定义为一个带有用`id`键控的`String`的`Object`。
*   无论输入是什么，这个函数都不会抛出，并在它返回的空`Object`中提供一个合理的默认值。

根据这些要求，我们可以实现一个命令式功能，它可以实现以下功能:

1.  验证输入是一个`Array`，如果不是，返回一个空的`Object`。
2.  声明一个用于构建最终结果的`result`累加器，默认为空的`Object`。
3.  迭代提供的`Array`并对每个项目执行以下操作:
    1.  根据我们的记录标准验证项目
    2.  如果通过，将记录添加到结果中，以记录上的`id`值为关键字。否则什么都不做。
4.  返回`result`。

通过一些助手来帮助我们进行一些类型检查，我们可以提供这样一个实现:

```
// isArray :: a -> Boolean
const isArray =
  Array.isArray

// isString :: a -> Boolean
const isString = x =>
  typeof x === 'string'

// isObject :: a -> Boolean
const isObject = x =>
  !!x && Object.prototype.toString.call(x) === '[object Object]'

// indexById :: [ * ] -> Object
function indexById(records) {
  if (!isArray(records)) {
    return {}
  }

  let result = {}

  for (let i = 0; i < records.length; i++) {
    const rec = records[i]

    if (isObject(rec) && isString(rec.id)) {
      result[rec.id] = rec
    }
  }

  return result
}

indexById(null)
//=> {}

indexById([])
//=> {}

indexById([ 1, 2, 3 ])
//=> {}

indexById(data)
//=> {
//   9CYolEKK: { id: '9CYolEKK', learner: 'Molly' },
//   gbdCC8Ui: { id: 'gbdCC8Ui', learner: 'Thomas' },
//   1AceDkK_: { id: '1AceDkK_', learner: 'Lisa' }
// } 
```

正如我们所看到的，我们有一个强大的实现来满足我们的需求，并按照预期对我们提供的任何输入做出响应。

至于我们的 ADT 实现，我们将非常依赖于 [`crocks`](https://evilsoft.github.io/crocks/) 库。尽管 JavaScript 是一种全功能的编程语言，但它缺少一些在其他语言中出现的结构，这些语言不是通用语言，而是严格的功能性语言。因此，像 [`crocks`](https://evilsoft.github.io/crocks/) 这样的库通常用于处理 ADT。

下面是一个使用 ADTs 实现需求的实现:

```
const {
  Assign, Maybe, composeK, converge, isArray,
  isObject, isString, liftA2, mreduceMap, objOf,
  prop, safe
} = require('crocks')

// wrapRecord :: Object -> Maybe Object
const wrapRecord = converge(
  liftA2(objOf),
  composeK(safe(isString), prop('id')),
  Maybe.of
)

// mapRecord :: a -> Object
const mapRecord = record =>
  safe(isObject, record)
    .chain(wrapRecord)
    .option({})

// indexById :: [ * ] -> Object
const indexById = records =>
  safe(isArray, records)
    .map(mreduceMap(Assign, mapRecord))
    .option({})

indexById(null)
//=> {}

indexById([])
//=> {}

indexById([ 1, 2, 3 ])
//=> {}

indexById(data)
//=> {
//   9CYolEKK: { id: '9CYolEKK', learner: 'Molly' },
//   gbdCC8Ui: { id: 'gbdCC8Ui', learner: 'Thomas' },
//   1AceDkK_: { id: '1AceDkK_', learner: 'Lisa' }
// } 
```

我希望这两个实现之间的一个区别是 ADT 实现中缺少熟悉的流控制和逻辑模式。像`for`循环和`if`语句这样的东西在第二个实现中不会出现一次。它们仍然存在，当然它们仍然存在，但是当使用 ADT 时，我们将这些流/逻辑编码为特定的类型。

例如，注意在几个地方使用的`safe`函数？看看传递给这些调用的第一个参数的谓词函数。注意，这里也进行了同样的检查，但是我们使用的是返回名为`Maybe`的 ADT 的`safe`函数，而不是`if`。

您可能已经注意到的另一件事是在第二个实现中缺少状态。声明的每个变量都是一个函数，而不是一个 JavaScript 值。我们在最初的实现中使用了两位状态，`result`来组合我们的最终结果，还有一个叫做`rec`的小助手，它只是清理代码，让我们不必从`Array`中引用索引值。

通过使用函数`mreduceMap`将每个记录折叠到一个`Assign`类型上，我们能够摆脱对`for`循环和`result`变量的需要。`Assign`让我们以类似于`Object.assign`在普通 JavaScript 中的方式组合`Object`，消除了跟踪类似`result` `Object`的累加器的需要。现在我们有了积累的方法，我们可以依靠`mreduceMap`来移除`for`循环。

`Maybe`、`Assign`、折叠等。现在不需要理解这些东西。我之所以提到它们，是因为我想告诉大家，原始实现中的每个模式都存在于 ADT 版本中，这里没有任何神奇之处。当我们用 ADT 编码时，我们通过在 ADT 中对它们进行编码，去除了大量的机械位，如累积、逻辑、控制流和状态杂耍，并让类型为我们处理所有的“管道”。

我希望了解的最后一件事是我们如何使用看起来像是[流畅的 api](https://martinfowler.com/bliki/FluentInterface.html) 将我们的操作链接在函数`mapRecord`和`indexById`中。看到这样的代码可能会让我们相信我们正在像典型的面向对象程序员一样使用传统的`Object`和类。当您听到这些操作被称为方法时，它甚至得到了加强(所有的 [crocks](https://evilsoft.github.io/crocks/) 文档都是这样做的)。这些直觉和误导性的描述会妨碍我们理解 ADT 在日常代码中的使用方式。

下一次，我们将深入探讨 ADT 的用法，从面向对象的程序员看待`Object`的角度来看，ADT 不是`Object`的。

### 以锻炼为乐

1.  采用第一个 POJ (Plain ol' JavaScript)函数，通过使用`Array.prototype`上可用的`reduce`方法移除`for`循环。注意`result`变量发生了什么，以及`{}`的默认值是如何应用的。
2.  以第一个 POJ 函数为例，在不使用定时器(`setTimeout`或`setInterval`)的情况下，将其重构为你能想到的*最低效的*实现。当你重构的时候，想想你认为最低效的是什么*。*
3.  使用第一个`POJ`函数或者你在练习 1 中的重构，识别可能存在于它们自己的函数中的离散动作/转换。然后创建这些函数并重构主函数来使用它们。

### 附加练习(也是为了好玩)

1.  我们使用第三方库的类型检查谓词函数来进行类型检查。选择一个我们使用的谓词，并实现您自己的版本，在您的实现中抛出不同类型的不同值，看看它是否如预期的那样运行。
2.  如果你碰巧精通像 [ramda](https://ramdajs.com/) 或 [lodash-fp](https://github.com/lodash/lodash/wiki/FP-Guide) 这样的库，就用你熟悉的库在函数中实现同样的行为。将您的函数的结果与上述 ADT 版本的以下无点数版本进行比较:

```
// wrapRecord :: Object -> Maybe Object
const wrapRecord = converge(
  liftA2(objOf),
  composeK(safe(isString), prop('id')),
  Maybe.of
)

// mapRecord :: a -> Object
const mapRecord = compose(
  option({}),
  chain(wrapRecord),
  safe(isObject)
)

// indexById :: [ * ] -> Object
const indexById = records => compose(
  option({ error: true }),
  map(mreduceMap(Assign, mapRecord)),
  safe(isArray),
) 
```