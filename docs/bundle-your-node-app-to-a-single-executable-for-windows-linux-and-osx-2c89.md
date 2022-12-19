# 将你的节点应用捆绑到一个可执行文件中，供 Windows、Linux 和 OsX 使用

> 原文：<https://dev.to/jochemstoel/bundle-your-node-app-to-a-single-executable-for-windows-linux-and-osx-2c89>

我被很多人问了很多次的一个问题是如何将一个节点应用程序编译成一个可执行文件。我很惊讶，因为这其实很简单。

## 问原因

*   *保护源代码不被修改或复制* -你不能在一个简单的文本编辑器中打开可执行文件。
*   *隐藏 API 凭证* -与保护源代码的区别相同。
*   *交付到没有节点或 NPM 的系统* -无需 NPM 安装依赖项，将所有内容捆绑在一个可执行文件中。
*   *规定节点版本*——强制某个版本的节点保证特性支持。
*   *防止商业应用被清空* -不再像注释掉、替换或删除许可验证功能那样简单。
*   *提高性能* -这不是一个有效的理由。捆绑的可执行文件并没有表现得更好，因为它包含了一个完整的节点，所以它比 13kb 的 JavaScript 要大得多(22MB)。
*   向朋友炫耀——我们有时都会这样做。
*   从总体上了解——对事物如何在幕后运作有普遍兴趣的人。我最喜欢的理由。
*   看到证明我可以的证据了吗？

