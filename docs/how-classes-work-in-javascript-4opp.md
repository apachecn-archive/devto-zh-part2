# JavaScript 中的类如何工作

> 原文：<https://dev.to/sait/how-classes-work-in-javascript-4opp>

与其他编程语言不同，javascript 不包含合适的类。

通过使用构造函数，类使用基于原型的继承。

## 什么是构造函数？

构造函数是一个函数对象，用于创建和初始化对象。

[![constructor](img/4adc1043114fbd6ad1bcfd5942cef617.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Kag1A8uC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/60g6uuod7iamst9xacpe.png)

现在我们用例子来讨论。

```
function Student(){

}
const student = Student();
console.log(student) // undefined 
```

Enter fullscreen mode Exit fullscreen mode

如果我们用 new 关键字调用一个函数，它将返回空对象而不是 undefined。

```
function Student(){
}

const student1 = new Student();
console.log(student1) //  { } 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/3d7f4590690d7258ae8b28c962262748.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ibdPNjwx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wq1igshbqnk9ctfrg1dg.png)

如何在返回的空对象中添加属性？

```
function Student(name,age){

  this.name = name
  this.age = age
}

const student1 = new Student('king',20)
// { name : 'king',age:20 } 
```

Enter fullscreen mode Exit fullscreen mode

新的对象由构造函数创建，并分配给关键字 this 以便我们可以使用 this.propertyname 向对象添加属性。

最后，构造函数返回如下图所示的`this`对象。

[![](img/21788b2590cebf642c3787f62e16df2e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KU0Z8qxV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gel8sia5i8gvvs99jggk.png)

### 类

类也是一种使用基于原型的继承的特殊类型的函数。

要声明类，我们需要使用 class 关键字。

```
class Student{

constructor(name,age){
  this.name = name
  this.age = age
}

}

const student1 = new Student('king',20)

// { name : 'king',age:20 } 
```

Enter fullscreen mode Exit fullscreen mode

### 类中的方法

```
class Student{

 constructor(name,age){
  this.name = name
  this.age = age
}

 getAge(){
   return this.age;
 }

}

const student1 = new Student('king',20)
student1.getAge(); 
```

Enter fullscreen mode Exit fullscreen mode

我们在 Student 类中声明的方法被添加到它的原型中，它将这些方法分配给`this.__proto__`属性。因此，在访问 getAge 方法时，javascript 引擎首先查找它的对象，如果找不到，它将查找`__proto__`属性。

看看下面的图片，你会明白的。

[![__proto__](img/0ce622b712373347a70ac91ca18db1e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IdzZmz00--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lo9wff2r9vrheji17esa.png)

### 在类中延伸

Extends 关键字帮助我们访问其他类或父类中的属性和方法。

如果我们从父类扩展类，我们需要在使用子类之前调用父类，javascript 提供了一个超级方法，它为我们完成调用。

```
class Csestudent extends  Student{

    constructor(name,age,course){
      super(name,age);
     this.course = course
    }

   getCourse(){
      return this.course
   }
}

const student = new Csestudent('king',20,'JavaScript') 
```

Enter fullscreen mode Exit fullscreen mode

在下图中，我展示了 javascript 引擎是如何处理代码的。

[![](img/54e78a0fe32b5876afce5bf0319b8cd0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0eGw1NIQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n3ze8dcdghy4jquec1fo.gif)

让我解释一下当我们调用一个 Csestudent 类时会发生什么。javascript 引擎首先将 Csestudent 添加到调用堆栈中。一旦它看到 super 方法，就会调用作为学生的父类，并返回带有两个属性的对象。最后，我们将 **course** 属性添加到该对象中。

最终的学生对象可能如下所示。

[![](img/6e806873849ca2d211ac69707f2ee8a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iOgtUE2g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k8khnv0bydgxi4e0urcj.png)

但是我们在学生类中声明的方法出现在`this.__proto__.__proto__`中

#### 参考文献

*   [Ecmascript-262 对象](https://www.ecma-international.org/publications/files/ECMA-ST-ARCH/ECMA-262,%203rd%20edition,%20December%201999.pdf#sec-11.2.2)

    *   [Ecma-262 等级定义](https://www.ecma-international.org/ecma-262/9.0/index.html#sec-class-definitions)

最初发布于
[**关注我的推特**](https://twitter.com/saigowthamr)