# 每个程序员都应该知道的 8 个时间复杂度

> 原文：<https://dev.to/amejiarosario/8-time-complexities-that-every-programmer-should-know-494m>

我们要学习的是每个开发者都应该熟悉的 top 算法的运行时间。了解这些时间复杂性将有助于您评估您的代码是否可伸缩。此外，比较同一问题的不同解决方案也很方便。最后，您将能够观察不同的实现，并知道哪一个执行得更好。

为了澄清本文其余部分使用的一些概念:

*   *时间复杂度*与计时算法需要多长时间无关。而是执行多少个操作。
*   程序执行的指令数量受输入大小的影响(以及它们的元素是如何排列的)。
*   大 O 符号用于使用输入大小`n`对算法进行分类。例如 O(n)或 O(n <sup>2</sup> )。

在我们开始之前，这里有一张**大的备忘单**和我们将在这篇文章中涉及的例子。**点击上面的**跳转到执行。😉

| 大 O 符号 | 名字 | 示例 |
| --- | --- | --- |
| *O(1)* | 常数 | # [奇数或偶数](#odd-or-even)，
# [查表](#lookup-table) |
| *O(对数 n)* | 对数的 | # [用**二分搜索法**T3】在排序后的数组中查找元素](#binary-search) |
| *O(n)* | 线性的 | # [在未排序的数组中找到最大元素](#the-largest-item-on-an-unsorted-array)，
# [在带有哈希映射的数组中找到重复元素](#) |
| *O(n log n)* | 线性数学 | # [用**合并排序**对数组中的元素进行排序](#mergesort) |
| *O(n <sup>2</sup> )* | 二次的 | # [数组中重复元素**(天真)**](#has-duplicates) ，
# [用**冒泡排序**](#Bubble-sort) 排序数组 |
| *O(n <sup>3</sup> )* | 立方体的 | # [三变量方程求解器](#triple-nested-loops) |
| *O(2 <sup>n</sup> )* | 指数的 | # [查找所有子集](#subsets-of-a-Set) |
| *O(n！)* | 阶乘 | # [寻找给定集合/字符串的所有排列](#permutations) |

现在，我们一个一个来，提供代码示例！

* * *

## (1)O-常数时间

`O(1)`描述无论输入大小如何，计算时间都相同的算法。

例如，如果一个函数用相同的时间处理 10 个元素和 100 万个项目，那么我们说它有一个恒定的增长率或`O(1)`。来看几个案例。

## 奇数还是偶数

找出一个数是奇数还是偶数。

```
 function isEvenOrOdd(n) {
    return n % 2 ? 'Odd' : 'Even';
  }

  console.log(isEvenOrOdd(10)); // => Even
  console.log(isEvenOrOdd(10001)); // => Odd 
```

Enter fullscreen mode Exit fullscreen mode

**高级注意:**你也可以用位和运算符 *`n & 1`* 代替 *`n % 2`* 。如果第一位( <abbr title="Least Significant Bit">LSB</abbr> )为`1`，则为奇数，否则为偶数。

不管 n 是`10`还是`10,001`，它都会执行一次第 2 行。

> 不要被俏皮话所愚弄。它们并不总是转化为常数时间。你必须知道它们是如何实现的。

如果你有一个类似于`Array.sort()`的方法或者任何其他的数组或对象方法，你必须查看实现来确定它的运行时间。

像和、乘、减、除、模、位移等基本运算有一个恒定的运行时间。这简直令人震惊！

如果使用教科书上的长乘法算法，两个数相乘需要`O(n<sup>2</sup>)`。然而，大多数编程语言将数字限制为最大值(例如，在 JS 中:`Number.MAX_VALUE`是`1.7976931348623157e+308`)。因此，您不能操作产生大于`MAX_VALUE`的结果的数字。所以，原语操作必然要在固定数量的指令上完成`O(1)`或者抛出溢出错误(在 JS 中，`Infinity`关键字)。

这个例子很简单。我们再做一个。

## 查表

给定一个字符串，找出它的词频数据。

