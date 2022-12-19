# x++和++x 的区别

> 原文：<https://dev.to/somedood/the-difference-between-x-and-x-44dl>

# 一个程序员的懒惰和实用

让我们面对现实吧。程序员拿着报酬在屏幕上输入一些魔法，最终变成有用的东西。由于一整天的工作大部分是阅读和打字，因此很自然地语法必须缩短以提高生产率和可读性...或者是因为打字累，为了多省几个按键。

这就是为什么我们有递增/递减运算符。

```
// Suppose we have a variable that stores a number
let someNum = 0;

// Practicality Level 1
someNum = someNum + 1;

// Practicality Level 2
someNum += 1;

// Practicality Level 9000+
someNum++;

// Wait... or should I use...?
++someNum; 
```

Enter fullscreen mode Exit fullscreen mode

啊，现在我们面对眼前的问题:什么是*`someNum++`和`++someNum`的区别？*

 *# 前缀与后缀

*   **前缀:** `++someNum`
*   **后缀:** `someNum++`

乍一看，这似乎是一种句法偏好；类似于[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)，你可以通过写`function* generator() {}`或`function *generator() {}`来定义一个。与直觉相反，两者在工作方式上有细微的差异，特别是在各自的回报上。

免责声明:在本文的其余部分，为了简洁起见，我将只使用增量运算符。从这里开始，应该暗示对于增量操作符为真的对于减量操作符也为真。

这两个操作符仍然像它们的语法所暗示的那样:递增。不管前缀或后缀是什么，变量肯定会增加 1。两者的区别在于它们的返回值。

*   **前缀**增量在增加后返回变量**的值。**
*   另一方面，更常用的**后缀**增量返回变量**在**被递增之前的值。

```
// Prefix increment
let prefix = 1;
console.log(++prefix); // 2
console.log(prefix); // 2

// Postfix increment
let postfix = 1;
console.log(postfix++); // 1
console.log(postfix); // 2 
```

Enter fullscreen mode Exit fullscreen mode

为了记住这个规则，我考虑了两者的语法。当一个人键入前缀增量时，他说`++x`。这里`++`的位置很重要。说`++x`就是先递增(`++`)*然后*返回`x`的值，于是就有了`++x`。后缀增量反过来工作。说`x++`就是先返回`x`的值*然后*递增(`++`)，这样`x++`。

# 什么时候用一个比另一个好？

这真的取决于你，程序员。在一天结束的时候，我们真正想从 increment 操作符中得到的是将变量增加 1。如果您仍然担心它们之间的差异，那么在某些情况下，可以使用其中一种来编写更简单的代码。例如，考虑以下情况。

ID 为`counter`的`button`计算其被按下的次数。它根据按钮被按下的次数改变 ID 为`displayPressCount`的`span`的`innerHTML`。常见的方法是附加一个 click 侦听器，该侦听器递增某个全局变量。

```
// Global variable that counts how many times the button has been pressed
let numberOfPresses = 0;

// Stores necessary HTML elements
const button = document.getElementById('counter');
const span = document.getElementById('displayPressCount');

// Event handler
function clickHandler() {
  // Increment counter
  numberOfPresses++;
  span.innerHTML = numberOfPresses;
}

// Attach click listener to button
button.addEventListener('click', clickHandler); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们把焦点转向`clickHandler`。注意增量操作符是如何在函数中占据一整行代码的？回忆前缀增量返回的内容可以帮助我们缩短函数。

```
// Event handler
function clickHandler() {
  // Increment counter
  span.innerHTML = ++numberOfPresses;
} 
```

Enter fullscreen mode Exit fullscreen mode

瞧吧！被缩短了！如果我们想变得更疯狂，我们甚至可以使用箭头函数。整个脚本现在看起来像这样。

```
// Global variable that counts how many times the button has been pressed
let numberOfPresses = 0;

// Stores necessary HTML elements
const button = document.getElementById('counter');
const span = document.getElementById('displayPressCount');

// Attach click listener to button
button.addEventListener('click',
  () => (span.innerHTML = ++numberOfPresses)
); 
```

Enter fullscreen mode Exit fullscreen mode

# 要记住的事情

前缀和后缀增量都将数字的值增加 1。两者之间唯一的区别是它们的返回值。前者先递增(`++`)，然后返回`x`的值，从而得到`++x`。后者先返回`x`的值，然后递增(`++`，从而`x++`。

现在去把你新发现的知识传播给世界吧！*