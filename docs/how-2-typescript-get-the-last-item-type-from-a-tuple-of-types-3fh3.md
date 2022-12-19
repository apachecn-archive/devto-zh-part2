# How 2 TypeScript:从类型元组中获取最后一个项类型

> 原文：<https://dev.to/miracleblue/how-2-typescript-get-the-last-item-type-from-a-tuple-of-types-3fh3>

(*注意:作者假设您使用的是 TypeScript 3.x* )

嘿你！是否要从类型元组中获取最后一项的类型？

你当然知道。

```
type Stuff = [number, boolean, string]
type OtherStuff = [string, number, object, boolean] 
```

Enter fullscreen mode Exit fullscreen mode

这里我有两个元组类型，一个有 3 个条目，一个有 4 个条目。`Stuff`中的最后一个类型是`string`,`OtherStuff`中的最后一个类型是`boolean`。

我们如何获得这些最后的项目类型？

令人惊奇的是，我们可以这样做的一个方法是——如果我们在写的时候知道元组的长度——使用一个数值作为索引来查找某个位置的类型。我知道，太棒了。像这样:

```
type LastStuff = Stuff[2] // => string
type LastOtherStuff = OtherStuff[3] // => boolean 
```

Enter fullscreen mode Exit fullscreen mode

有点像普通的数组查找！

但是如果你*不知道*元组的长度呢？嗯（表示踌躇等）...我们如何在编译时让 TypeScript 告诉我们长度，然后让我们使用该长度来挑选最后一项？

从[这个令人惊奇的 HKTs 库](https://github.com/pelotom/hkts/blob/master/src/index.ts#L38)中，我能够得到元组的长度，作为一个数字文字:

```
type GetLength<original extends any[]> = original extends { length: infer L } ? L : never

type Stuff = [number, boolean, string]
type OtherStuff = [string, number, object, boolean]

type LengthStuff = GetLength<Stuff> // => 3 
```

Enter fullscreen mode Exit fullscreen mode

注意这部分中的`infer`关键词:`original extends { length: infer L }` -我在[之前的 How 2 打字稿](https://dev.to/miracleblue/how-2-typescript-serious-business-with-typescripts-infer-keyword-40i5)中更多地谈到了什么是`infer`关键词，所以如果你感到困惑，我希望这能给你一点启发:)

但是请记住，列表是零索引的，所以如果我们想要最后一项，我们将需要一种方法来做`L - 1`。在 ts 中，我们不能只在类型级别进行直接的算术运算，所以这是行不通的:`type LastItem = Stuff[GetLength<Stuff> - 1]`(这是 TS 的一个语法错误)。所以我们需要某种地图。

我认为最好的方法是从一个数字文本到前一个数字文本的类型映射。我在[的这个简单类型库](https://github.com/andnp/SimplyTyped/blob/master/src/types/numbers.ts)中找到了这样一个类型，它本质上为我们提供了从 0 到 63 之间的任意值`n - 1`。使用这个，我可以输入我们推断为`L`的长度，并获取该列表的最后一个索引，并使用它来查找最后一个类型。像这样:

```
// Borrowed from SimplyTyped:
type Prev<T extends number> = [-1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62][T];

// Actual, legit sorcery
// Borrowed from pelotom/hkts:
type GetLength<original extends any[]> = original extends { length: infer L } ? L : nevertype GetLast<original extends any[]> = original[Prev<GetLength<original>>]

// Here are our test-subject tuples:
type Stuff = [number, boolean, string]
type OtherStuff = [string, number, object, boolean]

// How long is the `Stuff` tuple?
type LengthStuff = GetLength<Stuff> // => 3

// What is the last element of each tuple?
type LastStuff = GetLast<Stuff> // => string
type LastOtherStuff = GetLast<OtherStuff> // => boolean 
```

Enter fullscreen mode Exit fullscreen mode

嘣！它并不完美，但这是我用最新的打字稿版本能做的最好的了。

## 复思:

实际上，我尝试了很多很多不同的方法来实现这个目标。我使用的第一个版本使用了来自 [HKTs 库](https://github.com/pelotom/hkts/blob/master/src/index.ts#L58)的“TupleTable”来返回我最后一个条目的推断类型，使用了基于元组长度的查找。对于这项任务来说，这感觉有点大材小用，而且只限于 10 项。如果我想增加限制，那么元组表必须变得非常非常大。

相反，我四处寻找做一点类型级算术的方法。如果我们说任何给定元组的长度是`n`，那么任何给定元组的最后一项在`n - 1`。我们通过无数次在 JS 中查找数组的最后一项来了解这一点。在 TypeScript 中，我们还不能对数字文本类型进行任何算术运算，所以如果我们继续这样做，我们将需要一种数字文本之间的映射(这样，给定`5`，我们将得到`4`)。它的长度也是有限的，但是如果我们将最大长度增加 1(举例来说)，希望代码的复杂性不会显著增加。

在搜索 GitHub 后，我找到了我所需要的。简单类型中的`Prev`类型[将让我们传入一个数字(从 0 到 63 之间的任何数字),它会给出它前面的数字。所以，给定`5`，你得到`4`。在我们在 TypeScript 中获得内置的“后继者/前任者”类型之前，我认为这已经是最好的了。](https://github.com/andnp/SimplyTyped/blob/master/src/types/numbers.ts)

与我在 Haskell 和 PureScript 中习惯的相比，我实际上对 TypeScript 的类型系统的能力感到非常惊讶。相比之下，仍然有许多限制，也许永远都会有，但有一点是肯定的:TypeScript 可以在这个激烈的类型化语言世界中保持自己的地位。

下次见！