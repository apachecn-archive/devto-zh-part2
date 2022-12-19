# 循环的初始化变量存储在内存的什么地方？

> 原文：<https://dev.to/zchrb/where-is-a-loops-initialization-variable-stored-in-memory-2m0o>

为了便于理解，我正在处理这段代码:

```
function copyArrayAndManipulate(array, instructions) {
  let output = [];
  for (let i = 0; i < array.length; i++) {
    output.push(instructions(array[i]));
  }
  return output;
}

function multiplyBy2(input) {
  return input * 2;
}

let result = copyArrayAndManipulate([1, 2, 3], multiplyBy2); 
```

Enter fullscreen mode Exit fullscreen mode

在我的 JS 研究中，我学到了当你第一次运行一个 JS 文件时，你打开了一个全局执行上下文。这涉及到全局内存和全局执行线程。当您调用/执行一个函数时，您打开一个新的执行上下文，它被添加到调用堆栈的顶部，并且您现在有一个本地内存和本地执行线程。所以在这个实例中，当我到达我的`for`循环，当我声明`i`为`0`时，是把它存储在`copyArrayAndManipulate`函数执行上下文的本地内存中吗？还是一个`for`循环有它自己的记忆？

希望这有意义。