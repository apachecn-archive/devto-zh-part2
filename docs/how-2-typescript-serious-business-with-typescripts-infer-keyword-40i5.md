# 如何 2 TypeScript:严重的业务与 TypeScript 的推断关键字。

> 原文：<https://dev.to/miracleblue/how-2-typescript-serious-business-with-typescripts-infer-keyword-40i5>

嘿你！你想得到一个函数的返回类型并使用它，而不知道它到底是什么，或者要求你的东西的用户直接提供它吗？

你当然知道。

```
type GetReturnType<original extends Function> = 
  original extends (...x: any[]) => infer returnType ? returnType : never 
```

Enter fullscreen mode Exit fullscreen mode

typescript 中的条件类型允许您以相当动态的方式将类型变量引入表达式。注意关键字`infer`。这是对 TypeScript 说的:“我想把 TypeScript 推断在这个位置的任何东西赋值给名称`returnType`”。恰好那个位置的东西是给定函数的返回类型，我们称之为`original`。

当你付诸行动时会发生什么？我很高兴你问了！以这个极其强大、有用、全面的神奇高阶函数
为例

```
const someRandomStuff = <fn extends Function>(originalFn: fn) => {
  const result: GetReturnType<fn> = originalFn(12345);

  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

无论`originalFn(12345)`的返回类型是什么，`result`的类型都将是什么。我们实际上不知道，直到...

```
const innerFn = (item: number) => item.toString(); 
```

Enter fullscreen mode Exit fullscreen mode

仅供参考，TS 将推断出`innerFn`的输出是一个字符串。然后...

```
const output = someRandomStuff(innerFn); // ALL SYSTEMS GO 
```

Enter fullscreen mode Exit fullscreen mode

你想玩游戏吗？`output`是什么类型？答案是:`innerFn`的类型是什么？

是一串！

要不是这一招，`output`的类型就是`any`了。我知道，我也很惊讶。但这是真的。

您可以对 TypeScript 可以推断的任何东西做同样的事情。想知道论点的类型吗？

```
type NYET = never // preparation for unfunny joke
type GetArgumentType<original extends Function> =
  original extends (...x: infer argumentsType) => any ? argumentsType : NYET 
```

Enter fullscreen mode Exit fullscreen mode

就这些了，下次见。