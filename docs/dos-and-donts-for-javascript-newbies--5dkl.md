# Javascript 新手的注意事项

> 原文：<https://dev.to/jsguru_io/dos-and-donts-for-javascript-newbies--5dkl>

自从 Brendan Eich 创建 JS 的那一天起，JS 就经历了许多改进、变化、添加和框架。在经历了一生的动荡之后，JS 已经被塑造成我们现在在 2018 年所看到的样子，但未来还有更多增长等待着它。

今天，我认为用 Atwood 的名言来描述 JavaScript 的现状是最好的:“任何可以用 JavaScript 编写的应用程序最终都会用 JavaScript 编写。”事实上，你能想到的任何东西都可以用 JavaScript 编写。

在这篇文章中，我们会给你一些建议，告诉你应该做什么，不应该做什么，不管是对于 JS 新手还是之前有过 JS 经验的人。

在编写 JavaScript 代码时，有一些通用的经验法则应该时刻牢记在心。这些规则涉及变量声明、命名约定、代码注释、努力编写更简洁的代码以及跟上 JavaScript 世界的总体发展。让我们解决其中的一些问题。

### 变量

在命名变量时，使用 camelCase 规则通常被认为是最佳实践。这就是我们在 JSGuru 对它们的命名，当整个团队使用这个规则时，它会有所帮助，因为它有助于保持代码的统一。

保持变量名的简短和描述性也很重要。由于代码大部分时间都是共享的，所以应该尽可能地坚持这一点。读者应该能够判断出该变量中存储了什么或者它引用了什么，而无需将它记录在控制台中并回溯您的代码。一个好的变量名应该告诉读者它在一段代码中使用的上下文，而不是从用户的角度来看它的值或用途。例如，“userAnswer”是一个比“userInput”更好的变量名，因为从编码的角度来看，它清楚地提到了前面提出的一个问题。你确切地知道它指的是什么输入。同样，避免使用通用名称，如“num”和“arr ”,至少不要在名称后面附加与其所指内容相关的信息，例如“selectedColors”。同样，“想要订阅”或“应该记住”比“真实或虚假”更好。使用动词和复数/单数来帮助表示值，而不要使用与名称中值的类型相关的缩写。

让代码更整洁、更易读被认为是一个好的实践。要做到这一点，可以将变量声明放在脚本的开头，在列表中的第一个变量前面添加 var 或 let，并且只添加第一个变量。逗号可以将其余部分分开，为了完成交易，在声明的末尾加上一个分号。当你声明变量时，先初始化它们，这样我们可以避免未定义的值，然后再做其他的事情。

### 用 LET 或 CONST 代替 VAR

自从采用 ES6(也称为 ECMAScript 2015)标准以来，应该使用关键字 let 和 const 来声明变量。放弃 var 关键字的原因是，它应该为变量的用途和使用它的上下文提供更清晰的含义。Const 通常应该保存对不会随时间改变的值的引用，即使在对象和数组的情况下，允许对它们进行变异。另一方面，关键字 let 表示一个值可能被改变，或者一个不同的值将被赋给特定的变量。如果你试图改变一个常量的值，JavaScript 会告诉你并帮助你避免错误。const 的一个很好的用例是存储一个对 DOM 元素的引用，您总是希望将它保存在该变量中。关键字 let 意味着与循环或数学算法一起使用，通常当它的值预计会变化时。用 let 和 const 声明的变量不会像用 var 声明的那样被提升。

