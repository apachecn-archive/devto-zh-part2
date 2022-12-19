# 用闭包和工厂函数模拟 JavaScript 中的“私有”变量

> 原文：<https://dev.to/somedood/emulating-private-variables-in-javascript-with-closures-and-factory-functions-2314>

尽管最近在 JavaScript 中实现了[类，但是从来没有一种本地方式来控制对象属性的可见性。具体来说，从来没有一种方法可以真正使变量私有。目前，变通办法是我们的最佳选择。最常见的解决方法之一是*下划线符号*。它只是在变量名前加一个下划线(`_`)的惯例。这样做是为了表明变量是私有的，不应该被玩弄。例如，存储敏感信息(如密码)的“私有”变量将被命名为`_password`，以明确声明它是“私有”的。然而，它仍然可以通过写`someObj._password`来访问和变异。它就像您可以更改的任何其他对象属性一样。下划线仅仅是一些标识符前的一个符号。坦率地说，前置下划线只是作为一种约定，对那些可能想访问和改变“私有”变量的人来说是一种不可实施的威慑。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)

# 什么是私有变量？

在许多面向对象的编程语言中，有一种方法可以限制变量在其作用域之外的可见性。换句话说，一些编程语言允许*变量只有*可以被“拥有”它的对象访问。更专业地说，私有变量只对当前类可见。它在全局范围内或其任何子类中都不可访问。例如，我们可以在 Java(和大多数其他编程语言)中通过在声明变量时使用`private`关键字来做到这一点。试图在拥有私有变量的类之外访问该变量将会引发错误。

