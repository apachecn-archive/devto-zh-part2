# 对 Big-O 的直观解释

> 原文：<https://dev.to/jtamsut/an-intuitive-explanation-of-big-o-52md>

# 对大-O 的直观解释

在接下来的是一个直观的(希望！)和对 big-O 的简明讨论。我试图首先解释 big-O 是什么:*一种在给定“最坏”可能输入的情况下估计算法运行完成所需时间的方法*。然后我会给出几个例子来说明如何计算一个算法的 big-O。所有代码样本都是 JavaScript。

## 简介

一个**算法**是完成某项任务的指令列表。算法的一个例子是配方。数据结构是一种组织数据的方式。家谱是一种数据结构。它以一种直观和层次分明的方式组织家庭成员之间的关系。

### 大-O

**Big-O** 是算法运行完成所需时间的度量。让我们考虑一个例子。

下面的算法，`isOneInArray`接受一个整数数组作为输入。如果数组中的 1 *为*，则返回`true`，如果数组中的 1 *不为*，则返回`false`。请记住，整数是一个没有分数部分的数字，比如 4 或 1000。

```
function isOneInArray(array) {
  for (let index=0; index<array.length; index++) {
    let currentItem = array[index];
    if (currentItem === 1) {
      return true;
    }
  }
  return false;
} 
```

在我们继续之前，让我们构建一个简单的 CPU 或中央处理器的心理模型。CPU 在我们的计算机上进行“逻辑”计算。我们的 CPU 模型将赋予 CPU 以下属性:

1.  CPU 仅进行导致布尔运算(例如，1！1 !)的*比较*。== 2 结果为真)；比较包括`!==`、`===`、`>`、`<`等。我们的 CPU 最多比较*两个*值。
2.  一个 CPU 一次只能做*个这样的比较。*

**注意**:这是对 CPU 实际工作方式的过度简化，但是对于这次讨论的目的来说已经足够接近*。*

 *让我们考虑一个`isOneInArray`函数的样本输入:

```
isOneInArray([5,4,3,2,1]) 
```

我们的 CPU 在这里做什么？

如果我们看一下`isOneInArray`函数的实现，我们可以看到 CPU 被“要求”比较上面数组中的每个成员。这总共是 5 次比较，因为数组中有 5 个整数。在执行`isOneInArray`功能期间，CPU 正在做什么可以写成如下:

```
Is 5 equal to 1? No. Next item in array.
Is 4 equal to 1? No. Next item in array.
Is 3 equal to 1? No. Next item in array.
Is 2 equal to 1? No. Next item in array.
Is 1 equal to 1? Yes. Return true! 
```

这是 5 次比较。

需要注意的是，比较的次数等于数组中元素的数量。如果我们想要概括，我们可以说我们的`isOneInArray`函数所做的比较次数等于 **N** ，其中 **N** 是某个正整数，等于输入数组的长度。

因此我们说`isOneInArray`是 *O(n)* 。

如果我们将 CPU 执行的比较次数与输入数组的长度绘制成图表，结果如下所示:

[![linear](img/cf972435d2c7cb67ab071f8f7bf85d34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B0w5jtR8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.google.com/drawings/d/1ERlK5scz4_dbylONq1caIGbSFfZ5AEQMP46alcLozuI/pub%3Fw%3D850%26h%3D541)

这是一条斜率为 1 的线性线。由于这个原因，我们说我们的`isOneInArray`算法是 O(n ),其中 n 是输入数组的长度。唯一影响 CPU 比较次数的是输入数组的长度。

**需要注意的一件重要事情是，每当我们考虑 big-O 时，我们都会考虑算法的最坏可能输入**。我们可以假装我们有一个邪恶的天才对手，为我们提供输入，要求我们在算法中进行最可能的比较。对于`isOneInArray`来说，最坏情况的输入是在数组的最末端输入 1。

我们也可以写出 O(n <sup>2</sup> )、O(nlogn)和 O(常数)的函数。记住 n <sup>2</sup> ，nlogn 和常量都是我们可以乘以输入数组的长度来得到一个算法需要做的比较次数的因子。

让我们考虑一个带有嵌套 for 循环的算法，如下所示:

```
function isTwoInArray(nestedArray) {
  for (let index=0; index<nestedArray; index++) {
    for (let k=0; index<nestedArray[index]; k++) {
      if (nestedArray[index][k] === 2) {
        return true;
      }
    }
  }
  return false;
} 
```

给定以下输入，上述函数将进行多少次比较:

```
isTwoInArray([[6,5,4], [3,1,2]]) 
```

一般来说，在确定 big-O 时，我们可以使用以下表达式:

O(n <sup>)嵌套循环数</sup>

## 结论

这是对 big-O 的非正式的、数学上不严谨的介绍。关于算法和渐近复杂性，有许多数学上更严谨、更深入的资源。C.L.R.S 的 [*算法介绍*中我最喜欢的一个。](https://mitpress.mit.edu/books/introduction-algorithms-third-edition)

感谢阅读！*