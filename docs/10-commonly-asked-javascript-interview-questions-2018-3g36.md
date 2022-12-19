# 2018 年 10 个常见 Javascript 面试问题

> 原文：<https://dev.to/sharadtricks/10-commonly-asked-javascript-interview-questions-2018-3g36>

Javascript 是最流行的网络编程语言之一。它广泛用于创建交互式客户端 web 应用程序和游戏。Javascript 也可以作为服务器端编程语言。它是动态的、弱类型的、基于原型的、多范例的高级编程语言。

[![Common Javascript Interview Questions](../Images/cc66047b62cd14e088dafc36d2fa92fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--INV4HFyZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sae8qu6pp3zj05mgqkfy.png)

这里我们列出了 10 个常见的 Javascript 面试问题及其答案

## 1.变量 x，y，z 的输出会是什么？

var x = 10
var y = x++；
var z = ++ x；
console.log(x，y，z)
Ans:会记录 12，10，12

## 2.0==-0 的输出会是什么

答:输出 0==-0 为真

## 3.下面代码的输出是什么

函数 XYZ(){
var ABC = " hello Js "；
函数 abc () {
返回 54；
}
返回 abc
}
console.log(xyz())
答案:上面的代码会输出 hello Js。

## 4.如何用 JavaScript 创建一个对象？

Ans:在 Javascript 中创建对象 var object = { name:“obj”，年龄:10 }；

## 5.解释 JavaScript 中的原型继承？

在 JS 中，每个对象都有一个称为原型的属性，我们可以向它添加方法，当你从这些对象中创建另一个对象时，新创建的对象将自动继承其父对象的属性。

## 6.用 JavaScript 解释回调？

回调是作为参数或选项传递给某个方法的普通 JavaScript 函数。有些回调只是事件，当某个状态被触发时，调用它们是为了给用户一个反应的机会。

## 7.如何在 JavaScript 中清空 below 数组？

Var emptyArray = ['this '，' Array '，' is '，' full ']；答:在 JavaScript 中使用下面的代码行清空数组

empty array . length = 0；

emptyArray.splice(0，empty array . length)；

while(empty array . length){
empty array . pop()；
}

emptyArray = []

## 8.下面的代码会输出什么？

var lorem = { ipsum:1 }；
var output = (function(){
删除 lorem.ipsum
返回 lorem.ipsum
})()；

console.log(输出)；
答:输出将是未定义的，因为在对象“lorem”被返回之前，删除操作符从该对象中删除了属性“ipsum”。当引用已删除的属性时，结果是未定义的。

## 9.5+4+“3”的输出是多少？

因为 3 是一个字符串，所以上面代码的输出将是 93。

## 10.Javascript 的真名是什么？

Javascript 的原名是由 Marc 寻址的 Mocha。他是网景公司的创始人。

感谢您的阅读，祝您面试顺利！查看更多 [JavaScript 面试问题【onlineinterviewquestions.com】上](https://www.onlineinterviewquestions.com/advanced-javascript-interview-questions/)[的&答案](https://www.onlineinterviewquestions.com/)