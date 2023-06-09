# 语用类型:穷尽式检查

> 原文：<https://dev.to/stereobooster/exhaustive-check-1079>

它在事件处理中很有用，或者在任何你必须处理模式匹配(像 JS 中的`switсh/case`)和分离联合(又名联合或枚举)的地方很有用。

示例:假设我们有事件(或 Redux 中的动作)并且我们有一个函数，该函数假定处理事件(或 Redux 中的 reducer)

```
const addTodo = {
  type: 'ADD_TODO',
  payload: 'buy juice'
}
const removeTodo = {
  type: 'REMOVE_TODO',
  payload: 'buy juice'
}
function process(event) {
  switch(event.type) {
    case 'ADD_TODO': // do something
      break;
    case 'REMOVE_TODO': // do something
      break;    
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下一个任务是再添加一种类型的事件:

```
const addTodo = {
  type: 'CHANGE_TODO',
  payload: {
    old: 'buy juice',
    new: 'buy water',
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要记住代码中所有需要改变行为的地方。如果是两个连续的任务，并且只有一个地方需要改变，这就很容易了。但是如果两个月后我们需要这样做，而你一开始没有写代码呢？这听起来更难吧。如果这个系统在生产和关键部分发生变化，你就会有 FUD(恐惧-不确定-怀疑)。

这就是全面检查的优势所在。

## 流量

```
function exhaustiveCheck(value: empty) {
  throw new Error(`Unhandled value: ${value}`)
}

type AddTodo = {
  type: 'ADD_TODO',
  payload: string
}
type RemoveTodo = {
  type: 'REMOVE_TODO',
  payload: string
}
type Events = AddTodo | RemoveTodo;

function process(event: Events) {
  switch(event.type) {
    case 'ADD_TODO': // do something
      break;
    case 'REMOVE_TODO': // do something
      break;
    default:
      exhaustiveCheck(event.type);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦你添加了一个新的事件(一个联合类型的新值),类型系统就会注意到它并抱怨。

```
type ChangeTodo = {
  type: 'CHANGE_TODO',
  payload: string
}
type Events = AddTodo | RemoveTodo | ChangeTodo; 
```

Enter fullscreen mode Exit fullscreen mode

会导致([试试自己](https://flow.org/try/#0GYVwdgxgLglg9mABAUwB4AsCGIDOsBuyAwushANYAU+mANiMgFwoC2ADlAJ4CUiA3gChEiKOgBOcAO6IwyaQFExEsZQAGAVTBYwAE1rIdiGvSaIAJH2MMAvqu4DrAgVzbJEAQR06AKnB1xEAF5+IRFOV2YAcncAERiAfW8AeRikyIAaULZMTlo4TB1mPDEYMABzB2dwtwAlZBY4Ql9-IJDhF1NImvkAWSSANXlElLTM4Wzc-MLEYtKKxw7EEkxy5GaA4MF26qiiAAl3ADkAcSHk1IysnLyCoqgS8srF+UIwKBxWzx8-AIAfRDqDSaP0Q-2Wq3WAG4nKBILAEIg2BIIMgcDhKMhXlBmC9kG8cLwtjNJDAoBB0BisQA6DqE0LCCCYHBuaJxYYXZgAek5iBaODgLGQojm9OEiAARmJkJhyNCxYhGczEF1egMziNIlyeXyBUL0CL5cJJdLZaKdMhgNhaNjRcI0FhcARiKQKJS8VAadVuHLEI5rEA) ):

```
26:       exhaustiveCheck(event.type);
                          ^ Cannot call `exhaustiveCheck` with `event.type` bound to `value` because string literal `CHANGE_TODO` [1] is incompatible with empty [2].
References:
14:   type: 'CHANGE_TODO',
            ^ [1]
1: function exhaustiveCheck(value: empty) {
                                   ^ [2] 
```

Enter fullscreen mode Exit fullscreen mode

## 打字稿

[打字稿示例](https://www.typescriptlang.org/play/index.html#src=function%20exhaustiveCheck(value%3A%20never)%20%7B%0D%0A%20%20throw%20new%20Error(%60Unhandled%20value%3A%20%24%7Bvalue%7D%60)%0D%0A%7D%0D%0A%0D%0Atype%20AddTodo%20%3D%20%7B%0D%0A%20%20type%3A%20'ADD_TODO'%2C%0D%0A%20%20payload%3A%20string%0D%0A%7D%0D%0Atype%20RemoveTodo%20%3D%20%7B%0D%0A%20%20type%3A%20'REMOVE_TODO'%2C%0D%0A%20%20payload%3A%20string%0D%0A%7D%0D%0Atype%20ChangeTodo%20%3D%20%7B%0D%0A%20%20type%3A%20'CHANGE_TODO'%2C%0D%0A%20%20payload%3A%20string%0D%0A%7D%0D%0Atype%20Events%20%3D%20AddTodo%20%7C%20RemoveTodo%20%7C%20ChangeTodo%3B%0D%0A%0D%0Afunction%20process(event%3A%20Events)%20%7B%0D%0A%20%20switch(event.type)%20%7B%0D%0A%20%20%20%20case%20'ADD_TODO'%3A%20%2F%2F%20do%20something%0D%0A%20%20%20%20%20%20break%3B%0D%0A%20%20%20%20case%20'REMOVE_TODO'%3A%20%2F%2F%20do%20something%0D%0A%20%20%20%20%20%20break%3B%0D%0A%20%20%20%20default%3A%0D%0A%20%20%20%20%20%20exhaustiveCheck(event.type)%3B%0D%0A%20%20%7D%0D%0A%7D)看起来一样，除了用`never` :
代替`empty`

```
function exhaustiveCheck(value: never) {
  throw new Error(`Unhandled value: ${value}`)
} 
```

Enter fullscreen mode Exit fullscreen mode

错误看起来像:

```
Argument of type '"CHANGE_TODO"' is not assignable to parameter of type 'never'. 
```

Enter fullscreen mode Exit fullscreen mode

[这篇文章是](https://dev.to/t/pragmatictypes)系列文章的一部分。在[推特](https://twitter.com/stereobooster)和 [github](https://github.com/stereobooster) 上关注我。