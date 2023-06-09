# var vs let vs const

> 原文：<https://dev.to/rslim87/var-vs-let-vs-const-62j>

旧习难改。这适用于我，当涉及到声明变量时，尤其是当我进入流程时，我只是自动地用 var 声明所有变量。所以这篇文章的目的是让我一劳永逸地戒掉这个习惯。

Var 是函数作用域。这意味着当一个变量在一个函数中用一个变量声明时，这个变量不能在这个函数之外被访问，它将抛出一个错误。

```
function sound() {
    var dog = "woof"
    console.log(dog) 
}
sound()
/// woof
woof
/// Uncaught ReferenceError: dog is not defined 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果在块范围内用变量声明了一个变量，则可以在该块范围之外访问该变量。

```
var sound = "woof"
if (sound === "woof") {
 var animal = "dog"
 console.log(`${animal} goes ${sound}`)
}
/// dog goes woof
animal
/// "dog" 
```

Enter fullscreen mode Exit fullscreen mode

这个问题可以通过用 let 声明变量来解决，let 是块范围的。

```
var sound = "woof"
if (sound === "woof") {
 let animal = "dog"
 console.log(`${animal} goes ${sound}`)
}
/// dog goes woof
animal
/// Uncaught ReferenceError: animal is not defined 
```

Enter fullscreen mode Exit fullscreen mode

Const 类似于 let，但有一个很大的区别。常量不能被重新分配。与 var 不同，const 和 let 也不能被重声明。