[![Body image - comments](../Images/75dede034e9191f3030324bfabd80fce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q1woENSp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j4hrgtmytrifgf6c6vwh.png)

### 注释

您是否曾经发现自己处于这样一种情况，当您查看您的旧代码时，却发现没有与之相关的注释？也许你当时忘了写它们，或者你不小心推迟了写它们，结果后来忘了写。不管是什么情况，现在你正处于这样一种情况，你看着一堆象形文字，开始感到不知所措，因为你不知道从哪里开始阅读和理解它。编写干净的代码并遵循良好的命名约定肯定会有所帮助，但是一段更复杂的代码有时只需要一两条注释就可以帮助读者更快地理解它。我记得在很多情况下回到我的代码，花了很多时间弄清楚我写了什么，以及我到底是怎么做的。这时候我知道了在评论中写一些逻辑的重要性，只是为了作为笔记，帮助我在将来更快地理解它。几乎毫无疑问，您会发现自己正处于这样一种情况，即您试图理解您或其他人编写的代码，并希望有一些关于它的评论，以便加快进度。

把这个经历作为一个动力，帮助你理解写评论的重要性，下次写一些复杂的逻辑时牢记在心。只要写一个简短的句子抓住这一大块的精髓，相信我，你会在未来感谢你自己。更重要的是，任何阅读你的代码的人都会心存感激。顺便说一句，让你的评论变得幽默和积极并没有坏处，因为消极和傲慢会适得其反。

### 格式化代码

格式化代码有时会很棘手。为了帮助你，你应该试试像 ESLint 或 JSLint 这样的代码链接(链接到官方网站)。这两个工具都将帮助您拥有符合社区标准的更干净、更好的代码。您至少可以使用空白和换行符将代码分组到相关的块中。这将使你的代码更具可读性，你也能更快地理解它！

### 效率

在这一节中，我们将提醒您在编程中一般效率的重要性。谈到 Javascript，这些是一些常见的新手陷阱。

#### 1。获取 DOM 元素

如果我每次看到分散在代码中的 document.getElementById 都能得到一美元，我现在已经是百万富翁了。如果 DOM 元素实际上没有改变，就把它存储在一个变量中，以后再使用它。

```
 let container = document.getElementById("someElementId"); 
        container.innerHTML = "<h1>Hello World!</h1>";
        container.addEventListener('mouseover', function(e) {
            this.innerHTML = "<h1>Hello Again!</h1>";
        }) 
```

Enter fullscreen mode Exit fullscreen mode

这在 jQuery 中尤其常见，我们都见过这样的代码:

```
 $('#button').addClass('actioned');
    $('#button').hover(function () { ... });
    $('#button').click(function () { ... }); 
```

Enter fullscreen mode Exit fullscreen mode

而不是:

```
let button = $('#button');
    button.addClass('actioned');
    button.hover(function () { ... });
    button.click(function () { ... }); 
```

Enter fullscreen mode Exit fullscreen mode

您还应该记住的是，通过 Id 获取 DOM 元素是最快的方法，因此您应该使用它而不是其他方法，如 document . getelementsbyclassname、document . getelementsbytagname document . query selector 等。只要有可能。

#### 2。循环中的 DOM 操作

这是一个不要做什么的例子。这里我们从循环中取出一个 DOM 元素。这意味着我们不必在每次迭代中获取它，然后在每次迭代中填充它的内部 HTML。

```
function processArray(myArray) {
    for (let i = 0; i < myArray.length; i++){
      let div = document.getElementById("container");
      div.innerHTML = div.innerHTML + myArray[i];
      if (i < myArray.length - 1) {
        div.innerHTML = div.innerHTML + ", ";
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

优化这段代码的第一件事是将 fetch 语句移到循环之上。通过这样做，我们不会改变这个代码块的逻辑，但会显著提高代码的速度，同时还会减少内存的使用。为了避免每次迭代都要不断更新 DOM，因为这相当耗时，将 innerHTML 移出循环也是一个好主意。

```
 let container = document.getElementById("someElementId"); 
        container.innerHTML = "<h1>Hello World!</h1>";
        container.addEventListener('mouseover', function(e) {
            this.innerHTML = "<h1>Hello Again!</h1>";
        }) 
```

Enter fullscreen mode Exit fullscreen mode

当我们谈论代码的效率时，这些例子帮助我们记住两件事。首先，在循环外声明变量，其次，减少 DOM 操作，并确保明智地使用它们。

此外，在创建新变量时，一定要记住使用 let more then var。

但是，用 let 定义的全局变量不会像用 var 定义的那样作为属性添加到全局窗口对象中。

### 严格模式

当我们的目标是创建更健壮的 JavaScript 代码时，我们被鼓励使用“严格模式”。严格模式将(以前接受的)“错误语法”更改为真正的错误。这意味着试图给一个错误类型的变量的属性添加值，这将在常规模式下创建一个新的全局变量，现在会给你一个错误。在严格模式下，任何带有不可写属性、仅 getter 属性、不存在的属性、不存在的变量或不存在的对象的赋值都将引发错误。

跟上最新的 JavaScript 标准可能是这个列表中最重要的事情。

首先，您的代码将是现代的，并且很可能接近当时的行业标准。此外，通过使用最新的功能，您和所有其他开发人员都在鼓励和创造对浏览器的需求，以实现这些功能，并开始支持开箱即用。现在，这是在诸如 Babel 的 transpilation 工具的帮助下完成的。如果你不熟悉 Babel，简单地说，它将最新的 JavaScript 代码“翻译”成当今浏览器可以理解的格式。Babel 读取您的 JavaScript 代码，并编译您使用的最新特性，直到所有浏览器都能理解的 ES5。一些浏览器已经支持 ES6 特性，但是使用 Babel 和类似的工具仍然是必要的，因为我们希望我们的 JavaScript 能被所有浏览器和旧版本支持。

关于巴别塔的更多信息，我建议你访问他们的网站，他们有很棒的文档，可以让你快速入门。

更重要的是，你会让你的生活更轻松！最新的 JavaScript 特性令人惊叹，并且随着每个规范的发展，它们变得越来越好。它们是对旧的做事方式的改进，即使用承诺或 Async/Await 来避免陷入回调金字塔中。

学习新事物意味着离开你的舒适区，但是相信我，一旦你学会了，你就再也不会回头了。我推荐研究的几个特性是数组方法(map、reduce、filter)、async/await，当然还有我最喜欢的字符串文字。

重要的是要记住，你总是可以提高你的编码技能，写出更干净的代码。这是一场马拉松，而不是短跑，所以如果你的代码不够干净，不要感到不知所措。最重要的是管用！随着时间的推移，随着您变得更有经验并开始遵守行业标准，您将开始编写更干净的代码，即使这样，也将有大量的改进空间，就像任何事情都有改进空间一样！所以不要气馁，只是需要时间。

我希望这篇文章对你有所帮助。直到下一次，贾云馨，签出...