# 小测验:Javascript 范围

> 原文：<https://dev.to/sheldonnunes/mini-quiz-javascript-scoping-5egp>

一个关于 javascript 范围的快速测试问题。在浏览器中执行之前，尝试解决这个问题

如果你要创建这个对象:

```
var person = {
    name: 'Dave',
    weight: 100,
    height: 180,
    calculateBMI: function() {
        console.log(this);
        function innerFunction() {
            console.log(this);
        }
        innerFunction();
    }
} 
```

运行`person.calculateBMI()`时，innerFunction 记录时`this`会映射到什么对象？