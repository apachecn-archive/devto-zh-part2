# 搞什么...吊装？

> 原文：<https://dev.to/genta/what-the-hoisting-5f82>

在 Javascript 中提升...从来都是**语言本身真正不好的**行为。

# 你为什么要为我这么做？

如果你不知道我在说什么，那就是(简而言之)提升是什么:

```
console.log(test)
// -> undefined
var test = "I'm not here"; 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，当我调用 *console.log(test)* 变量本身，根本没有被声明！。

但是 Javascript 是一个坏人，它选择为你声明它。
事情是这样的:

```
//javascript will declare the variable, moving it at the TOP of it's scope
var test;
console.log(test)
// -> undefined
test = "I'm not here"; 
```

Enter fullscreen mode Exit fullscreen mode

这真的很混乱，根本不应该发生！。

我们还没有完成，因为这也发生在函数的范围内。
像这样:

```
function test(){
  return test;
  var test = "I'm not here";
}

console.log(test())
// -> undefined 
```

Enter fullscreen mode Exit fullscreen mode

## 并且还与函数本身发生关系！。

如果我们将调用移动到调用栈顶部的 *console.log(test)* ，那么
函数 *test()* 应该不存在...

```
console.log(test())

function test(){
  var test = "I should not be here";
  return test;
}

// -> "I should not be here" 
```

Enter fullscreen mode Exit fullscreen mode

无关...Javascript 将该函数移到了作用域的顶部...所以你可以在声明函数之前调用它...

但是等等:

```
console.log(test)
// -> undefined
var test =function test(){
   var test = "I'm not here";
   return test;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们可以在声明函数之前调用它，为什么会有一个未定义的函数？

因为，在最后这段代码中，只有 *var test* 声明被移到了顶部，而不是分配给它的函数。

## 我们来回顾一下！

**每次**你声明一个*变量*到一个作用域或者写一个函数声明， **Javascript 提升移动那些在它们作用域顶部的变量而不移动它们的值**。

<center>No way... I'm leaving!</center>

[![alt text](img/2dea2b1eb01faed933485840c36558ca.png "Logo Title Text 1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eBgaU2Pv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/4jop4FB.gif)

# 如此...**【让】****【常】**...他们是我们的救星！

让我们看看，如果我们尝试做同样的肮脏的事情:
，那么*让*和*常量*会发生什么

```
function test(){
    return confusing;
    let confusing = "Finally!";
}

console.log(test())
//-> ReferenceError: can't access lexical declaration `confusing' before         initialization 
```

Enter fullscreen mode Exit fullscreen mode

**啊啊啊！**原来，你在这里！

const:
也是如此

```
function test(){
    return confusing;
    const confusing = "Still Safe!";
}

console.log(test()) 
```

Enter fullscreen mode Exit fullscreen mode

**你能猜出，在全局范围内“let”和“const”会发生什么吗？**

[![alt text](img/62c17cafe82015ade1d592881fa22674.png "Logo Title Text 1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--83T58CaE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://m.popkey.co/8b80f7/Nv8Gx.gif)

因为 let 是一个“块范围局部变量”,你需要把它放到一个块中来释放它的能力...

<center>*But this is another story*</center>

[![alt text](img/e40843dbf663b336e5c15e27f7f74fdc.png "Logo Title Text 1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t5kDXrz8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media3.giphy.com/media/udRSyh5yLCqKQ/giphy.gif)