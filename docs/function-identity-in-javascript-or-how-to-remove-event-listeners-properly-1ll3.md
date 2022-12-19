# JavaScript 中的函数标识，或者如何正确移除事件侦听器

> 原文：<https://dev.to/smotchkkiss/function-identity-in-javascript-or-how-to-remove-event-listeners-properly-1ll3>

上周，我查看了我的一个同事写的 JavaScript，发现了一个有趣的错误。有趣的是，因为我知道我自己在某个时候被这个问题绊倒了，在别人的代码中看到它让我知道这确实是一个更多人正在努力解决的棘手问题，所以我认为它可能值得分享。

(我还为他们写了一篇很长的文章，描述了这个问题以及如何解决它，这是这篇文章的基础。)

这是问题代码的第一部分(我们讨论的是浏览器中的普通 ES6，没有框架。这也不是原始代码，我删除了所有与我想表达的观点无关的内容):

ⓐ

```
// For completeness, imagine these being something sensible:
let elements = document.querySelectorAll(/* ... */)
function mouseenterHandler(element) {
  // ...
}

// This is the interesting part:
elements.forEach(element => {
  element.addEventListener('mouseenter', () => {
    mouseenterHandler(element)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

查询一些 DOM 元素，并为每个元素附加事件侦听器。

然后，在更深处的某个地方，在拆卸程序中:

ⓑ

```
elements.forEach(element => {
  element.removeEventListener('mouseenter', () => {
    mouseenterHandler(element)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

很明显，这是试图通过使用*等价的匿名函数*作为参数调用`removeEventListener`来注销事件监听器。

## 问题

`() => { this.mouseenterHandler(element) }`在ⓐ中是一个*匿名函数*，我们没有保留对它的引用(即，它没有保存在变量中，也没有以任何方式命名)。

`() => { this.mouseenterHandler(element) }`在ⓑ是一个*等价的匿名函数*。这里需要注意的重要一点是，它们是*等价的*，而不是*相同的*。

## JavaScript 如何比较函数

JavaScript 中的函数是对象，像所有其他对象一样，它们通过引用进行比较。这意味着 JavaScript 无法确定两个函数的等价性。

### 你没有连载它们吗？

现在我们可能会想，JavaScript 已经可以序列化函数了，为什么不简单地通过字符串表示来比较它们呢？

```
let f1 = (x) => { return x + 1 }
let f2 = (x) => { return x + 1 }

console.log(f1.toString()) // '(x) => { return x + 1 }'
console.log(f2.toString()) // '(x) => { return x + 1 }'

// ... so:
console.log(f1.toString() === f2.toString()) // true - yay!? 
```

Enter fullscreen mode Exit fullscreen mode

但是让我们来考虑这个略有不同，但可以说是等价的函数:

```
function f3(x) {
  return x + 1
}

console.log(f3.toString()) // 'function f3(x) {\n  return x + 1\n}' 
```

Enter fullscreen mode Exit fullscreen mode

很明显，`f1.toString() === f3.toString()`和`f2.toString() === f3.toString()`将为假，尽管对于[ `Number.MIN_SAFE_INTEGER`，`Number.MAX_SAFE_INTEGER - 1` ]中任何给定的 x，`f1(x) === f3(x)`和`f2(x) === f3(x)`将为真也是微不足道的(实际上对于`x`的许多其他值也是如此)。

所以这个方法只适用于以完全相同的方式写出的函数。

### 实际上是怎么做的

在 JavaScript 中，基本上有三种*基本数据类型是不可变的*，这是一种奇特的比较科学的说法，它们的行为有点像纸笔数学中的事情。其中一种就是`Number`型。在数学中，只有一个数字𝟐.在这里谈论这个𝟐，在那里谈论那个𝟐是没有意义的。我们可以随心所欲地写出*字符* 𝟐，但它们中的每一个仍然是对同一个单一数字𝟐.的引用在 JavaScript 中也是如此:* 

```
let a = 2
let b = 2

console.log(a === b) // true 
```

Enter fullscreen mode Exit fullscreen mode

JS 中另外两个*基本数据类型是`String`和`Boolean`。这就是为什么我们能够通过等价来比较`f1`、`f2`和`f3`的字符串表示。

**JavaScript 中的其他所有东西**都通过引用进行比较*。每次我们写`[]`，我们创建*一个新的数组*，它是*而不是我们下次写`[]`的同一个*，每次我们写`{}`我们创建一个新的对象，每次我们写`() => {}`，我们创建一个新的函数。*

<small>(严格来说，不是每次*我们写*`[]``{}`或者`() => {}`，而是每次这些*中的一个被求值*。这实际上是一个很大的区别。想象一下像`function makeObj () { return {} }`这样的函数——每次我们调用`makeObj()`，它都会返回一个新的对象。)</small>

换句话说，

```
console.log([] === []) // false
console.log({} === {}) // false
console.log((() => {}) === (() => {})) // false, too! 
```

Enter fullscreen mode Exit fullscreen mode

但是

```
let a1 = []
let a2 = a1
console.log(a2 === a1) // true
let o1 = {}
let o2 = o1
console.log(o2 === o1) // true
let f4 = () => {}
let f5 = f4
console.log(f5 === f4) // also true 
```

Enter fullscreen mode Exit fullscreen mode

## 这和我们的 eventListeners 有什么关系

想象一下，对于 DOM 中的每个元素，JavaScript 创建一个数组来保存所有的 mouseenter 侦听器，如下所示:

```
let myElementMouseenterListeners = [] 
```

Enter fullscreen mode Exit fullscreen mode

每次我们添加一个事件监听器，比如

```
myElement.addEventListener('mouseenter', () => { console.log('yay') }) 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 在内部只是将它添加到数组:

```
let myListenerToAdd = () => { console.log('yay') }

myElementMouseenterListeners.push(myListenerToAdd)

console.log(myElementMouseenterListeners) // [ [Function] ] 
```

Enter fullscreen mode Exit fullscreen mode

当`'mouseenter'`事件发生时，JS 将调用数组中的每个函数:

```
let myMouseenterEvent = new MouseEvent('mouseenter')

myElementMouseenterListeners.forEach(mouseenterListener => {
  mouseenterListener(myMouseenterEvent)
}) 
```

Enter fullscreen mode Exit fullscreen mode

当我们试图删除一个事件监听器时，JavaScript 将遍历事件监听器数组，将其中的每个函数与我们试图删除的函数进行比较，如果恰好是*相同的*，则将它从数组中删除。

想象我们做:

```
myElement.removeEventListener('mouseenter', () => { console.log('yay') }) 
```

Enter fullscreen mode Exit fullscreen mode

而 JavaScript 做到了:

```
let myListenerToRemove = () => { console.log('yay') }

for (let i = 0; i < myElementMouseenterListeners.length; i++) {
  if (myElementMouseenterListeners[i] === myListenerToRemove) {
    myElementMouseenterListeners.splice(i, 1)
    break
  }
}

console.log(myElementMouseenterListeners) // still [ [Function] ] 
```

Enter fullscreen mode Exit fullscreen mode

这意味着，当循环到达我们在开始时添加的侦听器时，它会将它与我们提供给`removeEventListener`的侦听器进行比较，所以发生的事情基本上是这样的:

```
() => { console.log('yay') } === () => { console.log('yay') } 
```

Enter fullscreen mode Exit fullscreen mode

正如我们之前所讨论的，结果为假。

这意味着像
这样的代码

```
element.removeEventListener('eventname', () => { console.log('event handled') }) 
```

Enter fullscreen mode Exit fullscreen mode

用那个时刻刚刚创建的*匿名函数*作为第二个参数调用`removeEventListener`，**永远不会对**产生任何影响。相反，它会无声无息地失败。

## 我们需要做的事情(可能的解决方案)

为了让`removeEventListener`生效，我们必须提供一个对我们之前通过`addEventListener`注册的函数的引用。

一般来说，类似于

```
let element = document.querySelector(/* ... */)
function mouseenterHandler() {
  // ...
}

element.addEventListener('mouseenter', mouseenterHandler)

element.removeEventListener('mouseenter', mouseenterHandler) 
```

Enter fullscreen mode Exit fullscreen mode

将会起作用，因为我们在任何地方都使用对同一个函数的引用，所以当调用`removeEventListener`时，它会像`this.mouseenterHandler === this.mouseenterHandler`一样通过比较来找出要删除哪个函数，我们可以看到这是千真万确的。

现在的“问题”是我们实际的`mouseenterHandler`被一般化了——它将一个元素作为参数。这无疑是一个比为我们将要使用的每个元素编写一个新的`mouseenterHandler`函数更好的解决方案！但是现在我们必须以某种方式将参数放入其中，并且将对`mouseenterHandler`的调用包装在匿名函数中在这里是行不通的，正如我在上面详细说明的那样。

### 解决方案 1:为每个元素创建专门版本的事件处理程序

我们可以创建专门版本的`mouseenterHandler`，在我们填充`elements`之后，它已经包含了额外的参数。类似于:

```
let elements = document.querySelectorAll(/* ... */)
let enhancedElements = []

elements.forEach(element => {
  enhancedElements.push({
    element,
    mouseenterHandler() { mouseenterHandler(element) },
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，更改代码，将处理程序添加到

ⓐ

```
enhancedElements.forEach(ee => {
  ee.element.addEventListener('mouseenter', ee.mouseenterHandler)
}) 
```

Enter fullscreen mode Exit fullscreen mode

和移除，分别进入

ⓑ

```
enhancedElements.forEach(ee => {
  ee.element.removeEventListener('mouseenter', ee.mouseenterHandler)
}) 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但也会为每个元素创建一个额外的对象和函数，如果没有那么多元素，这可能不是问题，但仍然有一种更优雅的方式…

### 解决方案 2:改变我们的事件处理程序来处理他们已经得到的参数

浏览器将调用我们的事件处理程序，将事件作为第一个参数。事件只是一个具有许多属性的对象，其中一个属性是`event.target`，它是对发生事件的元素的引用。那么，为什么不改变我们的事件处理程序来使用它，这样我们就不需要手工把元素硬塞进去了？

例如，如果我们的 mouseenterHandler 看起来像这样:

```
mouseenterHandler(element) {
  element.classList.add(/* ... */)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以把它改成使用`event.target`来代替:

```
mouseenterHandler(event) {
  event.target.classList.add(/* ... */)
} 
```

Enter fullscreen mode Exit fullscreen mode

或者在参数列表中使用析构 right，这样我们就不用重复`event.`部分:

```
 mouseenterHandler({ target }) {
  target.classList.add(/* ... */)
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个解决方案，我们可以让`let elements = document.querySelectorAll(/* ... */)`线保持原样；不需要额外的对象或函数，我们只要把ⓐ改成:

```
elements.forEach(element => {
  element.addEventListener('mouseenter', mouseenterHandler)
}) 
```

Enter fullscreen mode Exit fullscreen mode

和ⓑ对号入座:

```
elements.forEach(element => {
  element.removeEventListener('mouseenter', mouseenterHandler)
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们的事件处理程序现在是“通用的”，可以不加修改地用于任何元素。

<small>*我撒谎了。`undefined`也是一种类型。</small>

* * *

感谢您的阅读！这是我在 dev.to 上的第一篇帖子，而且我的母语不是英语，所以关于风格、有用性等的一般反馈。不胜感激:)*