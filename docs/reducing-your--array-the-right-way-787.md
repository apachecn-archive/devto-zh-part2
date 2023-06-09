# 以正确的方式缩减您的阵列

> 原文：<https://dev.to/samolabams/reducing-your--array-the-right-way-787>

### 以正确的方式缩减您的阵列

[![](img/308f93f091b3cec876bf4df5622f4915.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QGdwoYGM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvXkJ89ppSS1eddnK2stWOw.jpeg) 

<figcaption>图片来源:Pixabay</figcaption>

Javascript 作为一种语言的好处之一是它允许我们编写函数式编程，也就是说，我们通过编写描述动作(应该发生什么)和抽象过程(应该如何发生)的函数来编写程序。这种编程风格在 javascript 中是可行的，因为函数是一等公民，它们是数据，可以像变量一样在应用程序中传递。

#### 纯函数

函数式编程的核心原则之一是编写纯函数。纯函数是没有自由变量的函数，也就是说，它的所有变量都来自于它的自变量，从而使它为相同的自变量返回相同的值，它对程序没有副作用，因为它不改变程序状态。

如果一个纯函数需要改变或修改传递给它的任何变量(参数)，它将创建一个副本，修改并返回该副本，而不改变原始变量。这就是所谓的不变性。函数式编程的本质是使用函数将数据从一种形式转换为另一种形式，就像我前面提到的，这些数据在转换之前被复制，从而保持原始数据完整。这提高了程序的可读性、可测试性和正确性。

#### Array.prototype.reduce 和 Array.prototype.reduceRight

数组是 javascript 中的一个基本数据结构。通过对数组应用函数，我们可以对它执行多种操作。在本文中，我们将研究其中的两个函数。分别叫做**reduce**(array . prototype . reduce)和**reduce right**(array . prototype . reduce right)。*这些函数所做的是通过迭代数组的元素来处理数组，并构建一个基于特定标准返回的单个值。*

*reduce()* 和 *reduceRight()* 的工作方式相同，只是 *reduce()* 从数组的第一项开始到最后一项，而 *reduceRight()* 从最后一项开始到第一项。

*reduce()* 和 *reduceRight()* 都接受两个参数，*一个执行归约操作的函数(一个归约器)和一个可选的初始值，归约器用这个初始值开始它的操作。*reducer 接受四个参数:前一项(也称为累积值)、当前项、当前项的索引和数组本身。前两个参数是常用的减速器。每次迭代返回的值(累积值)作为第一个参数传递给下一次迭代。

> reduce()和 reduceRight()使用一个 reducer，reducer 是在每次迭代中进行实际计算的函数。

#### 例子

让我们用一个基本的例子来说明 reduce 是如何工作的，我们将使用 reduce 函数来总结数组的元素。

```
let numbers = [2,4,6,7,8];
let sum = numbers.reduce((acc, cur) => {
 return acc + cur;
});
console.log(sum); // 27 
```

numbers 数组中有五个元素，reducer 将使用第一个值(2)作为初始累加器(acc)遍历这些元素。让我们看看每次迭代的每个值是什么样的。

```
First iteration => acc = 2, cur = 4, acc + cur = 6
Second iteration => acc = 6, cur = 6, acc + cur = 12
Third iteration => acc = 12, cur = 7, acc + cur = 19
Fourth iteration => acc = 19, cur = 8, acc + cur = 27 
```

如果像这样将第二个参数传递给 reduce 函数

```
let numbers = [2,4,6,7,8];
let sum = numbers.reduce((acc, cur) => {
 return acc + cur;
}, **20** );

console.log(sum); // 47 
```

它将是归约器的第一个参数，迭代将发生五次，而不是像这样的四次:

```
First iteration => acc = 20, cur = 2, acc + cur = 22
Second iteration => acc = 22, cur = 4, acc + cur = 26
Third iteration => acc = 26, cur = 6, acc + cur = 32
Fourth iteration => acc = 32, cur = 7, acc + cur = 39
Fifth iteration => acc = 39, cur = 8, acc + cur = 47 
```

> 最佳实践是通过提供一个值作为 reduce 函数的第二个参数，将 reduce 函数初始化为一个合理的默认值。

使用 reduce 函数查找数组中的最大数字:

```
const numbers = [20,22,50,60,12];
const largestNumber = numbers.reduce((max, num) => {
 console.log(`${num} > ${max} is ${num > max}`); 
 return (num > max) ? num : max
}, 0);

console.log(largestNumber); // 60 
```

在上面的例子中，max 变量被初始化为 0，在每次迭代中，reducer 将当前 max 值与当前元素(num)进行比较，并返回两个值中的较大值，返回值被赋给 max 变量，并用于下一次迭代。

使用 reduce 函数删除数组中的重复元素:

```
let numbers = [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4];

let distinctNumbers = numbers.reduce((distinct, num) => {
 return (distinct.indexOf(num)) !== -1 ? distinct : [...distinct, num];
}, []);

console.log(distinctNumbers); // [1,2,3,5,4] 
```

*注意，reduce 函数被初始化为一个空数组，因为我们期望结果是一个数组，这是合理的默认值。本例中 reducer 所做的是检查一个数字是否已经存在于初始化的数组中，如果不存在就添加它，然后返回数组。这里使用了扩展运算符。*

让我们再举一个例子来说明 *reduceRight()* 。假设我们需要执行一个从右到左优先的操作，比如算术运算。让我们看下面的例子:

```
Compute 3^(3^2)
// We calculate (3 ^ 5) first, assuming the result is n, we then calculate (2 ^ n) to get the final answer.

let numbers = [3,3,2];
let answer = numbers.reduceRight((ans, num) => {
 return Math.pow(num, ans);
});

console.log(answer); // 19683 
```

在下面的示例中，缩减器从右侧(2)开始，在第一次迭代中，计算将是(3)即 9，第二次迭代将是(3⁹)即 19683。

#### 结论

我刚刚向您展示了 *reduce()* 和 *reduceRight()* 的一些功能，它们的用例比我们在这里讨论的要多。要了解更多信息，[请查看 MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)。Sarah drasner 也写了一篇关于 css-tricks 的文章。

感谢阅读