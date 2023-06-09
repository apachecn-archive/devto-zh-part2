# Javascript 中的构造函数是什么

> 原文：<https://dev.to/jdissan/what-is-constructor-in-javascript-2jab>

在 JS 世界里有一个术语叫做构造函数，像我这样有 C#/Java 背景的人在实际应用中实现这个时会有很多困惑。所以我做了一个小小的研究，得出了以下的认识

[![Alt text of image](img/55d852b541e598d152afb6934eef9e0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oWJwQXS3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theleaguelady.com/wp-content/uploads/2018/07/dont-believe-everything-you-read.jpg)T3】

```
function jsObject(name){ this.name = name };
// call function
let obj = jsObject("myName");
obj.name;

// VM603:1 Uncaught TypeError: Cannot read property 'name' of undefined
// at <anonymous>:1:3 
```

让我们解决这个问题

```
function jsObject(name){ 
   var myObj = {};
   myObj.name = name;
   return myObj;
};

// call function
let obj = jsObject("myName");
obj.name;

// --> myname 
```

工作正常，也就是说，没有 javascript 提供一些很酷的特性

```
 // call function
let obj =  new jsObject("myName");
obj.name;
// --> myname 
```

发生了什么，是的，javascript 给了开发者一些很酷的东西。

```
 function jsObject(name){ 
   // var this = {};-> add at run time 
   this.name = name;
   // return this; -> add at run time 
}; 
```

现在我知道了构造函数，但是请随意添加您的注释