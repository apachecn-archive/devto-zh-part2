# 在 Elm 中使用递归进行循环

> 原文：<https://dev.to/vincecampanale/using-recursion-to-loop-in-elm-19pf>

本帖围绕以下问题展开:

```
Find the difference between the square of the sum and the sum of the squares of the first N natural numbers.

The square of the sum of the first ten natural numbers is (1 + 2 + ... + 10)² = 55² = 3025.

The sum of the squares of the first ten natural numbers is 1² + 2² + ... + 10² = 385.

Hence the difference between the square of the sum of the first ten natural numbers and the sum of the squares of the first ten natural numbers is 3025 - 385 = 2640. 
```

Enter fullscreen mode Exit fullscreen mode

这个问题的功劳归于[exercisem . io](//exercism.io/exercises/elm/difference-of-squares/readme)。

计划是先用 Javascript 中的一个`for`循环求解，再用 Javascript 中的递归求解，最后将递归解翻译成 Elm。

### 同`for`循环

在几乎是伪代码的情况下，`for`循环解决方案看起来像这样:

```
-- get the square of the sum of n by:
  -- going from 1 to n
  -- and adding each number to a total
-- return the total after the loop is done

-- get the sum of the squares of n by:
  -- going from 1 to n
  -- and adding the square of each number to a total
-- return the total after the loop is done

-- subtract the latter from the former 
```

Enter fullscreen mode Exit fullscreen mode

翻译成 Javascript，我们得到这个:

```
function squareOfSum(number) {
  let sum = 0;
  for (let i = 1; i <= number; i++) {
    sum += i;
  }
  return Math.pow(sum, 2);
}

function sumOfSquares(number) {
  let sum = 0;
  for (let i = 1; i <= number; i++) {
    sum += Math.pow(i, 2);
  }
  return sum;
}

function difference(number) {
  return squareOfSum(number) - sumOfSquares(number);
}

console.log(difference(10) === 2640); // true 
```

Enter fullscreen mode Exit fullscreen mode

感谢我广泛的测试套件，我可以自信地重构并使用递归。

### 为了理解递归...

上述解决方案的递归等价是这样的:

```
-- get the square of the sum of n by:
  -- getting the triangular number for n by:
    -- returning 0 if n is 0
    -- adding n to the triangular number of n - 1

-- get the sum of the squares of n by:
  -- returning 0 if n is 0
  -- adding the square of n to the sum of the squares of n - 1

-- subtract the latter from the former 
```

Enter fullscreen mode Exit fullscreen mode

因此，递归作为一种不同的循环方式，通过为每个数字`n`定义一个动作直到 1，并在`n`达到 0 时定义一个最终动作来结束循环。

我谷歌了一下“以加代乘的阶乘”，找到了[“三角数”](https://en.wikipedia.org/wiki/Triangular_number)，所以计算从 1 到`N`的正整数之和的函数就叫`triangulate`🤷🏻‍♂️.

让我们先写那个函数:

```
function triangulate(n) {
  if (n === 0) {
    return 0;
  } else {
    return n + triangulate(n - 1);
  }
}

// which can be simplified to:

function triangulate(n) {
  return n === 0 ? 0 : n + triangulate(n - 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

使用三角函数，我们可以得到`squareOfSum`函数:

```
function squareOfSum(n) {
  const sum = triangulate(n);
  return Math.pow(sum, 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

`sumOfSquares`函数也可以使用递归:

```
function sumOfSquares(n) {
  if (n === 0) {
    return 0;
  } else {
    return Math.pow(n, 2) + sumOfSquares(n - 1);
  }
}

// again, can be reduced to..

function sumOfSquares(n) {
  return n === 0 ? Math.pow(n, 2) + sumOfSquares(n - 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

关于 Javascript 解决方案的最后一个想法是让`triangulate`更通用一点，并为指数添加第二个参数。

```
const triangulate = (n, exp = 1) => 
  n === 0
  ? 0
  : Math.pow(n, exp) + triangulate(n - 1, exp); 
```

Enter fullscreen mode Exit fullscreen mode

那么`sumOfSquares`可以写成如下:

```
function sumOfSquares(n) {
  return triangulate(n, 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 来点榆树怎么样？

Elm 没有`for`循环。哇哦

是的，真的。

幸运的是，我们已经知道这个问题可以不通过`for`循环来解决。那么 Elm 等价于上面的递归解决方案是什么呢？好吧，让我们在 Javascript 中再重构`sumOfSquares`一次*一次*，这次只使用两种情况的 switch 语句。

```
function sumOfSquares(n) {
  switch (n) {
    case 0:
      return 0;
    default:
      return Math.pow(n, 2) + sumOfSquares(n - 1);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

Elm 有一个`case`语句，因此一个几乎等价的函数将起作用:

```
sumOfSquares : Int -> Int
sumOfSquares n =
  case n of
    0 -> 0
    _ -> (n ^ 2) + sumOfSquares (n - 1) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将类似的方法应用于`squareOfSum` :

```
squareOfSum : Int -> Int
squareOfSum n = 
  let
    triangulate x =
      case x of
          0 -> 0
          _ -> x + triangulate (x - 1)
  in 
    (triangulate n) ^ 2 
```

Enter fullscreen mode Exit fullscreen mode

那么最后的函数`difference`就是:

```
difference : Int -> Int
difference n =
  (squareOfSum n) - (sumOfSquares n) 
```

Enter fullscreen mode Exit fullscreen mode

瞧，我们在 Elm 中解决了一个`for`循环友好的问题，这是一种没有`for`循环的语言。

### 更好的办法？

虽然我们*可以*使用递归遍历`0`和`N`之间的数字，但我们也可以利用 [Elm 核心](http://package.elm-lang.org/packages/elm-lang/core/latest/)中公开的其他实用程序。

比如`List.range`和`List.sum`就让这个问题简单多了。

```
import List exposing (map, range, sum)

square : Int -> Int
square n =
    n ^ 2

squareOfSum : Int -> Int
squareOfSum n =
    range 1 n |> sum |> square

sumOfSquares : Int -> Int
sumOfSquares n =
    range 1 n |> map square |> sum

difference : Int -> Int
difference n =
    squareOfSum n - sumOfSquares n 
```

Enter fullscreen mode Exit fullscreen mode

由于`for`循环是我们作为程序员首先学习的东西之一，所以在解决日常问题时很容易依赖`for`循环。使用 Elm 教会了我`for`循环在大多数时候是不必要的，寻求不同的解决方案可以产生更具声明性和可读性的代码。

感谢阅读:)