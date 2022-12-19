# 共同的敌人，JavaScript 的“This”关键字拯救了世界

> 原文：<https://dev.to/ogwurujohnson/the-common-enemy-javascripts-this-keyword-saves-the-day-4fj8>

各地的开发人员，无论是经验丰富的还是刚刚开始编码的，都曾经或者仍然不理解 javascript 的关键字`this`,大多数认为它很复杂。理解和简化 JavaScript 中`this`关键字背后的思想的关键是通过理解；

1.  为什么我们需要`this`以及为什么我们首先应该使用`this`。
2.  `this`如何工作。
3.  如何识别`this`绑定的是什么？

根据 [Getify 在他的“你不知道 JS 系列”](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md)中的说法，解释`this`为“在每个函数的范围内自动定义的特殊标识符关键字，但是它确切指的是什么甚至困扰着经验丰富的 Js 开发人员”。

但是**为什么**我们需要`this`？

[Getify](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md) 解释说`this`提供了一种更优雅的隐式“传递”对象引用的方式，导致更干净的 API 设计和更容易的重用。你的使用模式越复杂，你就会越清楚地看到，传递上下文对你没有好处，对阅读你代码的人也没有好处。`This`允许我们在不同的上下文中重用函数，换句话说，它允许我们在调用函数方法时决定哪个对象应该是焦点。

使用`this`有很多困惑；
大多数开发人员试图将它想得过于字面化。大多数开发人员认为`this`指的是函数本身，但是他们没有注意到`this`绑定与函数声明的位置无关，而是与调用函数的方式有关。

但是，我们如何确定`这必然是什么？；
*注:*

1.  `this`的值通常由函数执行上下文决定。这里的执行上下文是指如何调用函数。
2.  重要的是要知道，这可能是不同的(指不同的东西)，每次调用该函数。

[布兰登·莫雷利](https://codeburst.io/javascript-the-keyword-this-for-beginners-fb5238d99f85)在他的文章《Javascript:初学者的关键词‘this’》中，概述了你可以用来确定`this`绑定什么的五条通用规则；

A.**全局对象:**启动你的 chrome 控制台( *ctrl+shift+j* )，键入；`console.log(this)`，你注意到输出的是*窗口对象*，这意味着‘this’指的是全局对象，因为我们在全局范围内。但是为了进一步证明这里的`this`指的是全局对象，让我们声明一个变量，在你的控制台中粘贴这段代码，`var myName =
Johnson;`，现在当你调用`myName`时，它返回*约翰逊*但是猜猜还有什么会返回*约翰逊*现在复制并粘贴代码`window.myName`，看看它返回什么。

所有这些意味着在全局范围内声明的每个变量都被附加到全局或窗口对象上，这就是为什么`this`在全局范围内声明时引用全局对象。

记住我们的*注①*；此的值由调用它的时间决定。假设我们有一个函数返回`this`你认为控制台会记录什么；在运行代码之前尝试猜测。结果是`this`，这次会引用*窗口对象*，这是为什么呢？因为离`this`关键字最近的父对象仍然是全局对象。

```
function name()
{
    console.log(this);
}

name(); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用来确定`this`绑定到什么的另一个规则是；

B.**声明的对象:**
在这种情况下，当关键字`this`被用在声明的对象(比如说`var person = {};`)内部时，`this`关键字指的是该方法被调用的最近的父对象。举个例子；

```
var author = 
{
  first: 'Johnson',
  last: 'Ogwuru',  
  full: function() 
    {
      console.log(this.first + ' ' + this.last);
    }
};

author.full(); 
```

Enter fullscreen mode Exit fullscreen mode

它`logs=>(Johnson Ogwuru)`。进一步证明`this`在这个上下文中是指最近的父对象；在控制台上复制并运行以下代码；

```
var author = 
{
  first: 'Johnson',
  last: 'Ogwuru',  
  full: function() 
    {
      console.log(this);
    }
};
author.full(); 
```

Enter fullscreen mode Exit fullscreen mode

正如您所见，控制台返回了*人物对象*，证明`this`已经获取了`author`的值。最后一件事，记得我们说过`this`是指最近的父对象；但是如果我们有嵌套的语句呢？

```
var author = 
{
  first: 'Johnson',
  last: 'Ogwuru',  
  full: function() 
    {
      console.log(this.first + ' ' + this.last);
    },
  author2: 
    {
        first : "Jane",
        last: "Johnson",
        full: function() 
        {
          console.log(this.first + ' ' + this.last);
        }
    }
};

author.full();
author.author2.full(); 
```

Enter fullscreen mode Exit fullscreen mode

当`author.full()`被调用时，函数内部的`this`被绑定到`author object`，当`author.author2.full()`被调用时，`this`被绑定到离它最近的对象`author2 object`。

**最后要注意的几点:**

1.  `this`的值通常由函数执行上下文决定。
2.  在全局范围内，`this`是指全局对象。
3.  根据 [getify](#) ，我们使用`this`关键字来避免复杂性。想象一下我们之前的例子中的场景，我们尝试使用`author.first`而不是`this.first`，碰巧我们有另一个名为`author`的全局变量(*，我们可能知道也可能不知道*),`this`试图引用它，会导致*难以调试的错误。*

点击此链接阅读更多关于`this`的内容；

1.  [代码突发](https://codeburst.io/javascript-the-keyword-this-for-beginners-fb5238d99f85)
2.  [JavaScriptisSexy](http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/)