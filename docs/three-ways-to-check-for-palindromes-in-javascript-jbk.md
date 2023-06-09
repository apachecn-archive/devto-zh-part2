# JavaScript 中检查回文的三种方法

> 原文：<https://dev.to/domazet93/three-ways-to-check-for-palindromes-in-javascript-jbk>

回文是一个单词或短语，无论你向后读还是向前读都是一样的，例如单词**‘level’**。

我将创建三个不同的函数来完成相同的工作——对传递的字符串进行回文检查。我将使用 [performance.now()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) Web API 方法来测量函数执行的时间，看看这个函数中哪一个的性能最好。

#### 创建数组

```
isPalindrome = (string) => {
  string = string.toLocaleLowerCase();
  return Array.from(string).toString() === Array.from(string).reverse().toString()
}

//0.52 ms 
```

Enter fullscreen mode Exit fullscreen mode

Array 对象有很多有用的方法——其中之一是 [reverse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse) ，但是首先，我们需要从一个字符串创建一个数组。反转后，第一个数组元素成为最后一个，最后一个数组元素成为第一个。我们现在不能做检查，因为条件总是假的，因为它们是内存中不同位置的对象。toString()方法将从数组中返回一个字符串。

#### 递归

```
isPalindrome = (string) => {
  let strLen = string.length;
  string = string.toLocaleLowerCase();

  if (strLen === 0 || strLen === 1) {
    return true;
  }
  if (string[0] === string[strLen - 1]) {
    return isPalindrome(string.slice(1, strLen - 1) );
  }  
  return false;
};

//0.30 ms 
```

Enter fullscreen mode Exit fullscreen mode

递归是一个函数调用自身来解决问题的过程。在我们的代码中，函数被调用，直到字符串完成(空字符串或剩下一个字符)，或者如果条件下降。首先检查字符串中的第一个和最后一个字符，然后如果它们相同，对一个子字符串做同样的事情，去掉第一个和最后一个字符，依此类推...

#### 为循环

```
isPalindrome = (string) => {
  let strLen = Math.floor(string.length / 2);
  string = string.toLocaleLowerCase();

  for (let i = 0; i < strLen; i++) {
    if (string[i] !== string[strLen - i - 1]) {
      return false;
    }
  }
  return true;
}

//0.20 ms 
```

Enter fullscreen mode Exit fullscreen mode

for 循环从检查字符串的第一个字符和最后一个字符开始。如果相等，继续遍历字符串，直到字符串到达中心。

我喜欢第一种方法，因为它简洁而简单，但是从性能角度来看，它给我们的结果是最差的，简单 for 循环的速度是两倍多。