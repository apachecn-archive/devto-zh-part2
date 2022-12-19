# 节点，接受来自命令行的输入

> 原文：<https://dev.to/flaviocopes/node-accept-input-from-the-command-line-1h6>

如何让 Node.js CLI 程序具有交互性？

从版本 7 开始，Node 提供了 [`readline`模块](https://nodejs.org/api/readline.html)来执行这一任务:从可读流(如`process.stdin`流)中获取输入，在 Node 程序执行期间，这是终端输入，一次一行。

```
const readline = require('readline').createInterface({
  input: process.stdin,
  output: process.stdout
})

readline.question(`What's your name?`, (name) => {
  console.log(`Hi ${name}!`)
  readline.close()
}) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码询问用户名，一旦输入文本，用户按 enter，我们就发送一个问候。

`question()`方法显示第一个参数(一个问题)并等待用户输入。一旦按下回车键，它就调用回调函数。

在这个回调函数中，我们关闭了`readline`接口。

提供了几个其他的方法，我会让你在我上面链接的包文档中查看它们。

如果你需要一个密码，最好现在回显它，而不是显示一个`*`符号。

最简单的方法是使用 [`readline-sync`包](https://www.npmjs.com/package/readline-sync)，它在 API 方面非常相似，并且开箱即用。

更完整抽象的解决方案由 [Inquirer.js 包](https://github.com/SBoudrias/Inquirer.js)提供。

可以用`npm install inquirer`安装，然后可以这样复制上面的代码:

```
const inquirer = require('inquirer')

var questions = [{
  type: 'input',
  name: 'name',
  message: "What's your name?",
}]

inquirer.prompt(questions).then(answers => {
  console.log(`Hi ${answers['name']}!`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

Inquirer.js 允许你做很多事情，比如询问多项选择、单选按钮、确认等等。

了解所有的替代方法是值得的，尤其是 Node 提供的内置方法，但是如果您计划将 CLI 输入提升到一个新的水平，Inquirer.js 是一个最佳选择。