```
const dictionary = {the: 22038615, be: 12545825, and: 10741073, of: 10343885, a: 10144200, in: 6996437, to: 6332195 /* ... */};

function getWordFrequency(dictionary, word) {
  return dictionary[word];
}

console.log(getWordFrequency(dictionary, 'the'));
console.log(getWordFrequency(dictionary, 'in')); 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们可以确定，即使字典有 1000 万或 100 万个单词，它仍然会执行一次第 4 行来查找这个单词。然而，如果我们决定将字典存储为一个数组，而不是一个散列表，那么情况就不同了。在下一节中，我们将探索在数组中查找一个项目需要多少运行时间。

> 只有具有完美的*哈希函数*的哈希表才会有最坏情况下的运行时间 *O(1)* 。完美的哈希函数是不实际的，所以会有一些冲突和变通办法导致最坏情况下的运行时间 *O(n)* 。尽管如此，在*平均值*上，查找时间是 *O(1)* 。

* * *

## (n)O-线性时间

线性运行时间算法非常常见。线性运行时意味着程序从输入中访问每个元素。

线性时间复杂度 *`O(n)`* 意味着随着输入的增长，算法完成的时间也相应地变长。

一些例子:

### 未排序数组中最大的项目

假设您想从一个未排序的数组中找到最大值。

```
function findMax(n) {
  let max;
  let counter = 0;

  for (let i = 0; i < n.length; i++) {
    counter++;
    if(max === undefined || max < n[i]) {
      max = n[i];
    }
  }

  console.log(`n: ${n.length}, counter: ${counter}`);
  return max;
} 
```

Enter fullscreen mode Exit fullscreen mode

`findMax`函数会做多少次运算？

它检查来自输入`n`的每个元素。如果当前元素比`max`大，它将进行赋值。

注意，我们添加了一个计数器，这样它可以帮助我们计算内部块执行了多少次。

如果你得到时间复杂度，大概是这样的:

*   第 2-3 行:2 项操作
*   第 4 行:大小为 n 的循环
*   第 6-8 行:for 循环中的 3 个操作。

所以，这让我们`3(n) + 2`。

应用我们在[上一篇文章](https://adrianmejia.com/blog/2018/04/04/how-you-can-change-the-world-learning-data-structures-algorithms-free-online-course-tutorial/#Asymptotic-analysis)中学到的大 O 符号，我们只需要最大的阶项，因此`O(n)`。

我们可以使用我们的`counter`来验证这一点。如果`n`有 3 个元素:

```
findMax([3, 1, 2]);
// n: 3, counter: 3 
```

Enter fullscreen mode Exit fullscreen mode

或者如果`n`有 9 个元素:

```
findMax([4,5,6,1,9,2,8,3,7])
// n: 9, counter: 9 
```

Enter fullscreen mode Exit fullscreen mode

现在假设你有一个包含一百万个条目的数组，它将执行一百万次操作。如果我们画出 n 和运行时间，我们会得到一个类似线性方程的图形。

[![](img/f3898541d638f232df86ded14374d3ad.png "Linear Running time O(n) example")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8SRTbFgh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/linear-running-time-o%28n%29.jpg)

* * *

## O(n <sup>2</sup> ) -二次时间

二次时间复杂度的函数，增长率为 n <sup>2</sup> 。如果输入大小为 2，它将执行 4 次操作。如果输入大小为 8，则需要 64，以此类推。

以下是二次算法的一些代码示例:

### 有重复

你想在数组中找到重复的单词。一个简单的解决方案如下:

```
function hasDuplicates(n) {
  const duplicates = [];
  let counter = 0;

  for (let outter = 0; outter < n.length; outter++) {
    for (let inner = 0; inner < n.length; inner++) {
      counter++;

      if(outter === inner) continue;

      if(n[outter] === n[inner]) {
        return true;
      }
    }
  }

  console.log(`n: ${n.length}, counter: ${counter}`);
  return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

时间复杂度分析:

*   第 2-3 行:2 项操作
*   第 5-6 行:大小为 n 的双环，所以`n<sup>2</sup>`。
*   第 7-13 行:在 double 内部有大约 3 个操作

我们得到`3n^2 + 2`。

同样，当我们使用大 O 符号时，我们丢弃所有常数，留下最重要的项:`n^2`。所以，会是`O(n^2)`。

我们使用一个计数器变量来帮助我们验证。`hasDuplicates`函数有两个循环。如果我们输入 4 个单词，它将执行内部块 16 次。如果我们有 9，它将执行计数器 81 次，以此类推。

```
hasDuplicates([1,2,3,4]);
// n: 4, counter: 16 
```

Enter fullscreen mode Exit fullscreen mode

并且用 n 大小 9:

```
hasDuplicates([1,2,3,4,5,6,7,8,9]);
// n: 9, counter: 81 
```

Enter fullscreen mode Exit fullscreen mode

让我们看另一个例子。

### 冒泡排序

我们想对数组中的元素进行排序。

```
function sort(n) {
  for (let outer = 0; outer < n.length; outer++) {
    let outerElement = n[outer];

    for (let inner = outer + 1; inner < n.length; inner++) {
      let innerElement = n[inner];

      if(outerElement > innerElement) {
        // swap
        n[outer] = innerElement;
        n[inner] = outerElement;
        // update references
        outerElement = n[outer];
        innerElement = n[inner];
      }
    }
  }
  return n;
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，你可能会注意到，对于一个巨大的`n`，解决问题所需的时间会增加很多。你能发现嵌套循环和运行时间之间的关系吗？当一个函数只有一个循环时，它的运行时间复杂度通常是 O(n)。现在，这个函数有 2 个嵌套循环和二次运行时间:O(n <sup>2</sup> )。

* * *

## O(n <sup>c</sup> ) -多项式时间

多项式运行表示为 O(n <sup>c</sup> ，当`c > 1`。正如您已经看到的，两个内部循环几乎转化为 O(n <sup>2</sup> )，因为在大多数情况下它必须经过数组两次。三个嵌套循环是立方的吗？如果每个人都访问所有元素，那么是的！

通常，我们希望远离多项式运行时间(二次、三次、n <sup>c</sup> …)，因为随着输入的快速增长，它们需要更长的计算时间。然而，他们还不是最差的。

### 三重嵌套循环

假设您想要找到一个多变量方程的解，如下所示:

> 3x + 9y + 8z = 79

这个天真的程序会给你所有满足方程的解，其中`x`，`y`，`z` < `n`。

```
function findXYZ(n) {
  const solutions = [];

  for(let x = 0; x < n; x++) {
    for(let y = 0; y < n; y++) {
      for(let z = 0; z < n; z++) {
        if( 3*x + 9*y + 8*z === 79 ) {
          solutions.push({x, y, z});
        }
      }
    }
  }

  return solutions;
}

console.log(findXYZ(10)); // => [{x: 0, y: 7, z: 2}, ...] 
```

Enter fullscreen mode Exit fullscreen mode

这个算法有一个立方运行时间:`O(n<sup>3</sup>)`。

**注意:**我们可以做一个更有效的解决方案，但是为了展示一个立方运行时的例子，这已经足够好了。

* * *

## O(log n) -对数时间

对数时间复杂性通常适用于每次将问题分成两半的算法。例如，假设我们想要在旧的时尚词典中查找一个单词。它按字母顺序排列每个单词。至少有两种方法可以做到:

算法 A:

*   从书的开头开始，按顺序进行，直到找到要找的联系人。

算法 B:

*   打开中间的书，检查上面的第一个单词。
*   如果你要找的单词按字母顺序排列比较大，那么向右看。否则，看左半部。

哪个更快？第一种算法是一个字一个字地进行 *O(n)* ，而算法 B 在每次迭代中把问题分成两半 *O(log n)* 。这个第二种算法是二分搜索法。

## 二分搜索法

查找排序数组中元素的索引。

如果我们实现(算法 A)遍历一个数组中的所有元素，将需要运行时间`O(n)`。我们能做得更好吗？我们可以尝试利用集合已经排序的事实。稍后，当我们寻找有问题的元素时，我们可以一分为二。

```
function indexOf(array, element, offset = 0) {
  // split array in half
  const half = parseInt(array.length / 2);
  const current = array[half];

  if(current === element) {
    return offset + half;
  } else if(element > current) {
    const right = array.slice(half);
    return indexOf(right, element, offset + half);
  } else {
    const left = array.slice(0, half)
    return indexOf(left, element, offset);
  }
}

const directory = ["Adrian", "Bella", "Charlotte", "Daniel", "Emma", "Hanna", "Isabella", "Jayden", "Kaylee", "Luke", "Mia", "Nora", "Olivia", "Paisley", "Riley", "Thomas", "Wyatt", "Xander", "Zoe"];
console.log(indexOf(directory, 'Hanna'));   // => 5
console.log(indexOf(directory, 'Adrian'));  // => 0
console.log(indexOf(directory, 'Zoe'));     // => 18 
```

Enter fullscreen mode Exit fullscreen mode

计算`indexOf`的时间复杂度不像前面的例子那么简单。这个函数是递归的。

有几种方法可以分析像[主方法](https://adrianmejia.com/blog/2018/04/05/most-popular-algorithms-time-complexity-every-programmer-should-know-free-online-tutorial-course/#Master-Method-for-recursive-algorithms)这样的递归算法，这超出了本文的范围。根据经验，每当你看到一个算法将输入分成两半时，它可能涉及到一些运行时。由于递归之外所做的工作是恒定的，那么我们有一个运行时间为 *O(log n)* 。

* * *

## O(n log n) -线性算法

线性算法时间复杂度它比线性算法稍慢，但仍然比二次算法好得多(你会在文章的最后看到一个比较所有算法的图表)。

### 合并排序

对数组排序的最好方法是什么？之前，我们提出了一种使用时间复杂度为 O(n <sup>2</sup> )的冒泡排序的解决方案。我们能做得更好吗？

我们可以用一个叫`mergesort`的算法来改进。
它是这样工作的:

1.  我们将递归地划分数组，直到元素个数不超过两个。
2.  我们知道如何对 2 个项目进行排序，所以我们对它们进行迭代排序(基本情况)。
3.  最后一步是合并:我们从每个数组中一个接一个地进行合并，使它们按升序排列。

下面是合并排序的代码:

```
function sort(n) {
  const length = n.length;
  // base case
  if(length === 1) {
    return n;
  }
  if(length === 2) {
    return n[0] > n[1] ? [n[1], n[0]] : [n[0], n[1]];
  }
  // slit and merge
  const mid = length/2;
  return merge(sort(n.slice(0, mid)), sort(n.slice(mid)));
}

function merge(a = [], b = []) {
  const merged = [];
  // merge elements on a and b in asc order. Run-time O(a + b)
  for (let ai = 0, bi = 0; ai < a.length || bi < b.length;) {
    if(ai >= a.length || a[ai] > b[bi]) {
      merged.push(b[bi++]);
    } else {
      merged.push(a[ai++]);
    }
  }

  return merged;
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，它有两个功能`sort`和`merge`。Merge 是一个辅助函数，通过集合`a`和`b`运行一次，所以它的运行时间是 O(n)。Sort 是一个递归函数，每次将数组分成两半，mergesort 的总运行时间为 *O(n log n)* 。

注意:如果你想看完整的解释，请查看合并排序的主方法[。](https://adrianmejia.com/blog/2018/04/05/most-popular-algorithms-time-complexity-every-programmer-should-know-free-online-tutorial-course/#Mater-Method-for-Mergesort)

* * *

## O(2 <sup>n</sup> ) -指数时间

指数(基数为 2)运行时间意味着随着输入的增长，算法执行的计算每隔一段时间就会加倍。

### 集合的子集

寻找给定集合中所有不同的子集。例如，让我们做一些例子，尝试提出一个算法来解决它:

```
getSubsets('') // =>  ['']
getSubsets('a') // => ['', 'a']
getSubsets('ab') // => ['', 'a', 'b', 'ab'] 
```

Enter fullscreen mode Exit fullscreen mode

你注意到什么规律了吗？

*   第一个返回包含一个空元素。
*   第二种情况返回空元素+第一个元素。
*   第三种情况精确地返回第二种情况的结果+附加了第二个元素`b`的相同数组。

如果想找到`abc`的子集怎么办？嗯，它将会是“ab”的子集，也是“T1”的子集，在每个元素的末尾附加了“T2”。

正如您注意到的，每次输入变长，输出都是前一次的两倍长。让我们把它编码为 op:

```
function getSubsets(n = '') {
  const array = Array.from(n);
  const base = [''];

  const results = array.reduce((previous, element) => {
    const previousPlusElement = previous.map(el => {
      return `${el}${element}`;
    });
    return previous.concat(previousPlusElement);
  }, base);

  console.log(`getSubsets(${n}) // ${results.slice(0, 15).join(', ')}... `);
  console.log(`n: ${array.length}, counter: ${results.length};`);
  return results;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们对几个例子运行这个函数，我们将得到:

```
getSubsets('') // ...
// n = 0, f(n) = 1;
getSubsets('a') // , a...
// n = 1, f(n) = 2;
getSubsets('ab') // , a, b, ab...
// n = 2, f(n) = 4;
getSubsets('abc') // , a, b, ab, c, ac, bc, abc...
// n = 3, f(n) = 8;
getSubsets('abcd') // , a, b, ab, c, ac, bc, abc, d, ad, bd, abd, cd, acd, bcd...
// n = 4, f(n) = 16;
getSubsets('abcde') // , a, b, ab, c, ac, bc, abc, d, ad, bd, abd, cd, acd, bcd...
// n = 5, f(n) = 32; 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，如果你画出`n`和`f(n)`，你会注意到它和函数`2^n`一模一样。这个算法的运行时间为`O(2^n)`。

注意:你应该避免指数运行时间的函数(如果可能的话),因为它们伸缩性不好。每增加一个输入大小，处理输出所需的时间就会加倍。但是指数运行时间还不是最差的；有些人走得更慢。让我们在下一节再看一个例子。

* * *

## O(n！)-阶乘时间

阶乘是所有小于自身的正整数的乘积。例如:

> 5!= 5 x 4 x 3 x 2 x 1 = 120

它长得相当快:

> 20! = 2,432,902,008,176,640,000

正如你可能猜到的，如果可能的话，你希望远离有这个运行时间的算法！

## 排列组合

写一个函数，计算给定一个字符串可以组成的所有不同的单词。例如

```
getPermutations('a') // => [ 'a']
getPermutations('ab') // =>  [ 'ab', 'ba']
getPermutations('abc') // => [ 'abc', 'acb', 'bac', 'bca', 'cab', 'cba' ] 
```

Enter fullscreen mode Exit fullscreen mode

你会怎么解决这个问题？

一个简单的方法是检查字符串的长度是否为 1。如果是，返回该字符串，因为你不能对它进行不同的排列。

对于长度大于 1 的字符串，我们可以使用递归将问题分成更小的问题，直到长度为 1。我们可以去掉第一个字符，然后解决字符串剩余部分的问题，直到长度为 1。

```
function getPermutations(string, prefix = '') {
  if(string.length <= 1) {
    return [prefix + string];
  }

  return Array.from(string).reduce((result, char, index) => {
    const reminder = string.slice(0, index) + string.slice(index+1);
    result = result.concat(getPermutations(reminder, prefix + char));
    return result;
  }, []);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果打印出输出，它会是这样的:

```
getPermutations('ab') // ab, ba...
// n = 2, f(n) = 2;
getPermutations('abc') // abc, acb, bac, bca, cab, cba...
// n = 3, f(n) = 6;
getPermutations('abcd') // abcd, abdc, acbd, acdb, adbc, adcb, bacd...
// n = 4, f(n) = 24;
getPermutations('abcde') // abcde, abced, abdce, abdec, abecd, abedc, acbde...
// n = 5, f(n) = 120; 
```

Enter fullscreen mode Exit fullscreen mode

我用一根长度为 10 的绳子试了试。大概花了 8 秒钟！

```
time node ./lib/permutations.js
# getPermutations('abcdefghij') // => abcdefghij, abcdefghji, abcdefgihj, abcdefgijh, abcdefgjhi, abcdefgjih, abcdefhgij...
# // n = 10, f(n) = 3,628,800;
# ./lib/permutations.js  8.06s user 0.63s system 101% cpu 8.562 total 
```

Enter fullscreen mode Exit fullscreen mode

我有一点家庭作业给你...

> 你能试试 11 个字符的排列吗？；)下面评论你的电脑怎么了！

# 所有运行复杂性图表

我们研究了最常见的算法运行时间，每个算法都有一两个例子！他们应该给你一个在开发项目时如何计算运行时间的想法。下面您可以找到一个图表，其中显示了我们讨论过的所有时间复杂性:

[![](img/8956e95be419c952cf14b2caae9a7478.png "Big o running time complexities")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Oin5wr1H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/big-o-running-time-complexity.png)

注意你的时间复杂度！

您可以在 Github repo:
中找到所有这些例子以及更多内容

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [阿梅加罗萨里奥](https://github.com/amejiarosario)/[DSA . js-数据-结构-算法-javascript](https://github.com/amejiarosario/dsa.js-data-structures-algorithms-javascript)

### 🥞JavaScript + eBook 中解释和实现的数据结构和算法

<article class="markdown-body entry-content container-lg" itemprop="text">

[![image](img/56b5d4dbac1233ee93c5155b15079c68.png)T2】](https://user-images.githubusercontent.com/418605/59557258-10742880-8fa3-11e9-84fb-4d66a9d89faa.png)

# JavaScript 中的数据结构和算法

[![CircleCI](img/d5922fc0cbf322196a6f5e893fcfcf37.png)](https://app.circleci.com/pipelines/github/amejiarosario/dsa.js-data-structures-algorithms-javascript)[![NPM version](img/2c26fb04f41469582f423d88af92342e.png)](https://badge.fury.io/js/dsa.js)[![chat](img/e028ff0be0803e8e9dbdd25d5a8c6ca4.png)](https://dsajs-slackin.herokuapp.com)

> 这是 DSA.js book 的编码实现和 NPM 包的回购。

> 在这个库中，您可以找到 JavaScript 中算法和数据结构的实现。这些材料可以作为开发人员的参考手册，或者您可以在面试前刷新特定主题。还有，你可以找到更高效解决问题的思路。

[![Interactive Data Structures](img/8f63557fae7adb265acdf574fa489c65.png)T2】](https://user-images.githubusercontent.com/418605/46118890-ba721180-c1d6-11e8-82bc-6a671428b422.png)

## 目录

*   [安装](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#installation)
*   [特性](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#features)
*   [里面有什么](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#whats-inside)
    *   [<g-emoji class="g-emoji" alias="chart_with_upwards_trend" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4c8.png">📈</g-emoji>算法分析](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-algorithms-analysis)
    *   [<g-emoji class="g-emoji" alias="pancakes" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f95e.png">🥞</g-emoji>线性数据结构](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-linear-data-structures)
    *   [<g-emoji class="g-emoji" alias="evergreen_tree" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f332.png">🌲</g-emoji>非线性数据结构](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-non-linear-data-structures)
    *   [<g-emoji class="g-emoji" alias="hammer_and_pick" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2692.png">⚒</g-emoji> 算法技巧](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#%E2%9A%92-algorithms-techniques)
*   [预定](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#book)
*   [常见问题解答](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#faq)
*   [支持](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#support)
*   [执照](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#license)

## 装置

您可以克隆回购协议或安装来自 NPM 的代码:

```
npm install dsa.js
```

Enter fullscreen mode Exit fullscreen mode

然后您可以将它导入到您的程序或 CLI 中

```
const { LinkedList, Queue, Stack } = require('dsa.js');
```

Enter fullscreen mode Exit fullscreen mode

所有公开的数据结构和算法的完整列表[见](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/src/index.js)。

## 特征

算法是一种…

</article>

[View on GitHub](https://github.com/amejiarosario/dsa.js-data-structures-algorithms-javascript)