# 你知道浏览器内置的 javascript 压缩器吗？

> 原文：<https://dev.to/ahmedmusallam/do-you-know-of-an-in-browser-javascript-compressor-3a5a>

> 我希望这个帖子在这里是合适的:)

我在寻找浏览器内 javascript 压缩器的建议。我不需要混淆/丑化(虽然，这是受欢迎的)。我需要一个 javascript 库，它可以接受一串 Javascript 代码，这些代码可以用新的行、空格、制表符进行格式化..等等。并输出一行 javascript 代码。

> 尝试在 web 和 github 上查找，但我找到的大多数库都依赖于 nodeJS API。

例如:

```
function sayHi(){
  console.log('Hi!');
}

sayHi(); 
```

Enter fullscreen mode Exit fullscreen mode

将输出为:

```
function sayHi(){console.log("hi there")}sayHi(); 
```

Enter fullscreen mode Exit fullscreen mode

我需要一行 JS 作为我将要开始工作的开源 chrome 扩展的一个非常具体的用例。感谢您的帮助！

> 写完这篇文章后，我意识到我可以用一个正则表达式删除 new line 的所有实例。但是也许外面有更好的东西？