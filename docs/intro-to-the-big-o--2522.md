# 大 O 符号介绍👀

> 原文：<https://dev.to/sait/intro-to-the-big-o--2522>

## 什么是大 O？

在计算机科学中，大 O 用来分析一个算法的运行时间或所占用的空间，它是由 Paul Bachmann，Edmund Landau 发明的。

让我们借助例子来讨论一些常见的时间复杂性。

### 常数时间 O(1)

如果一个算法有一个恒定的时间，这意味着它总是花费相同的时间来产生输出。

示例

```
function removeLastitem(arr){
  return arr.pop()
}

console.log(removeLastitem([1,2,3,4,5,6])) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中 **removeLastitem** 函数总是花费相同的时间从数组中移除最后一个项目，不管数组有 10 个项目还是 20 个项目。

### 线性时间 O(n)

如果一个算法有一个线性时间，这意味着算法的运行时间随着输入大小的增长而增长。

示例

```
function sum(arr) {
    let total = 0;
    for (let i = 0; i < arr.length; i = i + 1) {
        total += arr[i];
    }
    return total;
}

console.log(sum([1, 2, 3, 4])) //10 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中， **sum** 函数根据数组的大小增加其运行时间。

### 二次时间 O(n<sup>2<sup>T5</sup></sup>)

算法的运行时间与输入大小的平方成正比。

示例:

```
function addAndLog(arr) {
    for (var i = 0; i < arr.length; i++) {
        for (var j = 0; j < arr.length; j++) {
            console.log(arr[i] + arr[j])
        }//O(n)
        console.log("----")
    }// O(n)
} 
```

Enter fullscreen mode Exit fullscreen mode

在下一个教程中，我们将学习关于[对数](https://dev.to/saigowthamr/intro-to-logarithms-big-o--3iia)希望你学到了一些东西。

快乐编码...

[在 twitter 上关注我](https://twitter.com/saigowthamr)

最初发表于[reactgo.com](https://reactgo.com/bigo-introduction/)