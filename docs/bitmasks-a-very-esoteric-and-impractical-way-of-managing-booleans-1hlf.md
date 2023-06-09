# 位掩码:一种非常深奥(并且不切实际)的管理布尔值的方法

> 原文：<https://dev.to/somedood/bitmasks-a-very-esoteric-and-impractical-way-of-managing-booleans-1hlf>

你有没有问过自己[位运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators)是干什么用的？为什么像 JavaScript 这样的高级语言会需要这么低级的操作符呢？首先，它实际上在 JavaScript 中有自己的用例。大多数只是不像其他人那么明显。事实上，大多数都不明显，除非你真的试着眯着眼睛看电脑屏幕。相信我，我试过了。我不是在开玩笑。在我相对较短的 JavaScript 经验中(撰写本文的 3 年时间)，在一般情况下很少会出现按位运算符的实例**。我可能看得不够深入，但我确实很清楚为什么会这样。到本文结束，你会明白为什么会这样。**

# 按位运算符

**注意:**我不要求你在这个问题上有广泛的知识，但是我会假设你已经至少在某种程度上熟悉了[二进制数字系统](https://www.mathsisfun.com/binary-number-system.html)和[位运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators)。如果没有，我强烈建议你读一点*(看我在那里做了什么？)*在继续本文的其余部分之前。

按位运算符允许我们操作构成二进制数的各个位。快速回顾一下，这里有一个常用位运算符的“表格”。

```
// I will use the binary notation prefix ("0b") a lot in this article.
const num1 = 0b1010; // 10
const num2 = 0b1111; // 15

// NOT num1
~num1; // 0b0101 (complement) === -11

// num1 AND num2
num1 & num2; // 0b1010 === 10

// num1 OR num2
num1 | num2; // 0b1111 === 15

// num1 XOR num2
num1 ^ num2; // 0b0101 === 5

// Bit-shift to the left by 1
num1 << 1; // 0b10100 === 20

// Bit-shift to the right by 1
num >> 1; // 0b0101 === 5 
```

Enter fullscreen mode Exit fullscreen mode

我的意思是这很好，而且都是为了每天学习新的东西，但是你什么时候会用到这些知识呢？按位运算符有实际应用吗？简而言之，不。尽管它在代码精简、内存优化以及其他一些用例中很有用，但是通过使用位操作符，你选择了可读性更差的代码。它只是读起来更神秘，因为你必须将你的“十进制模式”大脑设置为“二进制模式”。尽管如此，这并没有阻止我们，对不对？我们都是来学习的。所以事不宜迟，我提出**位掩码**。

# 过度设计一个简单的问题

老实说，我对“位掩码”没有一个简单的定义。如果你问我的话，这是一个非常奇怪的怪物。对我来说，位掩码可以看作是一个查询。使用位掩码意味着查询在某个二进制数中找到的位。如果你对这个定义感到困惑，我不怪你。我不得不承认这不是最好的定义。如果你能想到一个更好的，请在下面留下评论。我很乐意更新这篇文章，以明确包括您的定义。

无论如何，一个定义如果没有它的补充例子是没有价值的。假设我们有一个对象，它存储与应用程序中的配置相对应的布尔值。

```
// Mock app settings
const config = {
  isOnline: true,
  isFullscreen: false,
  hasAudio: true,
  hasPremiumAccount: false,
  canSendTelemetry: true
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们的工作到此为止。我们可以把它保存在一个 JSON 文件中。这是简单的实现。不过，我们可以用位掩码来[【过度工程】](https://en.wikipedia.org/wiki/Overengineering)这个问题。在 JavaScript 中，通过将数字类型传递给`Boolean`函数，可以将其显式转换(或强制转换)为布尔值。注意，在这种情况下，`Boolean`没有被用作[构造函数](https://www.w3schools.com/js/js_object_constructors.asp)。它只是一种将数字类型(或者实际上是任何类型)转换成等价的布尔值 *"truthiness"* 的方法。例如:

```
Boolean(-2); // true
Boolean(-1); // true
Boolean(0); // false
Boolean(1); // true
Boolean(2); // true
Boolean(Math.PI); // true
Boolean(Number.MAX_SAFE_INTEGER); // true 
```

Enter fullscreen mode Exit fullscreen mode

因为`0`本身并不完全是一个“真”值，所以它的计算结果是`false`。这种关系让我们知道如何将一组布尔值转换成一个数字。我们可以将应用程序设置存储为单个数字，而不是将其存储为一个对象。是的，你听到了，或者说读到了，没错。首先，我们认为布尔型是`1` s 和`0` s，其中`1`是`true`而`0`是`false`。这些`1`和`0`从左到右对应于`config`对象中的每个属性。

```
// For reference only
const config = {
  isOnline:          true,
  isFullscreen:      false,
  hasAudio:          true,
  hasPremiumAccount: false,
  canSendTelemetry:  true
};

// isOnline:          1
// isFullScreen:      0
// hasAudio:          1
// hasPremiumAccount: 0
// canSendTelemetry:  1
// Thus, we have the binary number 0b10101.
let configNumber = 0b10101; // 21 
```

Enter fullscreen mode Exit fullscreen mode

# 位掩码

**注:**文章诡异的地方来了。这是我施展黑魔法的地方。我希望你已经充分伸展了你的大脑肌肉，因为从现在开始你将会用它进行一次剧烈的锻炼。随意反复读一些部分。至少可以说，这是一个相当困难的话题。

既然我们已经将整个对象简化为一个数字，我们可以对它使用按位运算符。但是为什么，你会问吗？这就是位屏蔽的本质。

位掩码是“选择”您感兴趣的位的一种方式。当选择单个特定位时，它总是 2 的幂，因为 2 的任何幂对应于“开启”的特定位。由于向左移位实质上是乘以 2(类似于 2 的幂次幂)，您可以将向左移位视为“选择”您感兴趣的位的一种方式。

```
// Selecting the 1st bit from the right
// 2 ** 0
// 1 << 0
0b00001 === 1;

// Selecting the 2nd bit from the right
// 2 ** 1
// 1 << 1
0b00010 === 2;

// Selecting the 3rd bit from the right
// 2 ** 2
// 1 << 2
0b00100 === 4;

// Selecting the 4th bit from the right
// 2 ** 3
// 1 << 3
0b01000 === 8;

// Selecting the 5th bit from the right
// 2 ** 4
// 1 << 4
0b10000 === 16; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想选择多个位，我们也可以这样做。

```
// Selecting the 1st and 5th bit from the right
0b10001 === 17;

// Selecting the 3rd and 4th bit from the right
0b01100 === 12;

// Selecting the 2nd, 4th, and 5th bit from the right
0b11010 === 26;

// Selecting the 1st, 2nd, and 4th bit from the right
0b01011 === 11;

// Selecting ALL the bits
0b11111 === 31; 
```

Enter fullscreen mode Exit fullscreen mode

# 获取数值

位屏蔽允许我们提取`configNumber`数字中的单个位的值。我们如何做到这一点？假设我们想要得到`hasAudio`的值。我们知道`hasAudio`房产位于`configNumber`右侧第三位。

```
let configNumber = 0b10101; // 21

// Shifting 0b1 to the left 2 times gives the 3rd bit from the right
const bitMask = 0b1 << 2; // 4

// Since we know that the 3rd bit from the right corresponds to the hasAudio property...
const query = configNumber & bitMask; // 4

// ...we can test its "truthiness" by using the AND operator.
const truthiness = Boolean(query); // true

// The truthiness IS the value we want to extract.
truthiness === config.hasAudio; // true 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，你可能会问...

> “那么如果它返回`4`呢？
> “胁迫到`true`又怎么样？"
> "是‘真’又怎么样？"

如果你是在问这个问题，那么你只是回答了你自己的问题。`4`已被胁迫为`true`。这是原始`config`对象中`hasAudio`属性的确切值。我们已经通过位屏蔽成功提取了`hasAudio`属性的值。

那么，如果我们试图查询一个“虚假”的属性，比如`isFullscreen`，会发生什么呢？位屏蔽会在原始的`config`对象中反映相同的值吗？事实上，的确如此。我们知道`isFullScreen`属性位于`configNumber`中从右数第四位。

```
let configNumber = 0b10101; // 21

// Shifting 0b1 to the left 3 times gives the 4th bit from the right
const bitMask = 0b1 << 3; // 8

// Since we know that the 4th bit from the right corresponds to the isFullscreen property...
const query = configNumber & bitMask; // 0

// ...we can test its "truthiness" by using the AND operator.
const truthiness = Boolean(query); // false

// The truthiness IS the value we want to extract.
truthiness === config.isFullscreen; // true 
```

Enter fullscreen mode Exit fullscreen mode

通过在我们的`bitMask`中选择多个位，我们可以变得更加疯狂，但是我将把它作为一个练习留给你去思考。

你可能会注意到这里的一个模式。`AND`位运算符的结果决定了`query`的`truthiness`。`truthiness`本质上是我们首先试图获得的财产的实际价值。是的，我知道；这是黑魔法。我也有同样的反应。它太聪明了，我当时完全不能理解。

现在我们知道了如何从一个特定的位中提取一个布尔值，那么我们如何操作一个位呢？

# 切换值

当我们想要切换位时，也遵循同样的逻辑。我们仍然使用位掩码来选择我们感兴趣的位，但是对于我们的`query`，我们使用`XOR`位运算符(`^`)而不是`AND`位运算符(`&`)。

假设我们想要切换`canSendTelemetry`属性。我们知道它位于右数第一位。

```
let configNumber = 0b10101; // 21

// Shifting 0b1 to the left 0 times gives the 1st bit from the right,
// which corresponds to the canSendTelemetry property
const bitMask = 0b1 << 0; // 1

// Toggling the 1st bit from the right
const query = configNumber ^ bitMask; // 20

// Setting the query as the new configNumber
configNumber = query; 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们试图从新的`configNumber`中提取`canSendTelemetry`属性，我们会发现它不再被设置为`true`。我们已经成功地将该位从`true`切换到`false`(或者说从`1`切换到`0`)。

# 现在都在一起了

一遍又一遍地做这件事绝对是乏味的。因为我们都想节省一些击键次数，所以让我们创建一些实用函数来完成所有这些工作。首先，我们将编写两个提取位的“真度”的实用函数:一个在给定位掩码的情况下提取“真度”，而另一个在给定被提取位的零索引位置(从右边开始)的情况下提取“真度”。

```
/**
 * Extracts the "truthiness" of a bit given a mask
 * @param {number} binaryNum - The number to query from
 * @param {number} mask - This is the bitmask that selects the bit
 * @returns {boolean} - "Truthiness" of the bit we're interested in
 */
function getBits(binaryNum, mask) {
  const query = binaryNum & mask;
  return Boolean(query);
}

/**
 * Extracts the "truthiness" of a bit given a position
 * @param {number} binaryNum - The number to query from
 * @param {number} position - This is the zero-indexed position of the bit from the right
 * @returns {boolean} - "Truthiness" of the bit we're interested in
 */
function getBitsFrom(binaryNum, position) {
  // Bit-shifts according to zero-indexed position
  const mask = 1 << position;
  const query = binaryNum & mask;
  return Boolean(query);
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们编写一个用于切换一位或多位的实用函数。该函数返回新的`binaryNum`，它是切换所选位的结果。

```
/**
 * Returns the new number as a result of toggling the selected bits
 * @param {number} binaryNum - The number to query from
 * @param {number} mask - This is the bitmask that selects the bits to be toggled
 * @returns {number} - New number as a result of toggling the selected bits
 */
function toggleBits(binaryNum, mask) {
  return binaryNum ^ mask;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以在前面的例子中使用这些效用函数。

```
const config = {
  isOnline:          true,
  isFullscreen:      false,
  hasAudio:          true,
  hasPremiumAccount: false,
  canSendTelemetry:  true
};
let configNumber = 0b10101;

// Extracts hasPremiumAccount
getBits(configNumber, 1 << 1); // false
getBitsFrom(configNumber, 1); // false

// Toggles isOnline and isFullscreen
toggleBits(configNumber, (1 << 4) + (1 << 3)); // 0b01101 === 13 
```

Enter fullscreen mode Exit fullscreen mode

# 结论:我为什么还要麻烦位屏蔽呢？

这是一个非常好的问题。坦率地说，我不建议经常使用这个，如果有的话。尽管它很聪明，但对于普通用途来说太深奥了。大部分时间是不切实际，不可读的。需要不断地记录和了解，以确保选择和操作正确的钻头。总的来说，这方面的应用并不多，尤其是在 JavaScript 这样的高级语言中。然而，如果需要的话，这不应该阻止你使用它。作为程序员，我们的工作是确定哪些算法对用户(可用性)和开发人员(可维护性)都是最好的。

如果是这样的话，那我写一整篇关于这个的文章有什么意义呢？

*   这是给那些铁杆计算机科学家的。他们将从这篇文章中受益最大，尤其是那些刚刚开始深入计算机科学这个怪异世界的人。更一般地说，不需要成为计算机科学家也能从这篇文章中受益。任何对此类话题感兴趣的人都会看到所有这些位屏蔽混乱的价值。
*   对于那些对计算机科学不感兴趣的人来说，您现在有了更多的工具。如果时间需要，你可以在将来使用位掩码。我希望这篇文章能鼓励你创造性地思考。过度设计是我们最终都会遭受的诅咒。不过，这也不完全是件坏事。过度工程只是创造性思维的负面内涵。我们的大脑倾向于探索想法，即使它是不切实际的。当然，为了提高工作效率，我们不得不避免它，但是偶尔进行一点探索总是有益的。让大脑运转起来，它就会为你工作。
*   至于我，写这篇文章是为了考验自己。我想知道到目前为止我学到了多少。除此之外，我还从教别人中找到乐趣。一个人可以通过教别人学到很多东西。这是我为这个网站写文章的主要原因。这是有回报的，你知道吗？如果你还没有，那就继续尝试教别人一些新的东西。你可能会惊讶地发现它对你的帮助有多大。

负责任地屏蔽位。