有一些工具可以做几乎相同的事情。在这篇文章中，我将重点介绍如何使用 [pkg](https://www.npmjs.com/package/pkg) ,因为它是免费的(开源的),也是我迄今为止最愉快的工作体验。

## PKG

PKG 是一个命令行工具，可以简化应用程序的构建过程。通过运行 *npm i pkg -g* 来全局安装它。您也可以通过编程来使用它，但我们将会谈到这一点。

## 示例节点 app 'prettyprint.exe '

我们将创建一个节点应用程序，打开一个. json 输入文件，添加缩进(制表符，空格)和控制台日志的美化更可读的 JSON。我将详细描述这个过程，并创建这些文件的 git。

### [T1】NPM init/package . JSON](#npm-init-packagejson)

用 *package.json* 创建新节点应用程序的一个简单方法是在一个空目录中运行 *npm init* 。

```
{  "name":  "prettyprint",  "version":  "0.0.1",  "description":  "Pretty print a JSON file.",  "main":  "main.js",  "author":  "anybody",  "license":  "MIT"  } 
```

Enter fullscreen mode Exit fullscreen mode

### 模块，导出我们的函数

为了绝对简单起见，让我们假设 *main.js* 包含一个如下所示的函数:

```
/* You might want to check first if the file exists and stuff but this is an example. */
const fs = require('fs')
module.exports = function(filePath) {
    let data = fs.readFileSync(filePath).toString() /* open the file as string */
    let object = JSON.parse(data) /* parse the string to object */
    return JSON.stringify(object, false, 3) /* use 3 spaces of indentation */
} 
```

Enter fullscreen mode Exit fullscreen mode

> 是的， [@joelnet](https://dev.to/joelnet) 。我们都知道你更喜欢这样写。谢谢你。

```
module.exports = filePath => JSON.stringify(JSON.parse(require('fs').readFileSync(filePath).toString()), false, 3) 
```

Enter fullscreen mode Exit fullscreen mode

创建一个 *bin.js* 文件。

```
const prettyprint = require('.') /* the current working directory so that means main.js because of package.json */
let theFile = process.argv[2] /* what the user enters as first argument */

console.log(
    prettyprint(theFile)
) 
```

Enter fullscreen mode Exit fullscreen mode

> 是的， [@joelnet](https://dev.to/joelnet) 。它是这样短的:

```
console.log(require('.')(process.argv[2])) 
```

Enter fullscreen mode Exit fullscreen mode

### 一个虚拟的 JSON 文件来测试是否一切正常

或者使用自己的 JSON 文件。

```
{"user":{"name":"jochem","email":"jochemstoel@gmail.com"}} 
```

Enter fullscreen mode Exit fullscreen mode

### 测试复制/粘贴是否正确

如果你运行*节点 bin.js file.json* ，你会看到这个:

```
{  "user":  {  "name":  "jochem",  "email":  "jochemstoel@gmail.com"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 向 package.json 添加一个属性

简单地添加一个属性 *"bin"* ，值为 *"bin.js"* 到你的包 json 中，如下:

```
{  "name":  "prettyprint",  "version":  "0.0.1",  "description":  "Pretty print a JSON file.",  "main":  "main.js",  "bin":  "bin.js",  "author":  "anybody",  "license":  "MIT"  } 
```

Enter fullscreen mode Exit fullscreen mode

### 运行 pkg

运行 *pkg。*从你的 app 目录中构建一个可执行文件。
通过不提供目标，它将为所有三个平台构建。Windows、Linux 和 OSX。

```
pkg .
> pkg@4.3.4
> Targets not specified. Assuming:
  node10-linux-x64, node10-macos-x64, node10-win-x64 
```

Enter fullscreen mode Exit fullscreen mode

### 搞定！

瞧。将会创建 3 个新文件。

```
prettyprint-win.exe
prettyprint-linux
prettyprint-macos 
```

Enter fullscreen mode Exit fullscreen mode

要查看应用程序的运行情况，运行*prettyprint-win.exe 文件. json* 。在 Linux 上，chmod 你的二进制文件 *a+x* 使其可执行，然后运行*。/prettyprint-linux file.json* 。不了解 MacOS。

### 额外

我在任何地方都挤不进相关的东西。

#### 针对当前平台和版本的简单构建方式

从你的应用文件夹中，运行 *pkg -t host。*。 *-t* 表示目标平台，值 *host* 表示您的系统。*。*表示当前目录。
显然，您可以运行 *pkg - help* 来获得参数的完整列表。

#### 在 package.json 中，“main”和“bin”不必不同

虽然您通常希望将它们分开，但是 *main* 和 *bin* 可以具有相同的值，并且不一定需要是两个单独的文件。

#### 依赖项需要在 package.json 中

如果在创建应用程序后进行 NPM 安装，它会自动将依赖项添加到 package.json 中。

#### 原生模块和资产

要在可执行文件中包含资产文件/目录和/或构建依赖于本地节点模块的节点应用程序，请阅读[文档](https://www.npmjs.com/package/pkg)。

#### ...

我们在本教程中所做的一切并不是绝对必要的。你不需要整个 package.json 带有一个“bin”属性和所有这些东西。这只是普通的做法，有助于你学习。您也可以只构建一个 JavaScript 文件。

##### PKG API

在这个例子中，我使用 PKG API 来构建一个 JavaScript 文件，而不需要整个工作目录或包

```
/* js2exe.js */
const { exec } = require('pkg')
exec([ process.argv[2], '--target', 'host', '--output', 'app.exe' ]).then(function() {
    console.log('Done!')
}).catch(function(error) {
    console.error(error)
}) 
```

Enter fullscreen mode Exit fullscreen mode

> 是的， [@joelnet](https://dev.to/joelnet) 。它是这样短的:

```
require('pkg').exec([ process.argv[2], '--target', 'host', '--output', 'app.exe' ]).then(console.log).catch(console.error) 
```

Enter fullscreen mode Exit fullscreen mode

##### 运行

```
node js2exe.js "file.js" 
```

Enter fullscreen mode Exit fullscreen mode

##### 制作自己的独立编译器可执行文件

您甚至可以让它自己构建，从而产生一个可以自己构建自己和任何其他 JavaScript 的可执行文件。独立的编译器。

```
node js2exe.js js2exe.js 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以使用你的输出可执行文件*app.exe*作为一个独立的编译器，不再需要节点或 NPM。

```
app.exe myfile.js 
```

Enter fullscreen mode Exit fullscreen mode