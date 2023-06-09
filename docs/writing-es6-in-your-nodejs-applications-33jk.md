# 在 Node.js 应用程序中编写 ES6

> 原文：<https://dev.to/dhruv/writing-es6-in-your-nodejs-applications-33jk>

ES6 (ECMAScript 2015)是 JavaScript 的最新稳定版本。它包括新的语言语法和语言的实现。三年后，这些新特性在 JavaScript 引擎中的实现仍在进行中，但你仍然想在 ES6 中编写代码，因为谁想留在这个行业中。我们将在这里使用[巴别塔](https://babeljs.io/)来转换我们的 ES6 代码，使其能够被现有的 Javascript 引擎理解。Babel 是一个编译器，允许我们用 JavaScript 编写 ES6 特性，并在旧的/现有的引擎中运行。
如何使用 Node.js 应用程序设置 Babel

*   您的计算机上应该已经安装并运行了最新的 node.js。
*   创建一个新项目或者运行并移动到这个目录

    `$ mkdir new_project
    $ cd new_project`

*   创建一个文件`app.js`，在里面写一些 ES6 代码

    `import request from 'requests';
    let helloWorld = 'Hello World!`;
    console.log(`${helloWorld} this is some ES6 JavaScript code`);`

*   通过运行`npm init`创建一个`package.json`文件。输入所需的详细信息，或者对您不认识的字段按 return 键。此时你的`package.json`应该是这样的

T2`{
"name": "es6project",
"version": "1.0.0",
"description": "using babel with node",
"main": "app.js",
"scripts": {
"test": "echo \"Error: no test specified\" && exit 1"
},
"author": "",
"license": "ISC"
}`

*   安装巴别塔和巴别塔预置作为一个开发依赖

T2`$ npm install -D babel-cli
$ npm install -D babel-preset-es2015`

*   Babel 使用不同的[插件](https://babeljs.io/docs/en/plugins/)来实现不同的功能。在我们的例子中，我们可以使用 es2015 插件。我们需要创建一个`.babelrc`配置文件。

T2`$ touch .babelrc`

并将以下内容粘贴到文件中

T2`{
"presets": ["es2015"]
}`

*   创建一个 npm 构建命令来编译您的 ES6 Javascript。用以下命令修改您的`package.json`

T2`"scripts": {
"test": "echo \"Error: no test specified\" && exit 1",
"build": "babel --presets es2015 -d lib/ src"
}`

这会将 src 目录中的所有 ES6 代码编译到 lib 目录中。你应该看到你的代码编译在 lib 目录中，现在你可以运行它了。这段代码存在于这个 Github [库](https://github.com/nendhruv/es6_with_babel)中。

这篇文章最初发表在[媒体](https://medium.com/@nendhruv/writing-es6-in-your-node-js-applications-8aba8170761d)上