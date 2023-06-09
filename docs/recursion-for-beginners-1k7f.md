# 初学者递归

> 原文：<https://dev.to/alexdovzhanyn/recursion-for-beginners-1k7f>

作为一个初学程序员，困扰我的一个话题是递归的概念。它似乎从来没有在编程之外使用过，它的优势也不太明显，这使得它不太容易理解。这是一次尝试，向那些不确定递归是什么、如何工作或者为什么有用的人有效地解释递归。

什么是递归？
根据韦伯斯特词典，递归是:

> 一种计算机编程技术，涉及到过程、子程序、函数或算法的使用，这些程序、子程序、函数或算法一次或多次地调用自己，直到满足一个特定的条件，此时，每次重复的其余部分从最后一次调用到第一次调用都被处理

但这到底意味着什么呢？我们已经知道这是一种计算机编程技术，但是在什么情况下你会希望一个函数调用它自己呢？

基本上，递归的概念是让一个函数调用它自己，直到它做了它需要做的任何事情。这有点像一个循环(一些语言实际上使用递归作为他们的循环)，但关键的区别是循环(迭代)通过显式指定重复结构来工作，而递归通过连续的方法调用来实现重复。考虑以下任务:

创建一个方法，将任意两个数字作为参数，对它们运行 100 次斐波那契数列算法，并显示每一步的输出。

迭代地，你可以这样做:

```
const fib = (a, b) => {   // Create the method
  counter = 0              // Instantiate a counter
  while(counter < 100) {     // Loop until the counter reaches 99 
    sum = a + b
    console.log(sum)
    a = b                  // Reassign the variables for the next iteration
    b = sum
    counter++
  }
}

fib(4,5);                   // Call the function 
```

Enter fullscreen mode Exit fullscreen mode

这给了你想要的结果，并且运行良好。不过，递归地看，它会类似于:

```
fib = (a, b, counter = 0) => { // Create the method
  if (counter > 100) return // Check exit condition
  counter++
  console.log(a + b)
  return fib(b, a + b, counter) // Call itself again if exit condition isnt met
}

fib(4,5) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，它们给出了相同的结果，但是递归稍微短一些，更容易阅读。递归允许管理指数增长。虽然递归在许多情况下很有用，但也有迭代解决问题更好的情况。递归通常占用更多的内存，因此它可能不是解决需要最少内存使用的问题的最佳选择。