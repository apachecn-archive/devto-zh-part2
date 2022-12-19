# JavaScript:这是什么？

> 原文：<https://dev.to/mingt/what-is-this--lhb>

**这个**是一个有用但也令人困惑的关键字。在这里，我将主要介绍它在函数调用中是如何工作的。

在大多数情况下，函数中 **this** 的值取决于函数的调用方式。让我们看一些例子。如果我们写一个程序来增加一个雇员的工资，

```
const employee = {
    name: 'Foo',
    salary: 1000,
    giveRaise: function(){
        this.salary = this.salary*1.03;
    }
}

employee.giveRaise();
console.log(employee)        
//result:
//{ 
//    name:'foo',
//    salary:1030,
//    giveRaise:[Function:giveRaise]
//} 
```

Enter fullscreen mode Exit fullscreen mode

Foo 的工资从 1000 到 1030 不等。**函数中的这个**被正确绑定到 employee 对象。但是，如果上面的函数是这样调用的:

```
giveRaiseFunction = employee.giveRaise;
giveRaiseFunction();
console.log(employee);
//result:
//{
//    name: 'Foo',
//    salary: 1000,
//    giveRaise: [Function: giveRaise]
//} 
```

Enter fullscreen mode Exit fullscreen mode

可惜 Foo 工资不涨。当调用函数 giveRaiseFunction()时，该函数没有绑定到任何对象。因此，**这个**将引用全局对象，并且在浏览器中，它将是窗口，并且在窗口中没有薪水关键字。在某些情况下，如果人们使用严格模式，**这个**将会是它在执行上下文时被设置的值，缺省值将会是未定义的。

现在，考虑另一种情况，如果我们需要一个更复杂的函数，我们可能需要一个调用其他函数的函数。例如，

```
const employee = {
    name: 'Foo',
    salary: 1000,
    giveRaise: function(){
        const calculateIncrease = function(){
            //console.log(this);
            return this.salary*0.03
        }
    }
    //console.log(this);
    //console.log('-------------------------');
    this.salary = this.salary+calculateIncrease();
}

employee.giveRaise();
console.log(employee)

//result:
//{
//    name: 'Foo',
//    salary: NaN,
//    giveRaise: [Function: giveRaise]
//} 
```

Enter fullscreen mode Exit fullscreen mode

这一次，奇怪的结果发生了。薪资值被认为不是一个数字。如果我们做 console.log 测试来检查到底发生了什么，我们会发现 calculateIncrease 函数中的这个关键字实际上绑定到了全局对象而不是雇员对象。

在 ES6 中，引入了定义函数的新语法——箭头函数。当使用 arrow 函数语法时，函数没有自己的 **this** 绑定。它仍然是封闭词法上下文的 **this** 值(arrow 函数的父函数)。这样，**这个**将引用父对象，在本例中是雇员。

```
const employee = {
    name: 'Foo',
    salary: 1000,
    giveRaise: function(){
        const calculateIncrease = ()=>{
            return this.salary*0.03
        }
        this.salary = this.salary + calculateIncrease();
    }
}

employee.giveRaise();
console.log(employee)

//result:
//{
//    name: 'Foo',
//    salary: 1030,
//    giveRaise: [Function: giveRaise]
//} 
```

Enter fullscreen mode Exit fullscreen mode