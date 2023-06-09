# 向我妈妈解释了 3 个令人毛骨悚然的 Javascript 概念

> 原文：<https://dev.to/migueloop/3-javascript-creepy-concepts-explained-to-my-mom-8ap>

有些事情并不像我们所希望的那样容易理解。这就是人们常说的“这是一种奇特的语言”。我更喜欢 Java，因为它更简单。那是几年前我自己的想法。

所以对于面向编程的人来说，我相信会容易一些，我会用 [ES6](http://www.slideshare.net/MiguelRuizRodriguez/internal-workshop-es62015?trk=v-feed) 来捕捉这些概念，所以[T3】Vamos al grano。](http://www.linguee.com/spanish-english/translation/vamos+al+grano.html)

### 关闭

JS 中的闭包不容易理解，但这是继“两倍等于和三倍等于之间的区别”之后的一个非常常见的面试问题。

也称为**词法作用域**或**静态作用域，**闭包是一种抽象机制，允许你非常干净地分离关注点。

对我来说，最简单的定义是下一个:

闭包是一个内部函数，它可以访问外部(封闭)函数的变量——作用域链。

```
function foo() {
  var a = 2;

  function log() {
    console.log("a: " + a);
  }

  return log;
}
var a = 3;
var bar = foo();
bar();
console.log('Global a: '+ a); 
```

Enter fullscreen mode Exit fullscreen mode

结果将是:

*"a: 2"*

*《环球 a:3》*

### 回调

回调是作为参数传递给另一个函数 **的函数。**这个传递的函数将在另一个函数中被调用(或执行)。

```
function executor (callback){
  if(callback){
   console.log('Result after calling your function ' + callback())
  }
  else{
    console.error('No callback received')
  }
}
executor( () => (1 + 1) ) 
```

Enter fullscreen mode Exit fullscreen mode

最简单的情况是:

1.  我们有一个名为 executor 的函数，它将执行所有作为参数传入的内容
2.  我们通过传递函数作为参数来调用 executor
3.  结果将是:*“调用函数 2 后的结果”*

### 承诺

承诺表示异步操作的最终结果。它是一个占位符，成功的结果值或失败的原因将在其中具体化。

#### 宣告诺言

承诺可以是以下三种状态之一:

*   pending→promise 的结果还没有确定，因为将产生其结果的异步操作还没有完成。
*   履行→异步操作已经完成，承诺有了值。
*   拒绝→异步操作失败，承诺永远无法兑现。在拒绝状态下，一个承诺有一个*原因*，表明操作失败的原因。

```
var p = new Promise( (resolve, reject) => {  
   if (/* condition */) {
      resolve(/* value */);  // fulfilled successfully
   }
   else {
      reject(/* reason */);  // error, rejected
   }
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 一诺千金

承诺的主要 API 是它的`then`方法，该方法注册回调以接收最终值或承诺无法实现的原因。

```
var prom = new Promise((resolve, reject) => resolve(5));   
prom.then((val) => console.log(val)); // 5 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以检查承诺执行中的任何错误:

```
var prom = new Promise((resolve, reject) => resolve(5));   
prom
.then((val) => console.log(val))
.catch((err) => console.error(err)) 
```

Enter fullscreen mode Exit fullscreen mode