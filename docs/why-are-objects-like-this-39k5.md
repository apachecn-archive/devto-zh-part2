# 物体为什么是这样的？

> 原文：<https://dev.to/tttaaannnggg/why-are-objects-like-this-39k5>

我参加了一个名为“JavaScript 最简单的部分:对象、函数、执行上下文”的研讨会。我认为这将是对函数和作用域的一个非常基本的概述，告诉我局部变量在定义它的函数之外是不可访问的，或者如何在 JS 中声明一个对象，或者存储或调用它的方法。所有我以前在其他教程中做过的东西。是的，我得到了那些东西，但是我也得到很多我想要的理论和基本原理。讲师展示了一个相当全面的模型，包括调用堆栈、全局内存、全局执行上下文和局部范围的执行上下文/内存。

了解到函数、对象、变量和原语在初始化时都存储在内存中，使用它们中的任何一个都意味着调用当前存储在内存中的版本——这解释了为什么重新分配变量有效:您只是重写存储在(全局或局部)内存中的内容，然后访问存储的值。存储在内存中的值不一定与您在代码本身中显式看到的值相同。

我还学习了 JS 中的对象和指针，以及 javascript 中的数组是一个对象。为了节省空间，对象是指针的集合，这些指针存储了保存在其中的数据的位置。一个特别阴险的结果是，当你赋值的时候，比如说 object2 = object1，你没有把 object1 的属性和方法复制到 object2 中。你只是复制了指针(或者你让对象 2 的指针指向对象 1 的指针？不管是哪种方式，好像效果都一样)。

这有一些有趣的副作用。一个是，如果我们指定 object2 = object1，改变 object2 中的任何属性将会以同样的方式改变 object1 中的属性。这是因为两者都指向内存中的同一个位置。

下面是它对对象的工作原理:

```
let objA = {property1: 1};
let objB = objA;

(objA.property1 == 1) // returns true
(objB.property1 == 1) // also returns true

// now let's reassign objB:
objB.property1 = 2;

// and we see this behavior:
(objB.property1 == 2) // returns true
(objA.prooperty1 == 2) // ALSO RETURNS TRUE! 
```

Enter fullscreen mode Exit fullscreen mode

将这与原语的工作方式进行对比。

```
let a = 1;
let b = a;
(a == 1) // returns true
(b == 1) // returns true

//now let's reassign b:
b = 2

// now we see:
a == 1 // still returns true
b == 2 // returns true 
```

Enter fullscreen mode Exit fullscreen mode

让我困惑的是，在 objA 初始赋值后，我们没有对它做任何事情，但是因为 objB 指向 objA，而 objA 指向存储在内存中的属性和值，改变 objB.property1 *仍然会改变 objA.property*

这对我来说有点不直观，但是在我完成上一个问题集时，*真正让我犯错误的是，**数组是 javascript 中的对象**。这意味着，当构建一个函数来处理一个数组时，我不能这样做:* 

```
 function arrayFunction(array){
    array2 = array;
} 
```

Enter fullscreen mode Exit fullscreen mode

并且希望能够操作 array2 而不损坏作为参数传递给 arrayFunction 的数组中的数据。我还没有找到最好的方法来完成它，但是我知道我可以遍历存储在一个数组中的值，并将它们一个接一个地复制到一个新的数组中，以确保它们存储在一个单独的内存块中，我可以在不影响原始数组的情况下操作或销毁它。

如果这还不够的话，我们在比较物体的时候还需要考虑一些事情。

还记得我提到过，对象存储数据实际上是通过一个指针来指引你数据在内存中的存储位置吗？当我们比较对象时，我们实际上是在检查它们是否指向内存中的同一个位置。让我给你看一个例子。

```
 let objA = {property1: 1};
let objB = objA;

objA == objB //returns true, as expected

//but let's make a third object here:
let objC = {property1: 1};

//and let's do some comparisons!

objC == objA // returns false
objC == objB // also returns false! 
```

Enter fullscreen mode Exit fullscreen mode

因此，尽管有相同数量的属性、相同的属性名和相同的值，objC 并不指向存储数据的相同内存地址，因此与 objA 或 objB 不同。相反，我们必须逐个检查它们是否具有相同数量的属性，这些属性的名称是否相同，以及它们的值是否相同，方法是遍历对象。如果有对象存储在对象中，那么我们必须递归地做整个事情，直到我们得到最终的属性和值。

咻。

在 Javascript 中，数组是对象。这意味着所有这些都适用于比较数组。

幸运的是，这样做要简单得多，因为我们可以用 for 循环轻松地遍历任何数组。

```
for (let i=0; i<array.length; i++){
    console.log(array[i]);
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，将打印名为 array 的数组的所有值。我们只需要像这样嵌套两个循环，就可以可靠地比较两个数组。

所以，是的，我的大脑在燃烧，你可能会看到它从我的耳朵里渗出来。我今天要回去参加第二个研讨会，内容是关于递归的。我希望最后我会变得又好又脆。