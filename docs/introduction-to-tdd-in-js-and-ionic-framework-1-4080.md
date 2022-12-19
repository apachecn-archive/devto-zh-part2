# JS 和 Ionic framework 1 中的 TDD 介绍

> 原文：<https://dev.to/nikola/introduction-to-tdd-in-js-and-ionic-framework-1-4080>

[![](../Images/c9bc09b145ae7d2190df5b98adb4de12.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--krcvC_Hx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fve6sjwma42pyhgs3vzv.png)

*最初发布于[我的博客](http://www.nikola-breznjak.com/blog/javascript/ionic/introduction-tdd-ionic-framework/)T3】*

## TL；速度三角形定位法(dead reckoning)

在这篇相当长的文章中，我将向您介绍 Ionic 中的测试驱动开发。首先，我将介绍一些基本的理论概念，然后我们将看看如何将这些应用到几个例子中。首先是普通的 JavaScript，然后是 Ionic。

在本教程结束时，您将会有一条清晰的道路，知道如何开始在您的 JavaScript 和 Ionic 应用程序中实践 TDD。此外，在底部，你会看到一个完整的“资源转储”,包含了我自己在尝试学习 TDD 时所经历的所有资源。

*演示幻灯片，如果有人感兴趣，可以在[这里](http://www.nikola-breznjak.com/portfolio/IntroToTddInIonic.pdf)观看。*

## 让我们来回答一些棘手的问题

有多少人真正测试过你的代码？不用担心；这是个反问句，不用举手。

好吧，如果我们在这里诚实，在我的情况下(因为我最近主要写 JavaScript)，直到最近我一直在练习所谓的 **CLTDD** 。当然，它代表**控制台. log TDD** 。

我们都知道我们应该做点什么来改善现状，但是我们经常像这位先生那样做:

[![](../Images/356fb9c45e649135de6b98d039defc93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F57an7Ei--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/zu1Wx05m.png)

好了，玩笑归玩笑，让我试着强调一下为什么测试实际上对你有用。只需思考以下问题:

*   你是否曾经修复了一个 bug，却发现它破坏了系统的另一部分？
*   你有没有因为担心自己可能会出错而害怕接触一段复杂的代码？
*   你有没有发现一段你很确定不再被使用并且应该被删除的代码，但是你把它留在那里*以防万一*？

[![](../Images/8ae67822f850865396fc011e67d52235.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mwVLis8K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/dDh41WOm.jpg)

好吧，如果这些问题的答案是肯定的，那么你将会看到 TDD 的价值，如果实践正确的话。

## 什么是 TDD？

因为我们这里的大多数人都是开发人员，我打赌你听说过**单元测试**。然而，单元测试和 TDD 不是一回事。单元测试是一种**类型的测试**。TDD 是一种**编码技术**。也就是说，如果你写单元测试，你实际上并没有进行 TDD。

> TDD 是一种编写软件的方法，在这种方法中，你在编写应用程序代码之前先编写测试。基本步骤是:

*   红色 -写一个测试，确保它失败
*   **绿色** -编写尽可能简单的代码来通过测试
*   **重构**——简化/重构应用程序代码，确保所有的测试仍然通过

[![](../Images/91b4bbd6e13603e34f68b3173aaea154.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vZRWyuOZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/dJKgmChm.jpg)

此时你可能会想:

> 等等，现在我还得写代码来测试我还没写的代码？！"

是的，你写了更多的代码，但是[研究已经客观地表明](http://evidencebasedse.com/?q=node/78)**使用 TDD 的良好测试覆盖可以减少 40% - 80%的 bug 密度**。

## 为什么要为考试而烦恼？

那么，为什么首先要测试您的代码呢？截止日期快到了，现在应该把宝贵的时间花在编写测试上，而不是实际的应用程序代码，这还不够吗？

随着功能和代码库的增长，手动 QA 变得更加昂贵、耗时并且容易出错。

比方说，如果你从代码中删除了某个函数，你还记得它所有潜在的副作用吗？大概不会。但是有了单元测试，你甚至不需要这么做。如果你删除了其他地方的需求，那么单元测试就会失败，你就会知道你做错了什么。

所以基本上，我们测试我们的代码来验证它是否如我们所期望的那样运行。作为这一过程的结果，您会发现您为自己和其他开发人员提供了更好的特性文档。

此外，正如詹姆斯·辛克莱尔认为的那样，实践 TDD 迫使一个人去思考，因为你必须先思考，然后再写测试。此外，它使调试更容易，编程更有趣。

## 关于 TDD 的 5 个常见误解&单元测试

基于 Eric Elliot 的观点，关于 TDD 和单元测试有 5 个常见的误解。

*   TDD 太费时间了
*   在你知道设计之前，你不能写测试，在你实现代码之前，你不能知道设计
*   您必须在开始编写代码之前编写所有的测试
*   红绿，永远重构？
*   一切都需要单元测试

此外，他对 TDD 中的嘲讽持有相当强烈的观点:

> 这里有一个将改变你生活的建议:`Mocking is a code smell.`

## 演示时间

好了，理论讲够了，现在让我们看一些代码！

[![](../Images/87229f0b69e241c21e6132d1d8ddf42f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wgHiJGq_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/PxpH624m.jpg)

### 先决条件

为了能够遵循这个教程，你需要安装 [Node.js](https://nodejs.org/en/) 。此外，通过 **npm** ，您需要在全球范围内安装以下软件包:

*   [因果报应](https://karma-runner.github.io/1.0/index.html)
*   [茉莉](http://jasmine.github.io/)
*   [幻象](http://phantomjs.org/)

我选择 Karma 作为运行测试的环境，选择 Jasmine 作为实际测试用例的环境，因为这些框架对我来说是最可靠的，而且似乎被广泛使用。但是，请记住，还有许多其他选择。很少值得一提的是[摩卡](https://mochajs.org/)、[柴](http://chaijs.com/)、[兴农](http://sinonjs.org/)、[带子](https://github.com/substack/tape)等。

[![](../Images/6cdc8e73efd3df5b48337b962832851e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_MyxHXlY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/CseGOCbm.png)

我想在这里补充的是，现在(尤其是在 JavaScript 世界)你有大量的选择。选择一个选项并真正开始比无休止地权衡这些选项要好得多。

对于 Jasmine，我们将使用所谓的`Behaviour Driven Development (BDD)`风格来编写测试。这是 TDD 的一个变体，其中测试以如下形式编写:

*   描述`[thing]`
*   它应该`[do something]`

`[thing]`可以是一个模块、类或函数。Jasmine 包括像`describe()`和`it()`这样的内置函数，使这种风格的写作成为可能。此外，Jasmine 还提供了其他一些很酷的东西，比如间谍，我们不会在这里讨论，但是你可以从[官方文档](http://jasmine.github.io/2.4/introduction.html)中了解更多。

## JavaScript 演示

在这个演示中，我将向您展示一个简单的逐步 TDD 方法来构建一个简单的计算器库。这将是一个简单的文件，只有两个函数(`add`和`sub`)。这没什么稀奇的；这只是为了说明这个过程会如何进行。

### 文件夹结构和依赖关系

让我们首先创建一个名为`jstdd`的新文件夹，并在其中创建一个文件夹`app`:

`mkdir jstdd && cd jstdd && mkdir app && cd app`

另外，在`app`文件夹中创建一个`index.js`文件:

`touch index.js`

接下来，执行`jstdd`目录中的`npm init`。这将为我们创建一个`package.json`文件，所有其他的依赖项(我们将很快安装)将被保存到这个文件中。对于`npm init`命令中的每个问题，您可以通过保留默认值来安全地按下`ENTER`。

接下来，安装所有需要的依赖项:

`npm install karma karma-jasmine jasmine-core karma-phantomjs-launcher --save-dev`

对于那些不太熟悉 Node 和 npm 的人来说，使用`--save-dev`开关，我们将这些依赖关系保存到我们的`package.json`文件中，该文件是使用前面提到的`npm init`命令创建的。

接下来，创建一个名为`tests`的新文件夹，并在其中创建一个文件`index.spec.js`:

`mkdir tests && cd tests && touch index.spec.js`

### 建立因果报应

基本上，我们现在一切都准备好了。但是，在我们真正开始编写测试之前，我们必须配置 Karma。因此，在我们的应用程序的根目录(文件夹`jstdd`)中，我们必须执行

`karma init`

问题的答案应该是:

*   使用 Jasmine 作为测试框架
*   不要使用 Require.js
*   使用 PhantomJS 而不是 Chrome(使用键盘上的`TAB`键在选项之间切换)。这是因为我们想在控制台中运行我们的测试
*   当要求源文件和测试文件时，使用`app/*.js`和`tests/*.spec.js`。我们可以使用 glob 模式，这意味着星号(*)匹配任何东西
*   当询问要排除哪些文件时，按`ENTER`跳过即可
*   最后，选择`yes`让 Karma 查看所有文件并运行变更测试

随着这个过程的完成，Karma 生成了`karma.conf.js`文件，它(没有注释)看起来应该是这样的:

```
module.exports = function(config) {
    config.set({
        basePath: '',
        frameworks: ['jasmine'],

        files: [
            'app/*.js',
            'tests/*.spec.js'
        ],

        exclude: [],
        preprocessors: {},
        reporters: ['spec'],

        port: 9876,
        colors: true,
        logLevel: config.LOG_INFO,

        autoWatch: true,
        browsers: ['PhantomJS'],
        singleRun: false,

        concurrency: Infinity
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 最后让我们写一些测试

至此，我们已经设置好了一切，可以开始编写测试了。我们将在`index.spec.js`文件中编写我们的测试。

提醒您，我们的目标是创建一个简单的计算器库。所以，我们从写一个测试开始。

当我们使用 Jasmine 测试我们的代码时，我们用 Jasmine 所谓的`test suite`将我们的测试分组在一起。我们通过调用 Jasmine 的全局`describe`函数开始我们的测试套件。

所以我们要写(在`index.spec.js`文件中):

```
describe ("Calculator", function (){

}); 
```

Enter fullscreen mode Exit fullscreen mode

这个函数有两个参数:一个字符串和一个函数。字符串作为标题，函数是实现测试的代码。

在这个描述块中，我们将添加所谓的**规格**。在我们的`it`块中，我们放置测试代码的期望。

例如，我们要测试的第一件事是我们确实有一个`add`函数:

```
it('should have an add function', function() {
    expect(add).toBeDefined();
}); 
```

Enter fullscreen mode Exit fullscreen mode

不要担心语法；这可以通过浏览 [Jasmine 的文档](http://jasmine.github.io/2.4/introduction.html)很容易地了解到。此外，好消息是所有的测试工具都有或多或少相似的语法。

好了，我们写了测试，但是现在呢？嗯，我们通过运行`karma start`在终端中运行测试。

您应该会看到类似这样的内容:

[![](../Images/cb077e86861f7cef7c802a00abab3f83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZTbbeSoY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/1J2vYXP.png)

我们在这里看到了什么？我们发现我们的测试失败了。那么，我们现在做什么？我们进入下一步，以最简单的方式通过测试。那么，我们要怎么做呢？我们在`index.js`文件中编写一个`add`函数:

`function add() {}`

现在我们有一个通过测试。太好了。我们能重构(第三步)什么吗？很可能不在这个阶段，因此我们继续前进。

那么我们对函数`add`的下一个期望是什么呢？嗯，我们期望，例如，如果我们传递数字 1 和 2 给它，它会返回数字 3。那么我们如何为此编写一个测试呢？就像我们说的那样。所以:

```
it ("should return 3 when passed 1, 2", function (){
    expect(3).toEqual(add(1,2));
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有一个失败的测试，我们去解决它。此时我们扪心自问:

> 通过这个测试最快的方法是什么？

这个问题的答案是从函数中返回 3:

```
function add(){
    return 3;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们又一次通过了测试。

然而，假设我们想做另一个测试，当 3 和 2 传入时，我们期望 5:

```
it ("should return 5 when passed 3, 2", function (){
    expect(5).toEqual(add(3,2));
}); 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我们可以通过的一个方法是检查参数，并创建一些开关情况...但是，正如你所看到的，这种情况越来越多，老实说，这不是我们应该做的事情，所以我们重构。

所以，经验法则，第三步是重构并确保测试仍然通过。

在有灵感的时候，我们写下(在`index.js`文件中):

```
function add (a, b){
    return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们现在有了一个通过的测试和重构的代码。

### 使输出更漂亮

在这一点上，它可能不会很好地呈现我们所拥有的所有规范。如果你想看，你可以安装:

```
npm install karma-spec-reporter --save-dev
npm install jasmine-spec-reporter --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

然后，在`karma.conf.js`文件里把记者改成`spec`，就像这样:

```
reporters: ['spec'] 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们运行`karma start`时，我们将得到一个很好的输出，比如:

```
Calculator
    ✓ should have an add function
    ✓ should return 3 when passed 1, 2
    ✓ should return 5 when passed 3, 2

PhantomJS 2.1.1 (Mac OS X 0.0.0): Executed 3 of 3 SUCCESS (0.002 secs / 0.002 secs)
TOTAL: 3 SUCCESS 
```

Enter fullscreen mode Exit fullscreen mode

关于如何跳过某个测试，只需在它前面加上 x:

```
xit ("should return 5 when passed 3, 2", function (){
    expect(5).toEqual(add(3,2));
}); 
```

Enter fullscreen mode Exit fullscreen mode

Karma 随后在控制台日志中报告这一情况:

```
Calculator
    ✓ should have an add function
    ✓ should return 3 when passed 1, 2
    - should return 5 when passed 3, 2 
```

Enter fullscreen mode Exit fullscreen mode

表示跳过了最后一项测试。

### 完整的源代码和测试代码清单

仅供参考，当我们为`sub`函数:
添加测试时，这就是`index.spec.js`文件的样子

```
describe ("Calculator", function (){

    describe ("add function", function (){
        it('should have an add function', function() {
            expect(add).toBeDefined();
        });

        it ("should return 3 when passed 1, 2", function (){
            expect(3).toEqual(add(1,2));
        });

        it ("should return 5 when passed 3, 2", function (){
            expect(5).toEqual(add(3,2));
        });
    });

    describe ("sub function", function (){
        it('should have an sub function', function() {
            expect(sub).toBeDefined();
        });

        it ("should return -1 when passed 1, 2", function (){
            expect(-1).toEqual(sub(1,2));
        });

        it ("should return 1 when passed 3, 2", function (){
            expect(1).toEqual(sub(3,2));
        });
    });

}); 
```

Enter fullscreen mode Exit fullscreen mode

这是`index.js`文件的内容:

```
function add(a, b) {
    return a + b;
}

function sub(a, b) {
    return a - b;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是 Karma 在此时运行后输出到控制台的内容:

```
Calculator
    add function
      ✓ should have an add function
      ✓ should return 3 when passed 1, 2
      ✓ should return 5 when passed 3, 2
    sub function
      ✓ should have an sub function
      ✓ should return -1 when passed 1, 2
      ✓ should return 1 when passed 3, 2 
```

Enter fullscreen mode Exit fullscreen mode

如果你想看一下完整的代码，可以在 Github 上[叉一下。](https://github.com/Hitman666/jstdd)

### 沙袋鼠

这一切都很酷，你可以打开你的终端，看看你的测试如何变绿。然而，就像现在的一切一样，有更好的工具。一个这样的工具是 [Wallabyjs](https://wallabyjs.com/docs/intro/how-it-works.html) 。让我给你展示一下它能做什么。

首先，你要为你的编辑器安装 Wallaby。它们支持 Visual Studio 代码、Atom、Submlime、Webstorm 等。

安装之后，您必须设置它的配置文件。让我们创建一个新文件，将其命名为`wallaby.js`，并将其放在我们的应用程序的根目录下。将以下代码复制/粘贴到其中:

```
module.exports = function (wallaby) {
  return {
    files: [
      'app/*.js'
    ],

    tests: [
      'tests/*.spec.js'
    ],
    debug: true
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

*此时你可能需要重启你的编辑器*。此时，您只需在编辑器中运行 Wallaby。在 Sublime 中，按下`CMD + SHIFT + P`并选择`Wallaby.js: Start`即可完成。崇高中还有一个便捷的快捷键:`CMD + .`后接`CMD + R`。

正如您将看到的，您现在在实际的编辑器中有了关于您的测试通过(左边的绿色矩形)或失败的信息:

[![](../Images/8710d4219845275f9dbb18e038b71f2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GNKsRBGd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/QHs7BuB.png)

实际上，Wallaby 还有很多特性，我将留给你去探索。我与他们没有任何关系；我只是碰巧喜欢它。但是，你不要说我没有提到它；正如每一个伟大的工具，它有它的价格。而且，如果你正在考虑(甚至抱怨)是否应该为某些软件付费，请阅读 Ambrose Little 关于[你的生产力值多少钱？](https://ambroselittle.svbtle.com/how-much-is-your-productivity-worth)。

好了，这就是 JavaScript 教程。现在让我们看看如何在 Ionic 框架应用程序中设置 Jasmine 和 Karma。

## Ionic 框架演示

为了学习这部分教程，您需要在全局范围内安装带有 npm 的 Ionic 和 Cordova 软件包。你可以在 [Ionic Framework:权威的 10，000 字指南](http://www.nikola-breznjak.com/blog/javascript/ionic/ionic-framework-a-definitive-10000-word-guide/)中了解更多关于如何做的内容。

### 启动新项目并安装必备组件

首先，我们开始一个新的离子项目:

`ionic start ionic-tdd tabs`

接下来，我们进入该文件夹并安装必要的先决条件。

```
cd ionic-tdd
npm install karma karma-jasmine karma-phantomjs-launcher jasmine-core --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

### 建立因果报应

请确保您已经从前面的 JavaScript 部分全局安装了 Karma。如果您不知道，您可以简单地使用:

`npm install -g karma-cli`

此外，此时，我们必须运行`npm install`来安装 Ionic `package.json`文件中的所有先决条件。

最后，我们需要用 bower 安装`angular-mocks`:

`bower install angular-mocks --save-dev`

因为我们将用它来模拟某些角度控制器。

完成后，我们在项目的根目录下创建一个新文件夹。姑且称之为`tests`:

`mkdir tests`

同样，让我们运行`karma init`命令(在您的终端中运行这个命令，在您的项目的根目录中运行一次)。

对于 Karma，您可以遵循与 JavaScript 部分相同的说明，只是不要输入源文件和测试文件的位置，我们将分别添加它们。

现在我们必须打开`karma.conf.js`文件并添加我们的源文件和测试文件:

```
files: [
        'www/lib/angular/angular.js',
        'www/js/*.js',
        'www/lib/angular-mocks/angular-mocks.js',
        'tests/*.spec.js'
],
browsers: ['PhantomJS'] 
```

Enter fullscreen mode Exit fullscreen mode

下一步，我们将配置我们的`gulpfile.js`文件，这样我们将能够通过 [Gulp](http://gulpjs.com/) 运行我们的测试，因为 Ionic 使用它作为它的任务运行器。我们在文件的顶部导入因果报应:

`var karmaServer = require('karma').Server;`

我们写了一个新任务叫做`test` :

```
gulp.task('test', function(done) {
    new karmaServer({
        configFile: __dirname + '/karma.conf.js',
        singleRun: false
    }).start();
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以像这样用`test`参数运行`gulp`:`gulp test`。

### 测试控制器

首先，我们在`tests`文件夹中创建一个新的`tests/controllers.spec.js`文件。

请注意，现在这不是 TDD 方法，因为我们已经在控制器中编写了代码。但是，如果你遇到一个没有单元测试的项目，这就是你要做的。另外，所有的重构都是为了使代码可测试，但这是另一个不同的故事了...

我们从编写描述函数开始:

```
describe('Controllers', function(){

}); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，因为这是有角度的，我们将有一个局部范围变量(`var scope`)。在每次测试之前，我们必须加载`starter.controller`模块:

`beforeEach(module('starter.controllers'));`

我们怎么知道我们必须设置这个模块？好吧，如果你看一下`controllers.js`文件，你会在顶部看到模块的名称为`starter.controllers`。

此外，我们需要注入 Angular 的范围变量并设置控制器。

```
beforeEach(inject(function($rootScope, $controller) {
    scope = $rootScope.$new();
    $controller('AccountCtrl', {$scope: scope});
})); 
```

Enter fullscreen mode Exit fullscreen mode

要将所有这些放在一个地方，您应该有一个类似这样的`controllers.spec.js`文件:

```
describe('Controllers', function(){
    var scope;

    beforeEach(module('starter.controllers'));

    beforeEach(inject(function($rootScope, $controller) {
        scope = $rootScope.$new();
        $controller('AccountCtrl', {$scope: scope});
    }));
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个样板代码，您必须在每个测试中编写它，所以尽管它起初看起来很奇怪，但在您使用它一段时间后，它会变成您不会想到的东西。

同样，如果您想知道我们是如何来到`AccountCtrl`的，只需看一下`controllers.js`文件和我们试图测试的控制器的名称。

最后，我们来测试一下。并且，假设我们想要测试`settings`对象上的`enableFriends`属性是否被设置为`true`，我们将编写这样一个测试:

```
it('should have enableFriends property set to true', function(){
    expect(scope.settings.enableFriends).toEqual(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们用`gulp test`运行我们的测试，我们可以看到我们的测试通过了。

### 测试服务/工厂

现在我们要为我们的工厂写一个测试`Chats`。如您所见，工厂有三个函数，用于获取所有聊天(目前是硬编码的)、移除聊天和获取特定聊天。

首先，我们将在`tests`文件夹中创建一个名为`services.spec.js`的新文件，并添加我们的`describe`函数:

```
describe('Chats Unit Tests', function(){

}); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将设置模块并注入聊天工厂:

```
var Chats;
beforeEach(module('starter.services'));

beforeEach(inject(function (_Chats_) {
    Chats = _Chats_;
})); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以编写我们的第一个测试了，好吧，让我们先测试一下我们的聊天工厂是否被定义了:

```
it('can get an instance of my factory', inject(function(Chats) {
    expect(Chats).toBeDefined();
})); 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以检查它是否返回五个聊天记录

```
it('has 5 chats', inject(function(Chats) {
    expect(Chats.all().length).toEqual(5);
})); 
```

Enter fullscreen mode Exit fullscreen mode

如果在这一点上，我们还希望看到一个更好的 spec 报告，我们应该终止当前运行的 gulp 进程。安装所需的软件包:

```
npm install karma-spec-reporter --save-dev
npm install jasmine-spec-reporter --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

调整`karma.conf.js`文件:

`reporters: ['spec'],`

并用`gulp test`重新运行 gulp。

要将所有这些放在一个地方，您应该有如下所示的`services.spec.js`文件:

```
describe('Chats Unit Tests', function(){
    var Chats;
    beforeEach(module('starter.services'));

    beforeEach(inject(function (_Chats_) {
        Chats = _Chats_;
    }));

    it('can get an instance of my factory', inject(function(Chats) {
        expect(Chats).toBeDefined();
    }));

    it('has 5 chats', inject(function(Chats) {
        expect(Chats.all().length).toEqual(5);
    }));
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你想看一下完整的代码，可以在 Github 上[叉一下。](https://github.com/Hitman666/ionic-tdd)

### 沙袋鼠

如果你想在 Ionic 中尝试 Wallaby，你只需要创建`wallaby.js`文件并设置配置:

```
module.exports = function (wallaby) {
  return {
    files: [
        'www/lib/angular/angular.js',
        'www/js/*.js',
        'www/lib/angular-mocks/angular-mocks.js',
    ],

    tests: [
        'tests/*.spec.js'
    ],
    debug: true
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

到目前为止，我个人的收获是，即使你不采用这整个 TDD 咒语，我也敦促你至少开始使用单元测试，因为你已经看到了它们的价值。至于整个 TDD 咒语，我还没有看到这一切是如何实现的，因为我觉得正确地采用它需要一定的纪律，直到正确地实现。

当然，这一切只是冰山一角。我刚刚谈到了单元测试以及 Jasmine 作为测试环境可以做些什么。我希望从现在开始的某个时候，我能够与您分享一些最佳实践和一些高级技术。在那之前，我希望这对你们中的一些人有用，至少能让你们继续前进。

演示项目在 Github 上:

*   [Ionic 框架演示](https://github.com/Hitman666/ionic-tdd)

是的，吃红色药丸；)

[![](../Images/0818615bd587ac7579f750f6af8527e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0DHi8jzB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/SzkbXjDm.png)

* * *

如果有人感兴趣的话，下面是关于阅读材料和我一路上收集的笔记，我通往如此令人敬畏的 TDD 的道路。

## [树屋球场](https://teamtreehouse.com/library/javascript-unit-testing)

*   使用 E2E 测试 sparringly(这符合[谷歌帖子](http://googletesting.blogspot.hr/2015/04/just-say-no-to-more-end-to-end-tests.html))
*   套装和规格
*   `mocha --reporter nyan`
*   `"scripts": {"test":mocha, "test:watch":"mocha --watch ./test ./"}`
*   `npm run test:watch`

## 书籍上的话题

*   [测试驱动开发，Kent Beck](http://amzn.to/1SGPsks)
*   [重构:改进现有代码的设计](http://amzn.to/1VUpnBV)
*   [爱奥尼亚在行动——关于爱奥尼亚 TDD 的章节](http://amzn.to/1NqbdGh)

## 博文

### [JS TDD 简介](http://jrsinclair.com/articles/2016/gentle-introduction-to-javascript-tdd-intro/)

TDD 的优势:

*   它迫使人们去思考
*   这使得调试更加容易
*   它让编码变得更有趣

TDD 是一种编写软件的方法，在这种方法中，你在编写应用程序代码之前先编写测试。基本步骤是:

*   红色 -写一个测试，确保它失败
*   **绿色**——尽可能编写最简单、最容易的代码来通过测试
*   **重构**——优化和/或简化应用程序代码，确保所有测试仍然通过

你必须先思考，然后写一个测试。

```
// flickr-fetcher-spec.js
'use strict';
var expect = require('chai').expect;

describe('FlickrFetcher', function() {
    it('should exist', function() {
        var FlickrFetcher = require('./flickr-fetcher.js');
        expect(FlickrFetcher).to.not.be.undefined;
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们使用一种`Behaviour Driven Development (BDD)`风格来编写测试。这是 TDD 的一个变体，其中测试以如下形式编写:

*   描述`[thing]`
*   它应该`[do something]`

`[thing]`可以是一个模块，或者一个类，或者一个方法，或者一个函数。Mocha 包括像`describe()`和`it()`这样的内置函数，使这种风格的写作成为可能。

在测试失败之前没有模块代码。那我该怎么办？我写另一个测试。

经验法则是，在比较数字、字符串或布尔值时使用**等于**，在比较数组或对象时使用 **eql** 。注:`eql`在其他一些测试框架中被命名为`deepEqual`。但是，注意，茉莉只有`toEqual`。

### [JS TDD 第二部分简介](http://jrsinclair.com/articles/2016/gentle-introduction-to-javascript-tdd-ajax/)

我用来替换`$.getJSON()`的`fakeFetcher()`函数被称为**存根**。存根是一段代码，它与“真正的”代码具有相同的 API 和行为，但功能却大大减少了。通常，这意味着**返回静态数据**，而不是与一些外部资源交互。

典型的存根可能会替换如下内容:

*   对关系数据库的查询
*   与文件系统的交互
*   接受用户输入
*   需要长时间计算的复杂计算

### [TDD 应该很好玩](http://jrsinclair.com/articles/2016/tdd-should-be-fun/)

*   功能测试(E2E)
*   整合测试，比 E2E 更频繁

### [在 JS 测试这个话题上稍微有点名气的 Eric Elliot](http://www.sitepoint.com/javascript-testing-unit-functional-integration/)

*   单元测试、集成测试和功能测试都是自动化测试的类型，它们构成了连续交付的重要基础，这是一种开发方法，允许您在几天或几小时而不是几个月或几年内安全地将变更交付到生产中。
*   一个 bug 进入生产环境的成本比一个自动化测试套件捕获的 bug 的成本高很多倍。换句话说，TDD 具有压倒性的正 ROI。
*   你不能在单元测试、功能测试和集成测试之间进行选择。使用所有的测试套件，并确保每种类型的测试套件都可以独立运行。

* * *

*   **单元测试**
    *   确保应用程序的各个组件按预期工作。断言测试**组件 API**
*   **集成测试**
    *   确保组件协作按预期工作。断言可以测试组件 API、UI 或副作用(如数据库 I/O、日志记录等)
*   **功能测试**
    *   从用户的角度来看，确保应用程序按预期运行。断言主要测试**用户界面**

> 例如，您的应用程序可能需要将 URL 路由到路由处理程序。可以针对 URL 解析器编写单元测试，以确保正确解析 URL 的相关组件。另一个单元测试可能确保路由器为给定的 URL 调用正确的处理程序。然而，如果你想测试当一个特定的 URL 被发布时，一个相应的记录被添加到数据库中，那将是一个集成测试，而不是一个单元测试。

是的，你写了更多的代码，但是[研究已经客观地表明](http://evidencebasedse.com/?q=node/78)**使用 TDD 的良好测试覆盖可以减少 40% - 80%的 bug 密度**。

他的另外两个帖子:

关于 TDD 的 5 个常见误解&单元测试

*   TDD 太费时间了。业务团队绝不会同意
*   在你知道设计之前，你不能写测试，在你实现代码之前，你不能知道设计
*   您必须在开始编写代码之前编写所有的测试
*   红绿，永远重构？
*   一切都需要单元测试

> 这里有一个将改变你生活的建议:`Mocking is a code smell.`

[每个单元测试必须回答的 5 个问题](https://medium.com/javascript-scene/what-every-unit-test-needs-f6cd34d9836d#.9uudcx44o)

*   一份好的测试失败 bug 报告有什么内容？
*   你在测试什么？
*   它应该做什么？
*   输出是什么(实际行为)？
*   预期输出是什么(预期行为)？

### 很少有一般的好博文

*   [谷歌对 E2E、集成和单元测试的态度](http://googletesting.blogspot.hr/2015/04/just-say-no-to-more-end-to-end-tests.html)
*   [TDD 已死，测试万岁](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html)
*   [测试驱动开发不是测试](https://www.stickyminds.com/article/test-driven-development-isnt-testing)
*   [TDD 中的三角测量](http://elnur.pro/triangulation-in-testing/)
*   [JavaScript 测试驱动开发简介](http://tutorials.pluralsight.com/front-end-javascript/introduction-to-test-driven-development-in-javascript)
*   [让你的函数变得纯粹](http://alistapart.com/article/making-your-javascript-pure)
*   [编写优秀的单元测试](http://blog.stevensanderson.com/2009/08/24/writing-great-unit-tests-best-and-worst-practises/)
    *   单元测试不是为了寻找 bug，但是在重构的时候是非常好的
*   [以娱乐和盈利为目的的 Angular 测试服务](http://nathanleclaire.com/blog/2014/04/12/unit-testing-services-in-angularjs-for-fun-and-for-profit/)
    *   如果有一种方法可以减少您编写(或管理)的代码中的缺陷数量，提高可交付产品的质量和上市时间，并使您之后的人更容易维护这些东西，您会这样做吗？
    *   你有多少次听到过这样的说法，“编写测试没有交付完成的代码重要？”如果你像我一样，这太多了，如果你根本不做测试，上帝会帮助你的。程序员也是人，我们都会犯错。所以测试你的代码。测试我的代码的次数帮助我在不可预见的问题变成纯粹的错误之前抓住了它们，防止了未来的倒退，或者仅仅是更好地架构，这是非常令人惊讶的。这是来自一个曾经讨厌为代码写测试的人。讨厌它。
    *   Jasmine 是一个行为驱动的开发框架，这是一种迂回的说法，我们的测试包括对他们正在测试的部分以及他们应该做什么的描述。
    *   您可以在 JavaScript 中非常容易地创建存根对象，所以如果没有必要引入额外的复杂性，那么就这样做。
    *   编写代码的时候，要把最终维护你的代码的人想象成一个知道你住在哪里的暴力精神病患者。
*   一个将永远改变你编码方式的奇怪技巧:Javascript TDD
    *   你是否曾经修复了一个 bug，却发现它在系统的另一部分破坏了一些可怕的东西？你一点都不知道，直到客户惊慌失措地打电话给支持部门？
    *   你是否曾经害怕接触一段复杂的代码，因为担心你可能会破坏它，并且再也无法修复它？…即使是你写的？
    *   你有没有发现一段你很确定不再被使用并且应该被删除的代码？但你把它留在那里以防万一？
    *   **TDD 不是关于测试的**。这是一种恰好涉及测试的思考和编码方式。
    *   TDD 和单元测试不是一回事。单元测试是测试的一种。TDD 是一种**编码技术**。
        *   红色——编写一个不起作用的小测试，也许一开始甚至无法编译
        *   绿色——让测试快速进行，在这个过程中犯下任何必要的错误
        *   重构——消除测试工作中产生的所有重复

## 最后，离子(Angular)相关 TDD 帖子

### [如何为你的 Ionic 应用编写自动化测试](http://gonehybrid.com/how-to-write-automated-tests-for-your-ionic-app-part-1/)

*   在单元测试的例子中，我们看到我们需要模拟依赖关系。对于集成测试，根据您想要一起测试的单元，您仍然可以模仿某些依赖项或者根本不模仿。

### [TDD 与离子](https://kapware.com/tdd-with-ionic/)

*   展示如何用 Jasmine 运行 Karma 的简短教程

### [单元测试你的 Ionic 框架 App](http://mcgivery.com/unit-testing-ionic-app/)

```
This tutorial was actually great (which I can't say for the previous two) and I've learned the most out of it and finally set up a test environment.

有趣的事实:我添加了`npm install --save-dev karma-nyan-reporter`，现在我这样运行我的测试:` karma start tests/my . conf . js-reporters nyan 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
其他一些 AngularJS TDD 的博文

*   [使用 Codeship 持续集成、Jasmine 和 Karma 对 AngularJS Ionic 应用程序进行单元测试](http://blog.pdsullivan.com/posts/2014/12/05/ionic-codeship-unit-testing-ci.html)
*   [AngularJS 中的单元测试最佳实践](http://andyshora.com/unit-testing-best-practices-angularjs.html)
*   [官方 AngularJS 单元测试指南](https://docs.angularjs.org/guide/unit-testing)
    *   下划线符号:使用下划线符号(例如:`_$rootScope_`)是 AngularJS 社区中广泛使用的一种约定，以保持测试中变量名的整洁。这就是$injector 在匹配参数时去掉前导和尾随下划线的原因。下划线规则仅适用于名称以一个下划线开始和结束的情况，否则不会发生替换。
*   [将 Karma 和 Jasmine 添加到现有的 Ionic 项目中](https://novas1r1.wordpress.com/2014/10/27/add-karma-and-jasmine-to-an-existing-ionic-project/)
*   [单元测试 AngularJS 应用](https://www.airpair.com/angularjs/posts/unit-testing-angularjs-applications)
*   [用茉莉和因果报应测试 AngularJS】](https://scotch.io/tutorials/testing-angularjs-with-jasmine-and-karma-part-1)

## 我的笔记

*   为了让因果报应和幻像一起运行，这是必须的。

*   不得不改变代码中的实际角度模拟 1.5.1 错误([https://github.com/angular/angular.js/issues/14251](https://github.com/angular/angular.js/issues/14251))。

至此，测试终于通过了！

## 工具

Wallabyjs -一个很棒的工具