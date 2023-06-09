# Node.js:从 2009 年开始打破浏览器的 JavaScript

> 原文：<https://dev.to/somedood/nodejs-breaking-javascript-out-of-the-browser-since-2009-53cn>

# JavaScript 简史

JavaScript 一直是 Web 的编程(或者说脚本)语言。它与 1995 年首次推出时的卑微起点相去甚远。事实上，那时候它甚至还不叫 JavaScript。在其早期开发中，它最初被称为 Mocha，是 Java 上的一种文字游戏，Java 是当时 web 服务器最流行的编程语言之一。当它第一次在网络浏览器 [Netscape Navigator](https://en.wikipedia.org/wiki/Netscape_Navigator) 中发布时，它被改成了 LiveScript。最后后来改名为 JavaScript。以前很有限。它仅仅是一种脚本语言，允许普通的网页制作人`alert('Welcome to my website!')`一个访问者。

**注意:**有一篇由 Ben Ilegbodu 所做的[的精彩演讲，描述了那段时间的 Web 开发情况。](https://www.youtube.com/watch?v=6hyIZlaWVmU&t=29s)

多亏了 ECMAScript 规范对这种语言的改进，JavaScript 变成了今天的样子。它是网络的编程语言**。它是**浏览器的**编程语言。由于它的无处不在，它与 HTML 和 CSS 一起成为了网络的三大支柱之一。**

# node . js 的诞生

一切都很好，直到需要 JavaScript 在浏览器之外运行。于是在 2009 年，通过 Ryan Dahl 的倡议， [Node.js](https://nodejs.org/en/) 诞生了。

Node.js 是一个免费的、[开源的](https://github.com/nodejs/node) JavaScript 运行时。这是什么意思？这意味着它允许 JavaScript 在浏览器之外运行*和在任何平台运行*。用户编写的任何 JavaScript 代码都可以在任何安装了 Node.js 的系统上运行。它本质上是让网络的“跨平台”面向所有人。**

# 为什么是 Node.js？

Node.js 公开了在浏览器环境中不可用的 API。这些 API 对操作系统的限制较少。例如，可以使用[文件系统 API](https://nodejs.org/api/fs.html) 来访问和操作操作系统的文件系统。它为开发人员提供了对文件系统的基本 CRUD(创建、读取、更新和删除)操作。出于安全原因，这不能在浏览器中实现。基本上，JavaScript 因为 Node.js 而变得更强大(也更危险)

有时候，我们需要将外部代码包含到我们自己的代码中。例如，如果我们想使用 [jQuery](https://jquery.com/) ，我们插入一个导入其功能的`<script>`标签。我们自己写的普通脚本也是如此。我们可以简单地导入任何带有`<script>`标签的 JavaScript 文件。然而，这在更大的范围内会变得很麻烦。想象一下，必须手动管理数百个依赖项。那肯定会把人逼疯的！这就是 Node.js 的用武之地。Node.js 提供了一种管理依赖关系的方法，或者用 Node.js 行话来说就是“包”。一个[下载安装 Node.js](https://nodejs.org/en/download/current/) 时，附带 [Node.js 包管理器(NPM)](https://www.npmjs.com/) 。简而言之，通过 NPM 注册表，外部包可以作为依赖项导入到一个人的项目中。稍后将讨论更多相关内容。

所有这些特性都很棒，但是 Node.js 有什么用呢？

*   服务器
*   数据库管理
*   网络应用
*   桌面和移动应用
*   “物联网”应用
*   机器人学

# node . js 入门

人们可以下载两个版本的 Node.js。目前，选择哪个并不重要。如果有帮助的话，我自己用当前版本。

*   [长期支持版本](https://nodejs.org/en/download/)被认为是最稳定的。它只在必要时更新，包括错误修复和基本维护。该版本是生产环境中的首选版本。
*   当前版本相当稳定。它经常更新新功能。鉴于更快的更新周期，它有最现代的 API 和技术供开发人员试用。

完成安装过程后，可以通过打开命令提示符进行测试。如果安装成功，运行`node -v`会输出 Node.js 的安装版本。

现在我们知道 Node.js 已经安装好了，我们可以运行`node`命令来启动 JavaScript 解释器。此时，Node.js 允许您编写 JavaScript。它基本上是 DevTools *中的[控制台，但是在命令提示符](https://developers.google.com/web/tools/chrome-devtools/console/)*中。要退出解释器，按两次`Ctrl + C`或输入`.exit`。

我们可以在解释器中键入所有我们想要的 JavaScript，但是如果我们想要运行 JavaScript 文件呢？Node.js 也可以做到这一点。首先，我们确保我们在文件所在的目录中。我们可以通过运行`cd /path/to/the/file`直到我们在正确的目录中。在那里，我们可以输入`node <filename>`来运行文件。例如，我们键入`node filename.js`来运行`filename.js`。我们甚至可以通过键入`node filename`来省略`.js`文件扩展名。Node.js 暗示`filename`是一个 JavaScript 文件。

**注意:**node . js 中没有全局`window`对象这种东西，取而代之的是，全局对象不出所料的被称为`global`对象。

# NPM 入门

如前所述，NPM 提供了一种更简单的方法来管理依赖关系。假设我们想在代码中使用 [Lodash](https://lodash.com/) 。首先，我们通过在项目目录中运行 [`npm init`](https://docs.npmjs.com/cli/init) 来初始化我们的新项目。根据提示，我们可以填写有关该项目的必要信息。之后创建一个 [`package.json`](https://docs.npmjs.com/files/package.json) 文件。它存储了项目的所有元数据。

# 导入模块

然后，我们下载 Lodash。假设我们在正确的目录中，我们可以通过运行 [`npm install lodash`](https://docs.npmjs.com/cli/install) 来实现。依赖项被下载到一个名为`node_modules`的文件夹中。一旦我们在项目中引入更多的依赖项，这个文件夹就会积累大量的文件。是的，它有一个巨大的文件是正常的。下载完成后，`package.json`文件会保存项目所有依赖项的记录，这样你就不必担心了。

一旦安装了 Lodash，我们就可以在当前目录中创建一个使用 Lodash 的 JavaScript 文件。在这个文件中，我们可以通过调用 [`require`](https://nodejs.org/api/modules.html#modules_require) 函数来导入 Lodash。

```
// Imports the functionality of Lodash in a variable named '_'
const _ = require('lodash');

// We can now use it in the file
_.join(['Node.js', 'is', 'cool!'], '  '); // 'Node.js is cool!' 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，Node.js 提供了一个文件系统 API。它是 Node.js 的每个安装都附带的内置模块。我们也可以通过调用`require`函数来导入该模块。运行 JavaScript 文件将把`'Hello World'`写到一个名为`devto.txt`的文本文件中。

```
// Imports the 'fs' module (File System API) in a variable named 'fs'
const fs = require('fs');

// Writes to a file named 'devto.txt' in the current directory
fs.writeFile('devto.txt', 'Hello World', err => console.log(err)); 
```

Enter fullscreen mode Exit fullscreen mode

`fs.writeFile`方法只是`fs`模块提供的众多特性之一。在[文档](https://nodejs.org/api/fs.html)中描述了 API 的所有可用方法和属性。

# 导出模块

如果我们想从不同的 JavaScript 文件中导入我们自己的代码呢？首先，我们必须导出 JavaScript 文件的代码。然后，使用`require`函数，我们可以将导出的代码导入到另一个文件中。事实上，这并不像听起来那么令人困惑。假设我们在同一个目录中有四个 JavaScript 文件，分别名为`index.js`、`add.js`、`username.js`和`dog.js`。

`add.js`的工作是提供一个返回两个输入之和的函数。我们可以通过将`add`函数分配给 [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) 属性来导出这个功能。

```
// add.js
function add(a, b) {
  return a + b;
}

// Exporting the function
module.exports = add; 
```

Enter fullscreen mode Exit fullscreen mode

`username.js`的工作是导出字符串`'Some Dood'`。差不多就是这样。不多不少。这只是为了强调我们可以出口任何东西。我的意思是可以将*的任意*类型的任意值赋给`module.exports`。是的，这意味着我们可以导出字符串、数字、布尔值、符号、函数、类、数组和对象。如果我们想变得更疯狂，我们甚至可以出口`null`和`undefined`。但是现在，我们导出一个简单的字符串。

```
// username.js
// Exporting the string
module.exports = 'Some Dood'; 
```

Enter fullscreen mode Exit fullscreen mode

`dog.js`的工作是导出一个代表狗的对象。

```
// Exporting the object
module.exports = {
  name: 'Presto',
  breed: 'Siberian Husky',
  bark: function() {
    console.log('Bork!');
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以通过使用`require`函数将这三个文件导入到`index.js`中。我们只需要提供文件的(绝对或相对)路径，以明确告诉 Node.js 我们是从我们的文件导入的，而不是从内置模块或项目的`node_modules`文件夹。作为额外的奖励，我们还可以省略文件扩展名`.js`。

```
// index.js
// Don't forget to provide absolute or relative paths!
const add = require('./add');
const myUsername = require('./username');
const dog = require('./dog');

const someSum = add(3, 7);
const someOtherSum = add(1, 1);

dog.bark(); // 'Bork!'
console.log(`${dog.name} is a ${dog.breed}.`); // 'Presto is a Siberian Husky.'
console.log(someSum); // 10
console.log(someOtherSum); // 2
console.log(myUsername); // 'Some Dood' 
```

Enter fullscreen mode Exit fullscreen mode

`require`函数返回的是分配给每个文件各自的`module.exports`的实际值。很有用，是吧？在底层，内置模块和外部依赖项也是如此。

# 结论

Node.js 的模块系统使得将代码分离成单独的模块成为可能。如果操作正确，这些单一用途的模块可以在代码库的许多地方重用，这减少了重写代码的需要。这鼓励了更容易维护和进行单元测试的模块化设计。

那么我们学到了什么？

*   Node.js 允许 JavaScript 在浏览器之外运行。
*   为了帮助管理外部依赖性，安装附带了 NPM。除了存储项目的元数据，`package.json`文件还跟踪这些依赖关系。
*   代码可以分为可导入模块和可导出模块。

还有很多要学的。这篇文章仅仅是对 Node.js 的一个介绍，互联网上有很多资源可以帮助你了解 Node.js。在 dev.to 中，我们有一个特定的[标签](https://dev.to/t/node)专门用于与 Node.js、[、Google、](https://www.google.com/)[、YouTube、](https://www.youtube.com/)、[堆栈溢出](https://stackoverflow.com/questions/tagged/node.js.)相关的所有事情，并且这篇文章的评论部分将随时供您提问。最后，Node.js 的[官方文档是关于该软件一切的最终参考。它甚至记录了 Node.js 的不同版本。](https://nodejs.org/api/index.html)

在我结束这篇文章之前，我对所有新来者有一个关于 Node.js 的最后警告:*小心野兽，那就是`node_modules`文件夹*。~~点头愉快！~~编码快乐！