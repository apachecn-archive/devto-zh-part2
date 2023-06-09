# 非 Node.js 开发人员的 Node.js

> 原文：<https://dev.to/raymondcamden/nodejs-for-the-non-nodejs-developer-2jhp>

*快速提示:当我第一次写这篇文章时，它完全是为对学习 Node 感兴趣的 ColdFusion 开发人员准备的。在与我的好友[布莱恩·里纳尔迪](https://www.remotesynthesis.com/)交谈后，他觉得这对一般对学习 Node 感兴趣的人会很有用。所以我更改了标题和 URL，但保留了文本的其余部分。我认为他是对的，如果你正在用任何服务器端语言进行 web 开发，我认为这可能非常有用，可以帮助你了解 Node 如何工作的一些特性！*

去年，在最后一次 [cfObjective](http://www.cfobjective.com/) 上，我专门为 ColdFusion 开发人员做了一个关于 Node.js 的演示。作为一名前 ColdFusion 开发人员(除了偶尔做一些次要的工作)，我分享了我喜欢什么，不喜欢什么，以及在学习 Node 时让我感到困惑的事情。我的意图不是提供一个“Node 简介”，因为有很多这样的资源存在，而是专注于我在学习时注意到的一些特别的东西。我仍在学习 Node，在我认为自己是专家之前，可能还有十年的时间。但是我很高兴我做出了改变，我也想帮助别人。一段时间以来，我一直打算创建一个书面版本的幻灯片，当一位读者几天前给我发电子邮件，从 ColdFusion 的角度询问 Node 时，我认为是时候离开我的后方，实际去做了。

[![Burning Bridges](img/48dce84d16657c339a625f54bc65e034.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZpvXsKFN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/08/cfn1.jpg)

### 这不是什么……

需要澄清的是，这并不是对 ColdFusion 的攻击。多年来，ColdFusion 为我和我的家人提供了收入来源。很长一段时间，它是最强大、最实用、最容易使用的平台之一。我对 Adobe 如何运行平台有一定的感觉，我对人们开始使用 ColdFusion 是否有意义也有一定的感觉，但这不是问题的关键。欢迎在评论中私下联系我，我很乐意分享。但是如果你想让你的客户开心，并且用 ColdFusion 把食物放在桌子上，无论如何，继续吧！

还要注意，JavaScript 和 Node 并不是完美的语言/平台。虽然我过去对 CFML 有过挫败感，但 ColdFusion 开发人员也应该准备好处理 JavaScript 的…特质。有时间去参观一下 wtfjs.com，看看 JavaScript 如何不时给你带来惊喜的好例子。就我个人而言，我最喜欢 JavaScript 的一个问题，这一点也不奇怪，也不是一个错误，就是忘记了什么时候一个值是一个字符串，然后对它执行一些算术运算。这很容易纠正，但直到今天还是会让我犯错。

### 那么什么是节点呢？

好吧，Node.js 专家们(是的，我在 Node 和 Node.js 之间来回穿梭，起诉我吧)，请不要在这里太生气。我将用一种在我学习时对我有意义的方式来定义 Node。有更好的，更深入的解释，但我想保持简单。

实际上，Node 在服务器上使用 JavaScript。它创建于 2009 年(尽管网景公司有一个更早的版本，似乎没人记得了)，由 V8 驱动。不是饮料(虽然那会很棒)，而是 Chrome 的 JavaScript 引擎。它是开源的(咳咳，Adobe ),并得到许多大公司的支持。基本上你不用担心它会消失或者价格会大幅上涨。

这是您已经习惯的 JavaScript，尽管是在不同的环境中。所以做`$("someFormField").val()`这样的事情没有意义。你写的不是在浏览器中运行的代码，而是在服务器中运行的代码。另一方面，您可以访问服务器的文件系统，并且可以对服务器进行文件系统 CRUD 和数据库操作。

像 ColdFusion 一样，调用节点服务器的最终结果是某种文本或二进制输出。Ie，HTML，JSON，动态图片等。

所以耶！很像 ColdFusion！

[![Woot! Dancing cats!](img/12b8b58a0ef78b4a02eccfb6651de876.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PGK9_0u8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/08/cfn2.jpg)

### 除了…

节点是非常裸露的骨头。开箱即用，你不会得到一个网络服务器。不要弄什么`<cfquery>`之类的。没有太多真正与 web 开发相关的东西，这很好。Node 不仅仅是一个 web 平台(稍后会详细介绍)。但是好消息是你可以建造任何你想要的东西。人们已经做到了。很多人。

NPM，或节点包管理器，使安装实用程序变得容易。托管在 npmjs.org 的 NPM 工具不仅可以让你搜索代码，还可以安装代码和任何依赖项。如果你安装了一些实用程序 Foo，它需要 Booger，然后又安装了同样需要 Booger 的 Goo，NPM 会聪明地意识到这一点，不会再下载了。自从第一天起，这个能力就成了*cold fusion 中缺失的一块巨大的*。你现在有了 [CommandBox](https://www.ortussolutions.com/products/commandbox) 这很好，坦率地说，它背后的人(Ortus Solutions)可能是 ColdFusion 有史以来发生的最好的事情。

不幸的是，虽然拥有 NPM 很棒，但也可能让人不知所措。你搜索类似“rss”的东西来添加 RSS 解析，你可能会得到一百多个结果。那真是…太好了。如果你没有做好准备，它也会让你呆在原地不动。您需要弄清楚什么样的库看起来具有最好的特性、最好的支持，并且最能满足您的业务需求。

这听起来好像我在批评开源(哦，我的上帝，神经！)我不是，但我确实认为习惯于这种生态系统和建筑方式的人可能不会意识到这对新来的人来说是多么令人震惊和恐惧。是的，自由开放的代码很棒。但这并不总是意味着这很容易。这是公平的，开发毕竟是工作，但不要想“哦，我需要在 Node 中做 X？没问题——我随便找个 npm 包就行了！”

好了，准备好了吗？没有吗？太糟糕了。

### 第一步

首先，你安装它。前往[nodejs.org](https://nodejs.org/en/)，为你的平台找到安装程序。如果你正在使用 WSL(Linux 的 Windows 子系统)，你会想要遵循 Ubuntu 的指示，或者考虑`nvm`或`n`(是的，祝你好运尝试谷歌那个)。一旦安装完毕，只要进入你的终端并确认你可以运行`node -v`:

[![node -v at the terminal](img/39c30ed6cb7f1b9b1e2c85033e135ccb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--84LzNrVK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/08/cfn3.jpg)

祝贺您——您成为了一名节点开发人员。去更新你的 LinkedIn 个人资料。

### 哦嘿——节点版本超赞！

注意到我上面使用的节点版本了吗？现在是谈论节点版本控制的好时机，因为它*如此简单*而且一点也不混乱。真的。

[![Stupid meme gif about node versioning](img/e631be12918a9ddffa991e5e1adf55bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7rOjmEFN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/08/cfn4.jpg)

当您访问 Node.js 页面时，您可能会注意到这个有趣的选择:

[![LTS versus Current](img/f929dd127824793ecd65de09ef4b62a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cYVFMjDO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/08/cfn5.jpg)

好吧…首先，我的版本(9.x)甚至没有被提及。但是我是落后了(10.8.0)还是领先了(8.11.3)？

是的，我自己也不知道。让我们去谷歌一下，第一个(也是最好的一个)答案来自 StackOverflow:

> 要了解这种差异，您需要了解为什么会有 Node 的长期支持(LTS)版本。
> 
> 节点 LTS 主要是针对企业使用，在那里可能有更多的阻力频繁更新，广泛的采购程序和冗长的测试和质量要求。
> 
> (一些删除的东西)
> 
> 一般来说，如果您能够跟上最新的稳定和未来的节点版本，您应该这样做。这些都是稳定的和生产就绪的版本，有很好的社区支持。不稳定的和实验性的功能隐藏在构建和运行时标志之后，不应该影响您的日常操作

来源:https://stackoverflow.com/a/34655149/52160

我的理解是倾向于使用当前的版本，但是检查你将在哪里部署你的代码，看看他们支持什么。

关于 Node 和 ColdFusion，我注意到一件事——我一般不会遇到版本问题。这并不是说 Node 不会改变，它会改变，但它不像 ColdFusion，在 ColdFusion 中，您可能在本地工作并使用一些随机标记，但后来发现您的客户端使用的是旧版本的 cold fusion，这样您就完蛋了。在使用 Node 时，它不会或很少影响我。这可能是我不够先进，但我会说不要担心，抓住当前版本。(是的，我需要更新。)一件很酷的事情是，你可以在你的代码中使用所有那些很酷的时髦的 ES6 东西，而不用在乎旧的浏览器。

### 咱们码！

假设你已经安装了 Node，我们怎么做一个基本的 Hello World 呢？

*   首先，您创建一个文件(好吧，这可能是显而易见的)
*   你写你的代码
*   然后`node filename`在你的终端
*   仅此而已。

一个例子

```
x = new Date();
y = 1;
z = 'Hello World';

console.log(x, y, z); 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一些变量，然后将它们记录到控制台。

“但是，雷，你说过这不是你在浏览器中使用的 JavaScript 吗？console.log 是如何工作的？

确实如此。跟着它走。不，说真的，还有其他方法来“写”出字符串，但是在调试时，你可以使用我们都喜欢的相同的熟悉的`console.log`命令。

[![Running hello world](img/fa6a481aa11da56d7c43003402f1c91e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QOMc0vd2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/08/cfn6.jpg)

### 添加功能

好了，既然 Node 没有附带 ColdFusion 的许多现成功能，我们该如何添加它呢？

正如我上面所说的，如果你想找到 X 的解，很可能你会找到大约一千个解。一旦你发现哪一个最适合你，你就有了几个获得代码的选择。

#### 选项 1 -复制粘贴

如果我们谈论的是 10 行左右的代码，比如你可能在 StackOverflow 上找到的代码，那么当然，只需将这些代码复制并粘贴到你的应用程序中。搞定了。

#### 选项 2 -模块

这里有很多技术细节我现在要忽略，但是在最简单的层面上，模块是一组打包的功能，你可以把它们包含在你的代码中。与 ColdFusion 最相似的是 CFC。

你找出你需要的模块——安装它——在你的文件中需要它——然后使用它。

“需求”部分对你来说是新的。在 ColdFusion 中，一旦 CFC 在文件系统中(以及在几个特定的文件夹中)，我们就可以实例化它。对于 Node，即使我们有可用的模块，我们仍然需要告诉我们的特定文件来加载功能。这只是一行代码，所以没什么大不了的。

让我们考虑一个简单的例子。Wordnik API 是一个与字典数据相关的非常酷的 API。让我们看一个通过节点使用 API 的演示。

```
const request = require('request');

let apiKey = 'secret key';
let word = 'fear';
let url = `http://api.wordnik.com:80/v4/word.json/${word}/definitions?limit=20&useCanonical=false&includeTags=false&api_key=${apiKey}`;

request(url, (err, resp, body) => {

    if(err) {
        throw new Error(err);
    }

    let result = JSON.parse(body);

    console.log(result[0].text);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个程序的第一行是我如何加载一个模块的例子，在这个例子中，[请求](https://www.npmjs.com/package/request)。您可以在 Node 中执行 HTTP 操作，而不需要添加任何额外的库，但是 request 使它变得更加简单。还有其他选项，例如， [axios](https://www.npmjs.com/package/axios) ，所以你有选项，但是 request 是最流行和最常用的选项之一。

我定义了几个变量，然后使用请求 API 来访问这个 URL。我检查并查看我是否有错误，如果没有，我解析 JSON 结果(哦，顺便说一下，你知道 ColdFusion 十年来一直无法正确解析 JSON 吗？是的——这在这里不是问题)然后打印到屏幕上。Wordnik API 的文档让我知道结果在`text`中，但是我没有阅读文档，我只是先做了这个:`console.log(result)`。

您还会注意到，我使用了一些奇特的 ES6 (err 7，我记不清了)JavaScript-ism。这完全是可选的。下面是一个可能看起来更熟悉的简单版本:

```
var request = require('request');

var apiKey = 'secret key';
var word = 'fear';
var url = 'http://api.wordnik.com:80/v4/word.json/'+word+'/definitions?limit=20&useCanonical=false&includeTags=false&api_key='+apiKey;

request(url, function(err, resp, body) {

    if(err) {
        throw new Error(err);
    }

    var result = JSON.parse(body);

    console.log(result[0].text);
}); 
```

Enter fullscreen mode Exit fullscreen mode

Node 不会在意你是 JavaScript noob 还是向导代码。

我们需要做些什么来让这些代码工作？首先，我们要安装`request`。在命令行中，您可以通过:`npm install request`来实现。npm CLI 将处理下载和安装请求以及它需要的任何东西。它会把这个放到一个叫做`npm_modules`的文件夹里。这是宇宙中最大的目录。抱歉。但是，这样做会抛出这个错误:

```
npm WARN saveError ENOENT: no such file or directory, open '/mnt/c/Users/ray/package.json' 
```

Enter fullscreen mode Exit fullscreen mode

接下来是其他更可怕的错误，基本上归结为一个简单的问题——缺少 package.json。

### 好吧，但是 package.json 是什么？

一旦您的节点应用程序开始使用从 npm 下载的东西，您需要将 package.json 文件添加到您的目录中。这个文件在较高的层次上定义了您的应用程序，包括名称、如何与之交互，以及最重要的，它依赖于什么。它是一个 JSON 文件，所以很容易阅读/修改，但通常情况下，您不必实际接触该文件。您可以通过运行:`npm init`来创建一个新的。这将询问您一系列问题，您可以简单地接受默认设置，然后按 enter 键。下面是一个例子:

```
{
  "name": "request_demo",
  "version": "1.0.0",
  "description": "",
  "main": "wordnik.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您安装请求模块，您将得到:

```
{
  "name": "request_demo",
  "version": "1.0.0",
  "description": "",
  "main": "wordnik.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "request": "^2.87.0"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在最酷的部分来了。还记得我说过`node_modules`文件夹有点大吗？就像宇宙的大小一样大？有了这个文件，你就可以和其他人共享你的代码，并排除那个特定的文件夹。如果开发者只是运行`npm install`，它将读取 JSON 文件的`dependencies`部分并包含所有内容。

关于这一点，我还有很多要跳过，但这将让你从最初开始。

如果你好奇的话，使用脚本的参数也很容易。Node 将其传递给一个名为`process.args`的对象。这是一个参数数组，其中第一项是`node`，第二项是文件名，所以通常从第三项开始检查参数。这个版本的脚本只是从上面的演示中删除了硬编码的单词:

```
 const request = require('request');

let apiKey = 'super secret key';

let word = process.argv[2];
let url = `http://api.wordnik.com:80/v4/word.json/${word}/definitions?limit=20&useCanonical=false&includeTags=false&api_key=${apiKey}`;

request(url, (err, resp, body) => {

    if(err) {
        throw new Error(err);
    }

    let result = JSON.parse(body);

    console.log(result[0].text);
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 进入网页！

[![Microsoft's old home page](img/aa1dd63e0e157daba5ce11890d5ff015.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dpApbO78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/08/cfn7.jpg)

首先是坏消息。如果你想开发一个网络应用，你需要手工制作。Node 支持这样做所需的一切——它可以启动 HTTP 服务器并监听端口。它可以根据请求发出代码，检查请求的路径，然后做任何有意义的事情。与 ColdFusion 相比，在 cold fusion 中，您只需创建一个名为 foo.cfm 的文件，如果该文件位于名为 snakes 的目录中，则可以在 yoursite.com/snakes/foo.cfm,节点上获得该文件。在构建 web 应用程序方面，该节点没有内置的文件与 URL 的关联。

好消息是这个问题在很久很久以前就被解决了。虽然你有选择，但在 Node 中构建 web 应用最流行的框架是 [Express](http://expressjs.com/) 。它为你做了大部分的样板工作，并使实际创建一个应用程序变得更加容易。前段时间刚学 Node 的时候，看到 Express 就是让我确信是时候学 Node 了。在此之前，我参加了多个 Node.js 介绍会议，在会议结束时，我们从头开始构建了一个 web 服务器，我认为这对我来说毫无意义。

虽然我不会在这里教你 Express(稍后我会分享最好的资源)，这里有一个简单的 Express 应用程序:

```
// Taken (and modified) from Express docs

const express = require('express');
const app = express();

app.use(express.static('public'));

// Routes, on url x do y
app.get('/', function (req, res) {
  res.send('<h1>Hello World!</h1>');
});

app.get('/meow', function (req, res) {
  res.send('<h1>Meow</h1>');
});

app.get('/woof', function (req, res) {
  res.send('<h1>Im so not a cat, sorry</h1>');
});

//start up
app.listen(3000, function () {
  console.log('Example app listening on port 3000!')
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个应用程序响应 3 个不同的网址- `/`、`/meow`和`/woof`。这条线:`app.use(express.static('public'));`比较爽。它允许您为 CSS、图像和 JavaScript 等静态资产定义一个文件夹。在这种情况下，如果我有 public/app.css，那么我只需在代码中链接到/app.css，它就会正确加载。

### 动态页面

所以-你喜欢 ColdFusion 是因为你可以在布局中融入一些逻辑，对吗？(老实说，你知道你有。)Node 也有相应的系统。所有上面的页面(技术上称为“路线”)都返回一个硬编码的字符串。你可以像这样做一些逻辑:

```
app.get('/cats', function(req, res) {
    let cats = ["Luna","Pig"];

    let html = '<h1>Cats</h1>';
    html += '<p>' + cats.join(',') + '</p>';
    res.send(html);

} 
```

Enter fullscreen mode Exit fullscreen mode

但是用 JavaScript 写 HTML 很乱。幸运快车让你为你的站点定义一个“模板”引擎。基本上是一种在 HTML 中使用标记等来添加基本逻辑和变量替换的方法。作为一个简单的例子，这是使用手柄模板引擎(还有很多)，这里有一个页面将呈现一些简单的变量:

```
<p>
    Title = 
</p>

<p>
    Time = 
</p> 
```

Enter fullscreen mode Exit fullscreen mode

下面是定义该文件路径的节点代码:

```
// Taken (and modified) from Express docs

const express = require('express');
const app = express();
const hbs = require('express-hbs');

app.use(express.static('public'));

app.engine('hbs', hbs.express4());
app.set('view engine', 'hbs');

app.get('/', function (req, res) {

    let time = new Date();

    res.render('index', {
        title:'Hello World',
        time:time
    });

});

//start up
app.listen(3000, function () {
  console.log('Example app listening on port 3000!')
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我的模板将被命名为 index.hbs. Handlebars 也做基本的循环和条件，但在大多数情况下，模板引擎希望你在你的 JavaScript 中做*逻辑*，而只是在你的模板中呈现。起初这很令人沮丧，但总的来说这是一个很好的主意。

### ColdFusion 提供给你的所有其他东西呢？

下面是您可以在 Node 中做的其他事情的简要介绍，这些事情可能不是很明显:

*   URL 和表单值:URL 值在`req.query`中可用，其中`req`是一个 HTTP 请求体，除了查询字符串之外，还有很多有趣的内容。表单需要更多的工作(一行代码),但是可以设置一个与 ColdFusion 中的表单范围具有相同值的`req.body`对象。
*   文件上传:是的，这个一开始有点乱。同样，Node 为您提供了开箱即用的一切，让您自己处理它们，但您真的想为此使用一个好的库，我推荐[强大的](https://github.com/felixge/node-formidable)。下面是一个代码如何使用强大外观的示例:

```
app.post('/something', (req, res) => {
  let form = new formidable.IncomingForm();
  form.parse(req, (err, fields, files) => {
    console.log('received files', files);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

*   Sessions:是的，你有 em，也有比 ColdFusion 更好的选项。我最喜欢的一个选项是*不*为用户使用会话，直到你实际为他们存储一个值。
*   数据库:是的，你也有它们，你需要找到一个支持你选择的数据库的库。老实说，ColdFusion 在这方面做得更好，但在 Node 中使用数据库一点也不困难。
*   错误处理:是的，内置的，Express 也有很好的支持，包括 404 和常规错误。此外，很容易编写代码，在开发过程中得到堆栈跟踪，而在生产过程中得到一条很好的消息。

### 即将上线

我记得玩了一两个月 Node，然后决定…“嘿——我想展示一下这个时尚弄潮儿演示！”然后我意识到-我不知道该怎么做。幸运的是，这现在变得非常简单。

而你可以使用 NGINX 或 Apache 和代理请求来运行一个节点应用程序。但是更好的解决方案是使用众多“PaaS”服务中的一种——平台即服务。以 [Zeit](https://zeit.co/) 为例，你可以进入一个 Node app 文件夹，输入`now`，就这样。然后，您可以展示您的代码。说真的，一个命令。(当然，真实世界的制作会稍微复杂一点，但老实说，不会太多。)

### 最终提示

这些没有特别的顺序，但是我把最好的留到了最后。

#### nodemon

安装并使用 [nodemon](https://nodemon.io/) 。这个小工具将运行一个节点 web 应用程序，并在您进行更改时重新加载它。哦，我没有提到这是网络应用的一个问题？好的，所以，不要担心，只要使用 nodemon。

#### 简单脚本

不要忘记，在构建 web 应用程序时，如果遇到棘手的问题，可以将代码写成脚本。这可能没有多大意义，但想象一下这个场景。您已经用 login 构建了一个 web 应用程序，登录后，用户可以单击一个链接来加载一个从数据库加载的猫的页面。你找到了一个很好的数据库包，但是使用起来有点棘手。你写代码，搞砸了，重新加载网络应用，不得不重新登录，点击链接，等等。

与其这样做，不如创建一个新文件，比如 test.js，并在其中放一些代码，用一些硬编码的值测试来自数据库的查询，并让它工作。然后，您可以将它集成回您的代码中。

我在使用无服务器时也有同样的体会。当做一些复杂的事情时，我会先构建一个测试脚本来本地运行，然后再尝试将其部署为无服务器应用程序。

每个节点开发者都知道这一点，而我不知道，所以他们现在可能都在嘲笑我。

#### 节点学校

NodeSchool 是一个很棒的资源。通过一个简单的 CLI 工具，您可以进行一系列练习，帮助您学习 Node 中的各种主题。然后，CLI 通过运行代码并检查输出来验证您的代码是否正确。

#### [T1】我的破烂](#my-junk)

你可以在这里找到我的节点垃圾-[https://www.raymondcamden.com/tags/nodejs](https://www.raymondcamden.com/tags/nodejs)。

#### 最后……

买这本书！是的，这本书……(如果你买了，我会得到几分钱):

我会警告你。这本书是旧的，新的版本很快就会出现。然而，这本书太他妈的好了--我是认真的--即使过时了我也会买。我有一本实体书和电子书——这就是它的优点。作为 ColdFusion 的开发人员，这本书对我来说是完美的，因为它主要关注如何用 Express 和我已经熟悉的术语来做“web stuff”。我怎么推荐都不为过——所以今天就买几本吧！

*Unsplash 上 [Nghia Le](https://unsplash.com/photos/V3DokM1NQcs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 的标题照片*