```
// Example Class
class Example {
    // hiddenVariable CAN only be accessed here
    private String hiddenVariable;

    public Example(String websiteName) {
        hiddenVariable = websiteName;
    }
}

// Main Method
public class Main {
    public static void main(String[] args) {
        // Instantiate class
        Example website = new Example("DEV.to");

        // This will throw an error
        // error: hiddenVariable has private access in Example
        System.out.println(website.hiddenVariable);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

将变量私有有很多原因，从安全到封装。在这种情况下，私有变量只能通过使用好的 ol' [getter 和 setter 方法](https://en.wikipedia.org/wiki/Mutator_method#JavaScript_example)来间接访问和操作。

# 关闭

在 JavaScript 中，当一个函数完成执行时，函数体中声明的任何变量都被“垃圾回收”。换句话说，它被从内存中删除。这就是为什么局部变量在 JavaScript 中是可能的。这就是函数内部的变量不能被外部访问的原因。

```
// dev is NOT accessible here
function someFunc() {
  // dev is accessible here
  const dev = 'to';
}
// dev is NOT accessible here 
```

Enter fullscreen mode Exit fullscreen mode

当函数内部的某些东西依赖于被删除的变量时，就会出现特殊的异常。例如，下面的函数返回另一个依赖于父函数变量的函数。

```
// Parent function
function parent() {
  // Local variable of the parent function
  const prefix = 'I am a ';

  // Child function
  return function(noun) {
    // The child function depends on the variables of the parent function.
    return prefix + noun;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**上面的例子利用了函数式编程中一个叫做[奉承](https://hackernoon.com/currying-in-js-d9ddc64f162e)的概念。如果你想的话，可以多读一些。

```
// Store the returned child function
const getSentence = parent();

// At this point, `parent()` has finished executing.
// Despite that, the `prefix` variable is still
// accessible to the child function. More on that later.
const job = getSentence('programmer');

// What is the value of `job`?
console.log(job); // 'I am a programmer' 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`prefix`仍然可以被子函数使用，即使它已经被垃圾收集了，因为子函数创建了自己的闭包。一个**闭包**就像一个函数执行时所处环境的“快照”。它的闭包是它自己内部的环境副本。

从技术上讲，闭包中的任何变量都只能由拥有它的子函数访问。如果当前执行上下文引用了闭包，则只能对这些变量执行操作。在这种情况下，子函数拥有的“快照”是对闭包的引用，因此它可以访问它的变量。

当`parent`函数执行完毕后，`prefix`变量将被删除。然而，在此之前，子函数会“拍摄”其当前环境的“快照”(包括它所依赖的`parent`函数的所有变量)。子函数现在有了自己的`prefix`变量副本，可以访问和操作。这就是闭包最基本的用例。MDN 提供了一个[更技术性的定义](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)。

# 工厂功能

工厂函数是任何返回对象的函数。是的，差不多就是这样。这不要与类和[构造函数](https://www.w3schools.com/js/js_object_constructors.asp)混淆。**类和构造函数**需要`new`关键字来实例化对象，而**工厂函数**返回实例化的对象本身。

```
function factory(name) {
  return { name };
}

const obj = factory('Some Dood');
console.log(obj.name); // 'Some Dood' 
```

Enter fullscreen mode Exit fullscreen mode

# 对私有变量使用闭包

我们现在已经掌握了在 JavaScript 中模拟“私有”变量所需的所有知识。我们可以从编写一个使用 getter 和 setter 方法返回对象的工厂函数开始。工厂函数接受两个参数，这两个参数对应于返回对象的“私有”属性。

```
function createAnimal(name, job) {
  // "Private" variables here
  let _name = name;
  let _job = job;

  // Public variables here
  return {
    // Getter Methods
    getName() {
      return _name;
    },
    getJob() {
      return _job;
    },
    // Setter Methods
    setName(newName) {
      _name = newName;
    },
    setJob(newJob) {
      _job = newJob;
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以调用工厂函数来创建动物对象的新实例。注意，每次我们调用工厂函数时，都会创建一个新的闭包。因此，每个返回的对象都可以访问自己的闭包。

```
const presto = createAnimal('Presto', 'Digger');
const fluffykins = createAnimal('Fluffykins', 'Jumper'); 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们这样做取得了什么成果呢？嗯，借助闭包的力量，我们实际上已经在 JavaScript 中模拟了“私有”变量。

```
// These properties will be inaccessible
console.log(presto._name); // undefined
console.log(presto._job); // undefined
console.log(fluffykins._name); // undefined
console.log(fluffykins._job); // undefined

// Getter methods have access to the closure
console.log(presto.getName()); // 'Presto'
console.log(presto.getJob()); // 'Digger'
console.log(fluffykins.getName()); // 'Fluffykins'
console.log(fluffykins.getJob()); // 'Jumper'

// Setter methods can mutate the variables in the closure
presto.setName('Quick');
presto.setJob('Bone Finder');
fluffykins.setName('Mittens');
fluffykins.setJob('Fish Eater');

console.log(presto.getName()); // 'Quick'
console.log(presto.getJob()); // 'Bone Finder'
console.log(fluffykins.getName()); // 'Mittens'
console.log(fluffykins.getJob()); // 'Fish Eater' 
```

Enter fullscreen mode Exit fullscreen mode

# 一种奇怪的编程模式的混合物

这种变通方法确实是实现面向对象语言的一个看似简单的特性的奇怪方式。但是，如果仔细分析一下，这种变通办法是有好处的。首先，它将两种不同且颇为冲突的编程范例紧密地结合在一起:[面向对象](https://en.wikipedia.org/wiki/Object-oriented_programming)和[函数式](https://en.wikipedia.org/wiki/Functional_programming)编程。

这种方法的面向对象特性包括使用工厂函数、可变性和封装。另一方面，函数式方法涉及到闭包的使用。JavaScript 确实是一种多范式语言，它继续模糊对比范式之间的界限。

有人可能会说，将这两种范式粘在一起既麻烦又奇怪。在我看来，我不会说那是完全正确的。即使范例的合并不遵循惯例和设计模式，我发现要在 JavaScript 中实现面向对象的特性，必须使用函数式编程的特性，这一点非常有趣。这两个矛盾的范例和谐地一起工作，类似于[的阴阳](https://en.wikipedia.org/wiki/Yin_and_yang)。尽管存在差异，但总有办法让事情运转起来。或许这可以作为生活的类比？