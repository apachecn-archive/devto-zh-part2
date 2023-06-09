# Base64 很好玩！

> 原文：<https://dev.to/legolord208/base64-is-fun--420d>

你好！今天我决定不再觉得 Base64 是一种黑魔法，而是去真正地学习它。你可以在这里看到结果。学到了很多有趣的东西，就在这里分享给大家。
因为混合的背景，我会把所有的东西分成几个部分，这样你就可以跳过任何你已经知道的东西。

# 按位运算符

为了正确地学习 Base64，您需要学习按位运算符。我肯定你以前在什么地方见过类似的东西。这些奇怪的半布尔运算符在整数上做什么？！如你所知，计算机使用二进制。但是关系比你想象的更亲密。按位运算符做的是翻转某些位。
*每个二进制数字(0 或 1)称为一位。*

按位 And: `&`。这将计算两位相同的两个整数的整数结果。换句话说，它看到第一位**和第二位**有哪些位。所以叫做按位 and。例子:

```
 42 // <- Binary: 101010
& 15 // <- Binary: 001111
= 10 // <- Binary: 001010

  5 // <- Binary: 101
& 6 // <- Binary: 110
= 4 // <- Binary: 100 
```

Enter fullscreen mode Exit fullscreen mode

按位移位:`<<`和`>>`。基本上是将所有数字向左或向右“移位”。这些可以比作乘以十进制的 10^n。事实上，这和乘以 2^n.是完全一样的，当你把一个东西乘以 2 时，编译器有时会优化它，把它移位 1。

```
 16 // <- Binary:  10000
<< 1
=  32 // <- Binary: 100000

   42 // <- Binary: 101010
>> 3
=  5  // <- Binary:    101 
```

Enter fullscreen mode Exit fullscreen mode

# 字节顺序

大家可能知道，`int`是 32 位整数，`byte`是 8 位整数。由于 32(以及所有其他类型的整数)是 8 的倍数，从技术上讲，你可以将一个更大类型的整数转换成多个字节，然后再转换回来...对吗？
比如说 666，`0000001010011010`，可以存储为`00000010`和`10011010`？

结果，当然，我不是第一个想到这一点的人。事实上，在记忆中它们也是这样表示的。在 C 中，你可以简单地将一个`int*`转换成一个`byte*`，然后访问整数中的特定字节。
你*可能*发现的(看情况)，是它们和我刚才展示的顺序相反(`10011010`在前，不是最后)。这叫做*小端*，这里我展示的是*大端*。 *Big endian* 的意思是“大端”(影响最大的字节，就像改变 100 中的一个比改变 10 中的一个影响更大)是第一。

# 把所有的东西放在一起

正常的二进制数据以 8 位存储，这意味着它可以达到 256。
64 恰好是一个数量可观的字符**也就是 2 的幂**。
它是 2 的幂的原因非常明显:只需读取 6 位而不是 8 位，就可以将其转换为 2 的幂。

假设我们有以下内容:“你好”。
以字节表示，这是`104 101 108 108 111`。
二进制:`01101000 01100101 01101100 01101100 01101111`。

现在我们把它变成一个整数。但在此之前，有一个问题。这很快变得难以置信的大。我们用一个方便的事实来解决这个问题:4 个 6 位整数和 3 个 8 位整数具有相同的位长。这也是为什么它被填充为 4: 6*4 = 8*3。这对于解码时的程序员来说非常方便，因此他/她不必对有效的 base64 进行边界检查。

正因为如此，我们每次只需要担心 3 个字节(解码时 4 个)。

```
for bytes in input.chunks(3) {
    // Fun fact, you *could* do this, but it's more fun to use bit shifting.
    // let buf = mem::transmute::<&[u8], u32>(bytes).to_be();
    let mut buf = 0;
    buf += (bytes.get(0).cloned().unwrap_or(0) as u32) << 8*2;
    buf += (bytes.get(1).cloned().unwrap_or(0) as u32) << 8*1;
    buf +=  bytes.get(2).cloned().unwrap_or(0) as u32; 
```

Enter fullscreen mode Exit fullscreen mode

*`.get(n).cloned().unwrap_or(0)`的原因基本上是因为索引会崩溃。输入不必能被 3 整除。*
现在我们有

```
011010000110010101101100 
```

Enter fullscreen mode Exit fullscreen mode

和

```
011011000110111100000000 
```

Enter fullscreen mode Exit fullscreen mode

。
*注意:最后一个数字用 0 填充。*

第二步现在听起来非常简单:每 6 个字节重新分割数字，如果它在 4 的倍数之前用完，就插入`=`。

```
 for i in 0..4 {
        let j = (3-i) * 6;
        let buf = ((buf >> j) & 0x3F) as u8; 
```

Enter fullscreen mode Exit fullscreen mode

第一部分实际上相当简单，但不必要地冗长。它从 24 开始倒计时，一次 6 步。大概可以写成`for j in (0..=24).rev().step_by(6)`这样，但是我后面需要`i`。

但是接下来有趣的部分来了:
让我们想象`j`是 18。

```
 011010000110010101101100
>> 24
=  011010000110

   011010000110
&  000000111111 // <- 0x3F in binary.
=  000110 
```

Enter fullscreen mode Exit fullscreen mode

看到图案了吗？在某种程度上，我们基本上只是取了一个子串...的整数！按位移位允许我们向下取值，按位 and 允许我们指定我们关心的值。

现在我们有

```
011010 000110 010101 101100 
```

Enter fullscreen mode Exit fullscreen mode

和

```
011011 000110 111100 000000 
```

Enter fullscreen mode Exit fullscreen mode

。

最后，让我们插入值，如果超出界限，则插入`=`。

```
 if bytes.len() >= i {
            output.push(char(buf));
        } else {
            output.push('=');
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意:我没有包括`char`函数的定义。它基本上只是将一个值映射到一个 base64 字符，就像`0`映射到`A`。*

我们现在有

```
a G V s 
```

Enter fullscreen mode Exit fullscreen mode

和

```
b G 8 = 
```

Enter fullscreen mode Exit fullscreen mode

。

`aGVsbG8=`