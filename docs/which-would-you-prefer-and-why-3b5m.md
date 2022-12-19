# 你更喜欢哪个，为什么？

> 原文：<https://dev.to/lozadaomr/which-would-you-prefer-and-why-3b5m>

我最近写了一个小函数。并想出了不同的实现方式。

基本上，该函数接受一个参数并进行字符串操作。哪个更受欢迎？

```
function (arg) {
  let optionResult = ''

  if (arg === 'option1') {
    optionResult = // do stuff for option1
  } else if (arg === 'option2') {
    optionResult = // do stuff for option2
  } else if (arg === 'option3') {
    optionResult = // do stuff for option3
  }

  return optionResult
} 
```

Enter fullscreen mode Exit fullscreen mode

或者改为

```
function (arg) {
  let optionResult = {
    'option1': // do stuff for option1,
    'option2': // do stuff for option2,
    'option3': // do stuff for option3
  }

  return optionResult[arg]
} 
```

Enter fullscreen mode Exit fullscreen mode