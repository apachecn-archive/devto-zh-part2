# 那天我差点在面包店做了一个图书馆

> 原文：<https://dev.to/jhia/the-day-i-almost-made-a-library-on-a-bakery-13o4>

首先，我不是这方面的专家。我只是在猜测，也许这样我可以避免为一个简单的小数字操作安装一个库。如果你正在寻找答案，我向你推荐这个很酷的[浮点指南](https://floating-point-gui.de/)。但我还是写了这个，希望你会像员工一样笑。

有时候编码就像在花园里漫步一样简单，有时候却很有挑战性，但是有时候就是这样:

```
Python 3.6.3 (default, Oct 24 2017, 14:48:20) 
[GCC 7.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 0.1 + 0.2
0.30000000000000004 
```

我没想到会这样。嗯，我在撒谎，我真的预料到了。因为这个“错误的”答案只是浮点运算的常见结果。

第一次遇到自己搜索[Python](https://docs.python.org/3/tutorial/floatingpoint.html)为什么会这样，应该怎么解决(`Decimal`包是你的朋友)。但是，Node.js 也需要一个答案:

```
node v9.4.0
> 0.1 + 0.2
0.30000000000000004 
```

我很确定有 JavaScript 的库，但是我不想为了一个简单的操作给我的 package.json 添加任何其他的依赖。对此应该有一个比较棘手的答案，我猜是这样的。我抓起我的笔记本电脑，说*再见*现在。

然后我去了面包店。

购买食品杂货是令人放松的，但搭配面包却是独一无二的；角落里的小后台至少有我夏季播放列表的一半，作为背景音乐播放。一些客户和雇主过去常常演唱或至少低声吟唱这些歌曲。我就是其中之一。但是那天太满了，所以我决定在外面的一张桌子等我的搭档。

我的一个朋友借给我豪尔赫·路易斯·博尔赫斯的小说，我开始随着这种放松的音乐阅读。《T2》第二章的一些内容提到了十二进制和六十进制的转换。此时，一个想法从我灵光一现的脑海中闪过。我停下来合上书。

如果不是默认处理这些数字(二进制分数)，而是将数字转换成整数，会怎么样？

计算机更擅长计算整数，然后将结果还原成小数...

*找到了！*

我没意识到我在大声思考。每个人都把头转向我，当然是惊讶。作为回应，我在“那是我的歌！”之后开始唱歌跳舞。嗯，演出必须继续。然后惊喜变成了大笑。

谢谢你夏奇拉。

回到我的座位上，为我的场景感到尴尬，我勾画出我将在下面几行中编码的内容:

我们需要知道这个数字有多少位小数:

```
 function getExponential(num) {
  if (!!(num % 1)) {
    return num.toString().split('.')[1].length;
  }
  // just for integers
  return 0;
} 
```

然后我们可以做一个函数来计算两个数的和，比如:

```
 function add(a, b) {
  // we use the greater exponent
  const exp = Math.max(getExponential(a), getExponential(b));
  const intConversor = Math.pow(10, exp);
  return (a * intConversor + b * intConversor) / intConversor;
} 
```

用减法几乎是一样的:

```
 function subtract(a, b) {
  // we use the greater exponent
  const exp = Math.max(getExponential(a), getExponential(b));
  const intConversor = Math.pow(10, exp);
  return (a * intConversor - b * intConversor) / intConversor;
} 
```

`a`乘以`b`的小数位数是两个数的小数长度之和。这两个数字都应该是整数的最小表达式，它们可以是:

```
 function multiply(a, b) {
  const expA = getExponential(a);
  const expB = getExponential(b);
  const floatConversor = Math.pow(10, expA + expB);
  return (a * Math.pow(10, expA)) * (b * Math.pow(10, expB)) / floatConversor;
} 
```

我在考虑小数的除法:

```
 function divide(a, b) {
  // we use the greater exponent
  const exp = Math.max(getExponential(a), getExponential(b));
  const intConversor = Math.pow(10, exp);
  return a * intConversor / (b * intConversor);
} 
```

让我们来测试一下:

```
node v9.4.0
> add(0.1, 0.2)
0.3
> subtract(0.1, 0.2)
-0.1
> multiply(0.1, 0.2)
0.02
> divide(0.1, 2)
0.05 
```

搞定了。最后，我可以在不使用任何外部库的情况下进行操作。任务完成。是时候拿奖励了，咖啡！但是，当然，目前为止面包店。

Jesse Milns 在南福尔诺拍摄的主要照片。