# 用面向对象的 JavaScript 创建对象的 3 种方法

> 原文：<https://dev.to/educationecosystem/3-ways-to-create-objects-in-object-oriented-javascript-18nl>

(本文原载于我的博客[此处](https://blog.liveedu.tv/3-ways-to-create-objects-in-object-oriented-javascript/))。

JavaScript 是一种强大的编程语言，支持面向对象编程(OOP)。

事实上，在 JavaScript 中，对象主宰一切——从字符串和数组等核心特性到使用该语言构建的浏览器 API。

美国网络开发人员 Bautista 热衷于教人们编程，他强调“要想把你的 JavaScript 技能提高到一个新的水平，你需要理解这种语言基于对象的本质。”

以下是在面向对象的 JavaScript (OOJS)编程中创建对象的三种方法:

# 1。使用对象文字

在 OOP 中，一个对象文字指的是一个用逗号分隔的名字-值对列表，包含在花括号中。

名称是字符串，值是 JavaScript 中可用的任何基本数据类型，比如数组、字符串、数字、函数等等。

通常，对象文字用于封装代码，并将其打包成一个有序的包。这样，它们可以防止与全局范围内的变量和对象发生冲突。

使用对象文字，您可以将属性和方法聚集在一起，使您的代码干净整洁。

这里有一个例子:

```
var liveedu = {
  //declaring properties
student: "james",
  //declaring methods
watch: function() {
    console.log("learn new tech skills");
  },
};
//accessing methods and properties
liveedu.watch();
//output is learn new tech skills 
```

JavaScript 对象文字是单例的，它们允许您方便灵活地创建对象。

它们让你不用写太多的代码。

例如，您可以将一个对象文字放在工作区的任何地方，而不包括任何先前的设置，它仍然可以很好地工作——这非常有用！

尽管对象文字很重要，但它们不支持实例化或继承。

如果您想利用这些特性，您需要使用其他技术来创建对象。

# 2。使用对象构造函数

构造函数是创建 JavaScript 对象的最传统的技术，这些对象依赖原型继承来利用彼此的功能。

这些函数的一个关键特征是它们可以被实例化和继承。

让我们看一个如何在 OOJS 中使用构造函数的例子。

```
function Liveedu(student) {

      // properties

    this.student = student;

    // methods

    this.watch = function() {

      console.log(this.student + "learns new tech skills");

    }
}

// instantiating the object

var liveedu = new Liveedu("James ");

// accessing methods and properties

liveedu.watch(); //output is James learns new tech skills

console.log(Object.getPrototypeOf(liveedu)); // output is object 
```

下面是上面代码中发生的情况:

*   构造函数的创建就像普通函数一样。然而，不同之处在于 *this* 关键字用于声明属性和方法。在这种情况下，*这个*表示目前在 *Liveedu* 功能范围内的对象。

*   使用 *new* 操作符创建一个名为 *liveedu* 的新对象。通常， *new* 将新创建的对象绑定到被调用的构造函数中的 *this* 操作符。因此，绑定使得 *liveedu* 对象能够获取构造函数的属性和方法。

*   *liveedu* 对象有一个被称为*原型*的属性，在这里定义了所有继承的成员。因此，当一个类似于 *watch()* 的函数被调用时，浏览器将沿着对象链和它们各自的原型属性向上移动，直到检索到它的值。

# 3。使用原型继承

JavaScript 对象也可以使用原型继承的概念来创建。

大多数现代浏览器使用一个叫做 **proto** 的特殊属性来实现原型，这个属性读作 *dunder proto* (双下划线原型的缩写)。

让我们用下面的例子来说明如何在原型继承中使用 **proto** 。

```
var liveedu = { 

    student: "james",

    watch: function() {

        return this.student + " is learning new skills";
    }
}

var livecoding = { 

    student: "felix",

    watch: function() {

        return this.student + " is learning new skills";
    }
} 
```

正如你在上面的代码中看到的，两个对象有相似的方法，这使得代码看起来是多余的。

因此，为了使对象共享相同的*观察*方法，我们可以使用**原型**原型属性。

换句话说，我们可以使用原型来扩展对象的属性。

下面是上面代码的重写:

```
var WatchProto = {

    watch: function() {

        return this.student + " is learning new skills";
    }

}

var liveedu = { 

    student: "james",

    __proto__: WatchProto

}

var livecoding = { 

    student: "felix",

    __proto__: WatchProto

}

console.log(liveedu.watch()); //james is learning new skills

console.log(livecoding.watch()); //felix is learning new skills 
```

正如您在上面的代码中看到的，这两个对象共享在 *WatchProto* 中定义的相同方法。 *liveedu* 和 *livecoding* 对象可以直接访问它，导致代码更加干净和高效。

需要注意的是， **proto** 是一种新的 JavaScript ES6 语法，可能在旧浏览器中不可用。

或者，您可以使用 *Object.create()* 方法来创建原型。

这里有一个例子:

```
var WatchProto = {

    watch: function() {

        return this.student + " is learning new skills";
    }

}

var liveedu = Object.create(WatchProto);

liveedu.student = "james"; 
```

# 包装完毕

理解 JavaScript 对象是深入了解这种无处不在的语言的关键。

你在实现 JavaScript 的面向对象编程特性方面有什么经验？

请在下面分享你的评论和问题。