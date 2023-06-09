# JS 中的递归介绍

> 原文：<https://dev.to/ryanfarney3/intro-to-recursion-in-js-32g>

如果你刚开始编程，可能听说过这个话题；*递归*。就个人而言，递归是我花了很长时间才理解的编程概念之一。诚然，我仍然有一段路要走，但在我看来，有几个主要原因，为什么这个话题可以如此短暂。

1)不需要递归就可以解决任何问题，所以经常被初学者忽略。它的优势不是很明显。
3)可能会让人一头雾水。

我的一个好朋友曾经写道:“就像一本字典用一个词来描述自己，理解起来会令人沮丧。递归是不直观的。当第一次介绍递归时，程序员通常会想起电影《盗梦空间》

我可能会为此感到羞愧，也许这是我应得的，但我还没看过《盗梦空间》。这只是我从未抽出时间去做的事情之一…也许这就是为什么我花了这么长时间才弄清楚整个递归的事情> <。

我想说递归的主要优点是，对于一些较长的问题，它使算法更具可读性和优雅。然而，在大多数情况下，递归会更慢，并且会占用更多的调用堆栈。

这里有一篇很棒的文章，解释了递归和迭代解决方案之间的一些区别！

请耐心听我解释几个关键术语和一些基本问题，以帮助您掌握令人畏惧的递归主题。

* * *

也许我应该早点定义，但是**递归是一个函数，它调用自己，直到满足指定的条件。**

如果我们想写一个从一个数开始倒数的函数，我们可以这样做。

```
function sayDownFrom(n){
    console.log(n)
    if(n > 1){
        sayDownFrom(n -1) // recursive call
    } else {
        return true // base case
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在函数体中，我们看到函数实际上调用了自己。这被称为*递归调用*。我们还可以看到函数有一个停止点，可以称为*基例*。如果没有基本案例，我们将会陷入无限循环。

那么，这个函数到底在做什么呢？

逐行…

```
function sayDownFrom(n){
    // we print the number first
    console.log(n)
    // then we check if n is greater than 1, which is essentially setting a counter to stop if it is less
    if(n > 1){
        // if n is greater than 1, we call our function which will print out the number before n (in essence, counting down)
        sayDownFrom(n -1) // recursive call
    } else {
        // if n is not greater than one it will go to our base case here and return true and complete the execution
        return true // base case
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们一行一行地检查几个问题，以获得更多的经验，看看我们是否能在递归解决方案中挑出任何重复出现的主题。

让我们递归地编写经典的 isPalindrome 解决方案。这里我们想知道传递给我们函数的字符串是否是一个回文...比如《赛车》或者《汉娜》。

```
function isPalindrome(str) {
    // setting a variable to the length of our string
    var strLen = str.length;

    //checking if the length is zero or if the length is 1
    if (strLen === 0 || strLen === 1) {
      //if we reach either of these cases we will want to return true because of our next 'if' statement
        return true;
    }

    if (str[0] === str[strLen - 1]) {
      // here we are checking if the first index in the string and the last index in the string are the same

      // if they are the same then we are going to make our recursive call, but this time pass in the string without the letters that we just checked for
      // hence the use of slice
        return isPalindrome( str.slice(1, strLen - 1) );
    }

    // if this last 'if' statement were to fail and neither of the first or last letters were equal to each other at any time in our functions life
    // then we would return false immediately since it would not pass the 'if' statement above
    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经看了两个解决方案，一个是整数，一个是字符串。我们用数组扔一个进去吧！

让我们写出一个函数来看看一个数组是否包含一个给定的元素。

```
function includesNumber(arr, element) {
  //if there are no more elements in the array, then we have checked them all and it is not there
  if (!arr.length) {
    // so we will return false
    return false

    // we are now checking if the first element is equal to the passed in element
  } else if (arr[0] === element) {
    // if it is we return true
    return true

    // if none of those things are true yet, we check the array without the first element that we had just checked
  } else {
    return includesNumber(arr.slice(1), element)
  } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 结论

我们可以在这些简单的问题中看到一些模式，特别是我们的 includesNumber 和 isPalindrome 函数。在这两种方法中，我们都检查等效项并使用。切片法。就像编程中的任何事情一样，你练习得越多，你就会发现模式。如果你正在练习算法，我总是建议首先找到解决方案(不管它有多冗长和丑陋)，然后从那里重构(包括递归地思考或尝试问题)。

希望通过对几个问题的研究，您已经对需要寻找的一些东西以及如何开始考虑递归解决方案有了大致的了解。干杯！