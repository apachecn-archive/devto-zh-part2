# 我很无聊，就把自己的网站做成了一个节点包。以下是方法。

> 原文：<https://dev.to/shalvah/i-was-bored-so-i-made-my-website-into-a-node-package-heres-how-2id3>

> 更新:我已经修改了我的网站，从终端设计开始，但是你可以在这里找到那个版本。

啊哈，抓到你了！你上当了，笨蛋。好吧，我该停止胡闹了。这篇文章的标题实际上是真实的，但有几点需要注意。这是我的网站:

[![](../Images/0eaf47905ce8a517b0e3d6df8376ae6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yfcti2yC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.shalvah.me/store/media/zplj307cguvkz41v6y6l.gif)

这将是一个有点长的阅读，所以如果你还没有准备好，这里有你需要的链接:

*   [我的网站](https://shalvah.me)
*   [网站来源](https://github.com/shalvah/shalvah.github.io)
*   [为网站提供动力的 NPM 包的来源](https://github.com/shalvah/shalvah)

## 我为什么要这样做？

我讨厌建立网站。

好吧，这有点过了。我为网络写软件，但是我不喜欢建立网站。我更喜欢*应用*。我知道，语义。

但是说真的，我讨厌担心这个图片应该放在哪里，或者那个横幅应该放在哪里，以及在哪里放置一堆链接。

嘿，这并不意味着我不喜欢漂亮的网站。我知道——尤其是当它们是一大堆功能的关键时。

我更喜欢网络应用，网站不只是显示信息，而是接受你的输入，用它做一些很酷的事情。我尤其喜欢在他们的后端工作。

为了让你知道我有多不喜欢网站，下面是我的网站以前的版本:

[![](../Images/409f50593a83c2a4f54dfbea042d4881.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ex0CE2V7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.shalvah.me/store/media/ad5uvtvo11211jlxsdu5.PNG)

是的，伙计们。就是这样。一些文字，一些图标。我想你可以说它看起来很体面，以极简主义的方式。我不顾一切地想在里面加入一些工程挑战，我添加了一个 hack 来获取和显示我最近的一些媒体文章。

我已经考虑重建我的网站有一段时间了，尤其是我一直在提高我的前端技能。但是做另一个网站的前景让我感到厌烦，所以我问，*我怎样才能把它做成某种应用程序？*

你说过度工程，我说 po-tah-to。

然后我想起几周前我曾试图用 CSS 和 jQuery 构建一个命令行 UI。为什么不把我的网站变成一个终端？我见过几个这样的网站，它们真的很酷。从那时起，我开始思考，*我一直想开发一个 CLI 应用*，所以下一个想法是*动手吧！让它成为一个 CLI 应用程序，还可以在浏览器中运行*！_ Yasss！

所以我开始工作。

## CLI 工具

首先，我知道我将向 CLI 和浏览器展示相同的功能，所以我从我的`src/shalvah.js`(因相关性而被截断):
开始

```
const shalvah = {
    bio: "Hi, I'm Shalvah. I'm a backend software engineer based in Lagos, Nigeria.",
    prompt: 'Want to know more about me? Check me out on these sites:',
    links: [
        {
            name: 'GitHub',
            value: 'https://github.com/shalvah',
        },
        {
            name: 'dev.to',
            value: 'https://dev.to/shalvah',
        },
        {
            name: 'Twitter',
            value: 'https://twitter.com/theshalvah',
        }
    ],
    email: 'hello@shalvah.me'
};

module.exports = shalvah; 
```

Enter fullscreen mode Exit fullscreen mode

这个对象保存了我的所有信息。网站或 CLI 中的其他内容只是一种呈现和交互的方式。

然后我开始写 CLI 界面。我使用了三个主要工具:

*   用于创建命令行应用程序
*   [inquirer.js](https://github.com/SBoudrias/Inquirer.js) -用于创建交互式 CLI 提示
*   嗯，为了从终端打开东西

在我的包里. json:

```
{  "main":  "src/shalvah.js",  "bin":  "cli.js"  } 
```

Enter fullscreen mode Exit fullscreen mode

*   `main`:我之前创建的文件，所以无论何时你运行`require('shalvah')`，你都会得到那个对象。这样我就可以在它周围包装一个浏览器 UI(或者任何我选择的东西)。
*   `bin`:节点将链接到的可执行文件。所以当你`npm install -g shalvah`，然后你运行`shalvah`，Node 会执行这个文件。

这里是`cli.js`中的内容(再次因相关性而被截断):

```
#!/usr/bin/env node const program = require('commander');
const inquirer = require('inquirer');
const opn = require('opn');
const shalvah = require('./src/shalvah');

program.description('Shalvah in your command-line')
    .parse(process.argv);

console.log(shalvah.bio);
inquirer.prompt({
    name: 'link',
    type: 'list',
    message: shalvah.prompt,
    choices: shalvah.links
}).then(answers => {
    console.log(`Opening ${answers.link}`);
    opn(answers.link);
}); 
```

Enter fullscreen mode Exit fullscreen mode

很简单，最终。感谢这三个了不起的工具，用几行代码就完成了成熟的 CLI 应用程序。

之后，剩下要做的就是将包发布到 NPM，然后用`npm install -g shalvah`安装，瞧:

[![](../Images/e405f70d34e7a1ec02066b211bb6f64b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--24EUk_0Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.shalvah.me/store/media/qqjes6p80semcjy8tboz.gif)

## 建设网站

这有点复杂。我的意图是安装我的 NPM 包，然后创建一个`index.js`作为浏览器的入口点，就像`cli.js`是 CLI 的入口点一样。`index.js`将建立终端环境并向包发送调用。怎么样了？

良好的...

### 创建终端 UI

我要做的第一件事是处理终端 UI。我最近一直在提升我的前端技能，所以我很想自己动手。我最终决定使用一个库，因为我意识到我需要很多东西(比如事件处理程序和自定义键处理程序),这将花费我很多时间来编写、测试和重构。我也没有足够的关于终端、缓冲区和 I/O 流如何工作的知识。

我做了一些研究，我能找到的最可行的选项是 [xterm.js](https://xtermjs.org/) 。Xterm.js 是一个非常强大的网络终端模拟器。不幸的是，它的文档急需改进，所以我花了很长时间才弄明白如何使用它。此外，它还支持很多特性，但很多东西都是在底层实现的，所以我不得不围绕这些特性编写定制的处理程序。

### 将控制台移植到浏览器

接下来我想到的是，我真的很喜欢控制台上的 Inquirer.js 提示。我也想在网站上发布这些信息。同样，选择是:编写你自己的代码或者找一个库。出于同样的原因，我再次选择了库。我决定在浏览器中使用与 CLI 相同的库(Inquirer.js 和 Commander)。

我面临的一些挑战:

### *如何在浏览器中使用为命令行设计的包？*

在这里， [Browserify](https://github.com/browserify/browserify) 前来救援。如果你不熟悉的话，Browserify 是一个很棒的工具，它允许你在浏览器中使用节点包。它还为像`process`和`__dirname`这样的节点事物提供了“垫片”(一个垫片就像一个赝品)。

### 堵在指挥官身边

这相对容易，因为它的 API 希望您传入命令行参数(通常是`process.argv`)。在命令行上，运行`shalvah help`会用类似于`['/usr/bin/node', 'shalvah', 'help']`的东西填充`process.argv`，所以在浏览器中我做了:

```
 commander.parse([''].concat(args)); 
```

Enter fullscreen mode Exit fullscreen mode

### 整合查询者. js

这是一个更大的问题。它的代码大部分读写到`process.stdout` / `process.stdin`，都是 [`Readline.Interface`](https://nodejs.org/api/readline.html#readline_class_interface) 的实例。好消息是:

*   库依赖于行为(某些方法的存在)，而不是继承(`x instanceof Readline.Interface`)，以及
*   Xterm.js 已经支持 readline 的大部分功能。我写了一个 shim，假装`xterm.js`是接口的一个实例，然后我用 Browserify 的 [aliasify 转换来替换 Inquirer 期望用我的来替换的`readline`。shim 的简化版本如下所示:](https://github.com/benbria/aliasify)

```
module.exports = {
  createInterface({ input, output }) {
      // so we don't redefine these properties
      if (input.readlineified) {
          return input;
      }
      // normally, input and output should be the same xterm.Terminal instance
      input.input = input;
      input.output = input;
      input.pause = function () {};
      input.resume = function () {};
      input.close = function () {};
      input.setPrompt = function () {};
      input.removeListener = input.off.bind(input);

      Object.defineProperty(input, 'line', {
          get: function () {
              return input.textarea.value;
          }
      });
      input.readlineified = true;
      return input;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

Xterm.js 已经有了一个`write`函数，所以没有必要定义它。

我还必须做一些非常具体的垫片:

```
 // The most important shim. Used by both Commander and Inquirer.
    // We're tricking them into thinking xterm is a TTY 
    // (see https://nodejs.org/api/tty.html)
    term.isTTY = true;

    // Xterm is both our input and output
    process.stdout = process.stdin = process.stderr = term;

    // Shim process.exit so calling it actually halts execution. Used in Commander
    process.exit = () => {
        term.emit('line-processed');
        throw 'process.exit';
    };
    // catch the process.exit so no error is reported
    window.onerror = (n, o, p, e, error) => {
        if (error === 'process.exit') {
            console.log(error);
            return true;
        }
    };

    // For inquirer.js to exit when Ctrl-C is pressed (SIGINT)
    process.kill = () => {
        process.running = false;
        term.writeln('');
        term.writeThenPrompt('');
    }; 
```

Enter fullscreen mode Exit fullscreen mode

### 适当调整终端尺寸

我面临的另一个挑战是调整终端的大小，使它在桌面和移动上都很好看，没有任何难看的滚动条。以下是我希望它在手机上的样子:

[![](../Images/ef4e19e36e33d1974ea6d3061cc9147d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mqlYhor6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.shalvah.me/store/media/axgjx11utkix6d7cis85.PNG)

要做到这一点有点困难，因为终端窗口的大小不仅受 CSS 规则的影响，还受每行的行数和列数的影响，而行数和列数又受字体大小的影响。这非常棘手。如果列数多了 1 个单位，就会出现滚动条。在做了一些研究并尝试了许多方法之后，我决定采用这个方法:

```
 const term = new Terminal({
        cursorBlink: true,
        convertEol: true,
        fontFamily: "monospace",
        fontSize: '14',
        rows: calculateNumberOfTerminalRows(),
        cols: calculateNumberOfTerminalCols(),
    });

    // This measures the height of a single character using a div's height
    // and uses that to figure out how many rows can fit in about 80% of the screen
    function calculateNumberOfTerminalRows() {
        let testElement = document.createElement('div');
        testElement.innerText = 'h';
        testElement.style.visibility = 'hidden';
        document.querySelector('.term-container').append(testElement);
        testElement.style.fontSize = '14px';
        let fontHeight = testElement.clientHeight + 1;
        testElement.remove();
        return Math.floor(screen.availHeight * 0.8 / fontHeight) - 2;
    }

    // This measures the width of a single character using canvas
    // and uses that to figure out how many columns can fit in about 60% (80% for mobile) of the screen
    function calculateNumberOfTerminalCols() {
        const ctx = document.createElement("canvas").getContext('2d');
        ctx.font = '14px monospace';
        const fontWidth = ctx.measureText('h').width + 1;
        const screenWidth = screen.availWidth;
        return Math.floor(screenWidth * ((screenWidth > 600) ? 0.6 : 0.8) / fontWidth) + 3;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这可能看起来有点夸张，但这是我能想到的最可靠的方法。

### 颜色支持

我希望我的终端有颜色(谁不想呢？)，而用于此的程序是。不幸的是，chalk 似乎不能与 Xterm.js 一起工作，所以经过几个小时的调试，我发现问题来自于[一个用于检测颜色的外部依赖](https://www.npmjs.com/package/supports-color) chalk，所以我用我的垫片替换了它:

```
module.exports = {
    stdout: {
        level: 2,
        hasBasic: true,
        has256: true,
        has16m: false
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 切换手机上的提示

您会注意到，在我之前的例子中，我使用了一个名为`list`的询问提示，它允许您使用箭头键来选择一个选项。不过，在手机上，通常没有箭头键。所以我不得不切换到手机上的`rawlist`提示(使用数字输入):

```
 inquirer.prompt({
        name: 'link',
        type: (screen.width > 600) ? 'list' : 'rawlist'
}); 
```

Enter fullscreen mode Exit fullscreen mode

漫长的几个小时后，终端终于工作了！

[![](../Images/0eaf47905ce8a517b0e3d6df8376ae6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yfcti2yC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.shalvah.me/store/media/zplj307cguvkz41v6y6l.gif)

## 值得吗？

总的来说，这对我来说是一次有压力但有趣的经历，我学到了很多新东西。这是我第一次与我在这里描述的大部分内容一起工作，甚至学习，所以这是一个胜利。我甚至最终能够加入一些基本的 Unix 实用程序，比如`cd`和`ls`(试试吧！😁😁)

仍然有一些 bug，特别是在移动设备上，但是我已经厌倦了等待一切都变得完美，并继续发表这篇文章。希望你喜欢！