# 用 puppeter+Jest 编写端到端测试时处理异步

> 原文：<https://dev.to/aalises/dealing-with-asynchrony-when-writing-end-to-end-tests-with-puppeteer--jest-n37>

*在本文中，我们概述了在执行端到端测试时如何处理异步，使用* [**木偶师**](https://github.com/GoogleChrome/puppeteer) *作为 web 抓取器，使用* [**Jest**](https://jestjs.io) *作为断言库。我们将学习如何在浏览器上自动执行用户操作，等待服务器返回数据，等待我们的应用程序处理和呈现数据，从网站上实际检索信息，并与数据进行比较，以查看我们的应用程序是否真正按照给定用户操作的预期工作。*

* * *

因此，您已经创建并运行了出色的 web 应用程序，现在是测试的时候了....测试有很多种，从 [**单元测试**](https://en.wikipedia.org/wiki/Unit_testing) 测试组成应用程序的单个组件，到 [**集成测试**](https://en.wikipedia.org/wiki/Integration_testing) 测试这些组件如何相互作用。在本文中，我们将讨论另一种类型的测试，即 [**端到端(e2e)测试**](https://www.techopedia.com/definition/7035/end-to-end-test) 。

为了从用户的角度测试整个应用程序，端到端测试非常有用。这意味着测试从应用程序中呈现的结果、行为或数据对于给定的用户交互来说是预期的。他们从前端到后端进行测试，将应用程序视为一个整体，并模拟真实的案例场景。这是一篇关于什么是 e2e 测试及其重要性的好文章 。

为了测试 javascript 代码，最常见的断言框架之一是 **Jest** ，它允许你对你的函数和代码进行各种比较， [**，甚至测试 React 组件**](https://jestjs.io/docs/en/tutorial-react) 。特别是，为了进行 e2e 测试，一个相当新的工具**木偶师**来帮忙了。基本上就是一个基于 Chromium 的 [**web 刮刀**](https://en.wikipedia.org/wiki/Web_scraping) 。根据报告，它是一个*“节点库，提供一个高级 API 来控制 Chrome 或 DevTools 协议上的 Chrome”*。

它提供了一些方法，因此您可以通过代码模拟用户在浏览器上的交互，例如单击元素、浏览页面或在键盘上键入。让一只训练有素的猴子在你的应用程序上执行真实的任务🐒

你可以在这里找到两个测试库的 github repos:

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png)[Facebook](https://github.com/facebook)/[jest](https://github.com/facebook/jest)

### 令人愉快的 JavaScript 测试。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![npm version](../Images/80cf8713d30c19392582da32ed4f1afc.png)](http://badge.fury.io/js/jest)[![Jest is released under the MIT license.](../Images/486bafc059df9137c69818cd66696105.png)](https://github.com/facebook/jest/blob/master/LICENSE)[![Follow on Twitter](../Images/082c151275655a50d8f07e7959175cb6.png)](https://twitter.com/intent/follow?screen_name=fbjest)

[![](../Images/dde7a63b10c146e691f64affa6839ed3.png)T2】](https://raw.githubusercontent.com/facebook/jest/master/website/static/img/jest-readme-headline.png)

## <g-emoji class="g-emoji" alias="black_joker" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f0cf.png">🃏</g-emoji> 愉快的 JavaScript 测试

**👩🏻‍💻开发者就绪**:全面的 JavaScript 测试解决方案。适用于大多数 JavaScript 项目。

**🏃🏽即时反馈**:快速、交互式观看模式只运行与更改文件相关的测试文件。

**<g-emoji class="g-emoji" alias="camera_flash" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f4f8.png">📸</g-emoji>快照测试**:捕捉大型对象的快照，以简化测试并分析它们如何随时间变化。

*详见[jest js . io](https://jestjs.io)T3】*

## 目录

*   [入门](https://raw.githubusercontent.com/facebook/jest/master/#getting-started)
*   [从命令行运行](https://raw.githubusercontent.com/facebook/jest/master/#running-from-command-line)
*   [附加配置](https://raw.githubusercontent.com/facebook/jest/master/#additional-configuration)
    *   [生成基本配置文件](https://raw.githubusercontent.com/facebook/jest/master/#generate-a-basic-configuration-file)
    *   [利用巴别塔](https://raw.githubusercontent.com/facebook/jest/master/#using-babel)
    *   [使用 Webpack](https://raw.githubusercontent.com/facebook/jest/master/#using-webpack)
    *   [使用包裹](https://raw.githubusercontent.com/facebook/jest/master/#using-parcel)
    *   [使用打字稿](https://raw.githubusercontent.com/facebook/jest/master/#using-typescript)
*   [文档](https://raw.githubusercontent.com/facebook/jest/master/#documentation)
*   [徽章](https://raw.githubusercontent.com/facebook/jest/master/#badge)
*   [投稿](https://raw.githubusercontent.com/facebook/jest/master/#contributing)
    *   [行为准则](https://raw.githubusercontent.com/facebook/jest/master/#code-of-conduct)
    *   [投稿指南](https://raw.githubusercontent.com/facebook/jest/master/#contributing-guide)
    *   [好的第一期](https://raw.githubusercontent.com/facebook/jest/master/#good-first-issues)
*   [学分](https://raw.githubusercontent.com/facebook/jest/master/#credits)
    *   [支持者](https://raw.githubusercontent.com/facebook/jest/master/#backers)
    *   [赞助商](https://raw.githubusercontent.com/facebook/jest/master/#sponsors)
*   [执照](https://raw.githubusercontent.com/facebook/jest/master/#license)

## 入门指南

使用 [`yarn`](https://yarnpkg.com/en/package/jest) 安装 Jest:

```
yarn add --dev jest
```

Enter fullscreen mode Exit fullscreen mode

或者 [`npm`](https://www.npmjs.com/package/jest) :

```
npm install --save-dev jest
```

Enter fullscreen mode Exit fullscreen mode

注意:Jest 文档使用`yarn`命令，但是`npm`也可以。你可以比较一下[纱文件里的`yarn`和`npm`命令，这里是](https://yarnpkg.com/en/docs/migrating-from-npm#toc-cli-commands-comparison)。

让我们从编写一个测试开始…

</article>

[View on GitHub](https://github.com/facebook/jest)

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) [木偶师](https://github.com/puppeteer) / [木偶师](https://github.com/puppeteer/puppeteer)

### 无头 Chrome Node.js API

<article class="markdown-body entry-content container-lg" itemprop="text">

# 操纵木偶的人

[![Build status](../Images/2adf3b28ac9a6dda27e57c57e0497aa6.png) ](https://github.com/puppeteer/puppeteer/actions?query=workflow%3Arun-checks) [ ![npm puppeteer package](../Images/406efc2eb0d1d17fbacf5b98148bd281.png)](https://npmjs.org/package/puppeteer)

[![](../Images/48ffbae0e7030b5a769ac5d7d281043a.png)T2】](https://user-images.githubusercontent.com/10379601/29446482-04f7036a-841f-11e7-9872-91d1fc2ea683.png)

###### [API](https://github.com/puppeteer/puppeteer/blob/v8.0.0/docs/api.md) | [FAQ](https://raw.githubusercontent.com/puppeteer/puppeteer/main/#faq) | [投稿](https://github.com/puppeteer/puppeteer/blob/main/CONTRIBUTING.md) | [故障排除](https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md)

> Puppeteer 是一个节点库，它提供了一个高级 API 来控制 Chrome 或通过 [DevTools 协议](https://chromedevtools.github.io/devtools-protocol/)的 Chrome。默认情况下，木偶师运行[无头](https://developers.google.com/web/updates/2017/04/headless-chrome)，但可以配置为运行全(非无头)铬或铬。

###### 我能怎么做呢？

大多数你可以在浏览器中手动完成的事情都可以使用木偶师来完成！这里有几个例子可以帮助你开始:

*   生成页面的截图和 pdf。
*   抓取 SPA(单页应用程序)并生成预呈现内容(即“SSR”(服务器端呈现))。
*   自动化表单提交、UI 测试、键盘输入等。
*   创建一个最新的自动化测试环境。使用最新的 JavaScript 和浏览器功能，在最新版本的 Chrome 中直接运行测试。
*   捕获站点的[时间轴跟踪](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference)来帮助诊断性能问题。
*   测试 Chrome 扩展。

旋转一下:[https://try-puppeteer.appspot.com/](https://try-puppeteer.appspot.com/)

## 入门指南

### 装置

使用木偶师在你的…

</article>

[View on GitHub](https://github.com/puppeteer/puppeteer)

因此，**puppet er+Jest**已经成为测试 web 应用程序的一种很好的开源方式，通过在 puppet er 提供的无头浏览器上打开应用程序，模拟用户输入和/或交互，然后检查呈现的数据和我们的应用程序对不同动作的反应方式是否与 Jest 的预期一致。

在本文中，我们不会涵盖使用 puppeter+jest 进行测试的整个工作流程(比如安装，或者设置测试的主要结构，或者[测试表单](https://www.valentinog.com/blog/ui-testing-jest-puppetteer/))，而是将重点放在我们这样做时必须考虑的最重要的事情之一:**异步**。

##### 🤘[但是，嘿，这里有一个关于如何用木偶师+ Jest 测试的很棒的教程，让你开始。](https://blog.logrocket.com/end-to-end-testing-react-apps-with-puppeteer-and-jest-ce2f414b4fd7)

因为几乎所有的 web 应用程序都包含某种类型的**异步**。从后端检索数据，然后将数据呈现在屏幕上。然而，**木偶师按顺序执行所有的操作，所以...我们如何让他等到异步事件发生呢？**

<figure>

[![Can't wait](../Images/c8e13b389ee5f6e0cacb63bc51ba85fd.png)T2】](https://i.giphy.com/media/3owyoYjmvDijECcQTu/giphy.gif)

<figcaption>Figure 1: I feel you, but sometimes you have to wait just a little bit for everything to be rendered and ready, so your e2e tests do not crash and burn</figcaption>

</figure>

使用 [`Page`](https://pptr.dev/#?product=Puppeteer&version=v1.8.0&show=api-class-page) 类可用的`waitFor`函数，木偶师为您提供了一种**等待某些事情发生**的方法。您可以跟踪的变化是用户可以观察到的视觉变化。例如，由于一些异步调用，您可以看到页面中的某些内容何时出现、何时改变颜色、何时消失。然后人们会比较这些变化，这就是你在互动中所期望的，现在你有了。它是如何工作的？

* * *

### 等待，等待，等待...⏰

Puppeteer 中设置的`waitFor`函数帮助我们处理异步。当这些函数返回`Promises`时，通常利用 [`async/await`](https://javascript.info/async-await) ES2017 特性来执行测试。这些功能是:

*   `waitForNavigation`

```
await page.waitForNavigation({waitUntil: "networkidle2"}); 
```

Enter fullscreen mode Exit fullscreen mode

每当用户操作导致页面导航到另一个路径时，我们有时不得不在所有内容加载之前等待一会儿。为此我们有了`waitForNavigation`函数。它接受一个选项对象，在这里你可以设置一个`timeout`(以毫秒为单位)，或者`waitUntil`某个条件被满足。`waitUntil`的可能值为(根据[精彩木偶师文档](https://pptr.dev/)):

*   **load** ( *default* ):当`load`事件被触发时，认为导航已经完成。
*   **domcontentloaded** :当`DOMContentLoaded`事件被触发时，认为导航已经完成。
*   **networkidle0:** 当不超过 0 个网络连接持续至少`500` ms 时，认为导航结束
*   **networkidle2:** 当至少`500` ms 没有超过 2 个网络连接时，认为导航结束

一般来说，您会希望等到整个页面都加载完毕(`{waitUntil: load}`)，但这并不能保证一切都正常。您可以做的是等待特定的 DOM 元素出现，确保整个页面都已加载。您可以使用以下函数来实现这一点:

*   `waitForSelector`

这个函数等待一个特定的 [CSS 选择器](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors)出现，表明它匹配的元素在 DOM 上。

```
await page.waitForSelector("#create-project-button"); 
```

Enter fullscreen mode Exit fullscreen mode

```
await page.goto("https://www.example.com",{waitUntil: "load"});
await page.click("#show-profileinfo-button"); //Triggers a navigation

/* 
We can either wait for the navigation or wait until a selector that indicates 
that the next page is operative appears
*/
await page.waitForNavigation({waitUntil: "load"});
await page.waitForSelector("#data-main-table"); 
```

Enter fullscreen mode Exit fullscreen mode

*   `waitForFunction`

最后一个函数等待某个函数返回 true，以便继续执行。它通常用于监视选择器的一些属性。当选择器没有出现在 DOM 上，但是它的某些属性发生了变化时，就会用到它(所以您不能等待选择器，因为它已经在那里了)。它接受字符串或闭包作为参数。

例如，您希望等到某个消息发生变化。测试将通过首先使用`evaluate()`puppeter 函数获取消息来执行。它的第一个参数是一个在浏览器上下文中计算的函数(就像你在 Chrome 控制台中输入一样)。然后我们执行改变消息的异步操作(点击按钮或任何🖱🔨)，然后等待消息变化。

```
const previousMessage = await page.evaluate( () => document.querySelector('#message').innerHTML);

//Async behaviour...

await page.waitForFunction(`document.querySelector('#message').innerHTML !== ${previousMessage}`); //Wait until the message changes 
```

Enter fullscreen mode Exit fullscreen mode

使用这些`waitFor`函数，我们可以在异步操作后检测页面中的内容何时改变，现在我们只需要检索我们想要测试的数据。

* * *

### 异步操作后从选择器中检索数据

一旦我们检测到由异步代码引起的变化，我们通常希望从我们的应用程序中提取一些数据，稍后我们可以将这些数据与来自用户交互的**预期视觉结果**进行比较。我们使用`evaluate()`来做这件事。检索数据时最常见的情况是:

#### -检查 DOM 元素是否出现

一个非常常见的情况是检查给定的元素是否已经呈现在页面上，从而出现在 DOM 上。例如，保存一篇文章后，您应该在保存的文章部分找到它。导航到那里并查询 DOM 元素是否确实存在，这是我们可以断言的基本数据类型(作为一个**布尔断言**)。

在下面找到一个例子，检查一个 id 为`post-id`的给定帖子是否出现在 DOM 上，其中 id 是我们知道的一个数字。首先我们保存文章，我们等待文章被保存，转到保存的文章列表/路径，看到文章在那里。

```
const id = '243';

await page.click(`#post-card-${id} .button-save-post`);

//The class could be added when the post is saved (making a style change on the button)
await page.waitForSelector(`#post-card-${id} .post-saved`);
await page.click('#goto-saved-posts-btn');
await page.waitForNavigation();

const post = await page.evaluate(id => {
    return document.querySelector(`#post-${id}`) ? true : false
},id);

expect(post).toEqual(true); 
```

Enter fullscreen mode Exit fullscreen mode

在那里，我们可以观察到一些东西。

1.  前面提到的测试元素需要有*的唯一 id*。既然如此，查询选择器就容易多了，我们不需要做嵌套查询，根据元素在 DOM 上的位置来获取元素(*嘿，从那个表的第一行获取第一个 tr 元素*🙋🏼).

2.  我们看到了如何将参数传递给 evaluate 函数，并使用它将变量插入到选择器中。当函数在另一个作用域中被求值时，您需要将 node 中的变量绑定到新的作用域，您可以通过第二个参数来实现。

#### -检查匹配的属性值(如 innerHTML，选项...)

现在想象一下，不是检查一个元素是否在 DOM 上，而是实际上**想要检查页面上呈现的保存的帖子列表是否确实是你保存的帖子**。也就是说，您希望将字符串的**数组与帖子名称(例如`["post1,"post2"]`)和用户保存的帖子(您可以预先知道测试用户的帖子，或者从服务器响应中检索)进行比较。**

为此，您需要查询文章的所有标题元素，并从中获取给定的属性(可能是它们的 innerHTML、value、id...).之后，您必须将该信息转换为一个 [**可序列化值**](https://www.oreilly.com/library/view/javascript-the-definitive/9781449393854/ch06s09.html) (评估函数只能返回可序列化值，否则它将返回 **null** ，也就是说，总是返回数组、字符串或布尔值，例如，而不是 HTMLElements...).

执行该测试的一个示例是:

```
 const likedPosts = ["post1","post2","post3"];

    const list = await page.evaluate(() => {
      let out = []
      /*
        We get all the titles and get their innerHTML. We could also query 
        some property e.g title.getAttribute('value')
      */
      const titles = document.querySelectorAll("#post-title");
      for(let title of titles){
        out.push(title.innerHTML)
      }
      return out;
    });

    expect(list).toEqual(likedPosts); 
```

Enter fullscreen mode Exit fullscreen mode

这些是测试应用程序数据的最基本的情况(也是您大部分时间会用到的情况)。

#### ——断言`waitFor`成功

代替`evaluate()`你可以做的另一件事是，如果你只是想断言一个布尔选择器或一个特定的 DOM 变化，只需将`waitFor()`调用赋给一个变量并检查它是否为真。这种方法的缺点是，您必须为函数**设置一个估计的**超时**，该超时小于在开始**设置的 Jest 超时。⏳

如果超时，测试将失败。它要求你设置一个估计的超时时间，你认为这个时间足够让元素在请求发出后呈现在你的页面上...🤔).

例如，我们想要检查**是否有新的标签被添加到 post** 中，查询添加标签之前和之后出现的标签元素的数量，也就是说，比较它们的长度，看看它是否增加了，表示标签确实被添加了。

```
const previousLength = await page.evaluate(() => document.querySelectorAll('#tag').length);

//Add tag async operation...

//Wait maximum 5 seconds to see if the tag has been added.
const hasBeenAdded =  await page.waitForFunction(previousLength => {
       return document.querySelector('#tag')length > previousLength 
      }, {timeout: 5000}, previousLength);

expect(hasChanged).toBeTruthy(); 
```

Enter fullscreen mode Exit fullscreen mode

*注意，如果您将 waitForFunction 参数指定为闭包，您还必须将变量绑定到第三个元素`waitForFunction()`。*

为了将数据与我们从页面中检索到的信息进行比较，即我们的**基本事实**，一种方法是让一个受控的测试用户知道每一次测试的预期结果(喜欢的帖子数量，写的帖子)。在这种方法中，我们可以**硬编码**😱预期的数据，如帖子标题、点赞数等...就像我们在上一节的例子中所做的那样

你也可以**伪造来自服务器**的响应数据。通过这种方式，您可以测试从后端获得的数据与应用程序中呈现的数据是否一致，方法是响应您事先知道的可预测的、不可变的数据。这是为了测试应用程序是否对给定调用从服务器返回的数据做出可预测的响应(正确解析)。

在下一节中，我们将看到如何劫持请求并提供您所知道的自定义数据。木偶师提供了一种方法来实现这一点，但是如果你想更深入地研究伪造`XMLHttpRequest`和你的测试管理的几乎所有数据，你应该看看 [`Sinon.js`](https://sinonjs.org/) 💖

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) [同义词](https://github.com/sinonjs) / [否则](https://github.com/sinonjs/sinon)

### 测试 JavaScript 的间谍，存根和模拟。

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![Sinon.JS](../Images/a7b39033f953a1d752414a50fb1e4f50.png)](https://sinonjs.org "Sinon.JS") 
否则。联署材料

独立和测试框架不可知的 JavaScript 测试间谍、存根和模拟(读作“叹息-非”，以勇士 Sinon 命名)

[![npm version](../Images/e4390767da09ff27f7cdb1f4a460ee43.png)](https://www.npmjs.com/package/sinon)[![Join the chat at https://gitter.im/sinonjs/sinon](../Images/80064cbae208bbac11d2f49a393a38ef.png)](https://gitter.im/sinonjs/sinon?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)[![Build status](../Images/59096af00c6c80b27487c4d7aa34ae24.png)](http://travis-ci.org/sinonjs/sinon)[![Sauce Test Status](../Images/3a1dbde8653ab0d23bb6ebc2822ed70d.png)](https://saucelabs.com/u/sinonjs)[![Codecov status](../Images/d8247c787fb2a53f5c3e53124da32303.png)](https://codecov.io/gh/sinonjs/sinon)[![OpenCollective](../Images/69552c55a7b3aae8733355c78a9c5a10.png)](https://raw.githubusercontent.com/sinonjs/sinon/master/#backers)[![OpenCollective](../Images/ae9d790b431ba3433788ecf528ddaf29.png)](https://raw.githubusercontent.com/sinonjs/sinon/master/#sponsors)[![npm downloads per month](../Images/da6ce70c82981ecc9fc640a2a77bc55f.png)](https://www.npmjs.com/package/sinon)[![CDNJS version](../Images/4261ebb2f242321c07b02762b0ddbf33.png)](https://cdnjs.com/libraries/sinon.js)[![Contributor Covenant](../Images/2d6e91c763e4451d33e9562c488edc4c.png)](https://raw.githubusercontent.com/sinonjs/sinon/master/CODE_OF_CONDUCT.md)

[![Sauce Test Status](../Images/e58cc4dfe9a081680002d39273b6a5f6.png)T2】](https://saucelabs.com/u/sinonjs)

## 和睦相处

关于兼容性和浏览器支持的详细信息，请参见 [`COMPATIBILITY.md`](https://raw.githubusercontent.com/sinonjs/sinon/master/COMPATIBILITY.md)

## 装置

通过 [npm](https://github.com/npm/npm)

```
$ npm install sinon 
```

或者通过希能在[主页](https://sinonjs.org/releases/)上下载的浏览器版本。也有基于 [npm 的 cdn](https://sinonjs.org/releases#npm-cdns)可以使用。

## 使用

有关使用的文档，请参见 [sinon 项目主页](https://sinonjs.org/)。

如果您有文档中没有涉及的问题，您可以[查看堆栈溢出](https://stackoverflow.com/questions/tagged/sinon)上的`sinon`标签，或者访问 irc.freenode.net:6667 上的#sinon.js。

也可以通过 [Sinon 搜索。JS 邮件列表档案](http://groups.google.com/group/sinonjs)。

## 目标

*   没有全球污染
*   使用方便
*   需要最少的“集成”
*   易于与任何测试框架无缝嵌入
*   轻松伪造任何界面
*   为 XMLHttpRequest、计时器等提供现成的假件

## 投稿？

请参见 [CONTRIBUTING.md](https://raw.githubusercontent.com/sinonjs/sinon/master/CONTRIBUTING.md) 了解如何为兴农做贡献的详细信息。射流研究…

…</article>

[View on GitHub](https://github.com/sinonjs/sinon)

* * *

### 用傀儡师拦截请求并伪造请求

假设您想要检查页面上呈现的已保存帖子列表是否正确，给定我们可以从名为`/get_saved_posts`的端点获得的某个用户的已保存帖子列表。要启用木偶师的`requestInterception`，我们只需在启动木偶师
时进行设置

```
await page.setRequestInterceptionEnabled(true); 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们可以设置所有的请求来拦截和模拟响应数据。当然，这需要**知道后端**返回的数据的结构。典型的做法是，将所有伪数据响应对象存储到一个单独的类中，然后在请求拦截时，查询被调用的端点并返回相应的数据。这可以通过使用`page.on()`功能来完成:

##### [💪](#disclaimer-for-the-api-we-assume-the-somewhat-typical-format-raw-httpsapicomv1endpointname-endraw-so-the-parsing-to-retrieve-the-endpoint-is-specific-to-that-format-for-exemplification-purposes-you-can-detect-the-request-made-based-on-other-parameters-of-course-your-choice)

```
const responses = {
  "get_saved_posts": {
    status: 200,
    //Body has to be a string
    body: JSON.stringify({
      data: {
      posts: ["post1","post2"]
      }            
    });
  }
}

page.on('request', interceptedRequest => {
  const endpoint = interceptedRequest.url.split('/').pop();
  if(responses[endpoint]){
    request.respond(responses[endpoint]);
  }else{
    request.continue();
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里看到请求类的完整文档

人们可以很容易地看到，根据您的`API`的大小，这可能相当复杂，而且 e2e 测试的魅力之一是测试后端是否提供了正确的信息和数据😵。

**所有这些方法都要求您输入已知数据，硬编码为响应或变量以进行比较**。获取数据进行比较的另一种方法是截取响应并将数据存储到变量中。

* * *

### 从 [`Response`](https://pptr.dev/#?product=Puppeteer&version=v1.8.0&show=api-class-response) 获取数据进行断言

您还可以截取响应并从中获取数据。例如，我们可以截取`get_saved_posts`响应，并将所有帖子存储到一个变量中。

```
const posts = [];
page.on('response', response => {
    /*
    Grab the response for the endpoint we want and get the data. 
    You could then switch the endpoint and retrieve different data 
    from the API, such as the post id from before, remember? 
    */
    const endpoint = response.url().split('/').pop();
    if(endpoint === "get_saved_posts"){
        const responseBody = await response.json();
        posts = responseBody.data.posts
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

##### ✍🏼注意:在声明页面变量之后，请求和响应的`page.on()`方法通常放在测试的`beforeAll()`方法上，因为它们定义了测试的全局行为。

因此，在应用程序渲染完所有内容后，您可以查询 DOM 元素，然后与`posts`变量进行比较，以查看您的应用程序是否按照预期有效地渲染了所有内容。

* * *

### 总结

在本文中，我们提供了一个全面的概述，介绍了如何使用 puppeter+Jest 测试呈现从服务器获取的异步数据的应用程序。

我们已经学会了如何实现等待某些事件的发生(例如 DOM 突变)，这些事件触发了由异步数据引起的视觉变化。我们已经完成了检测、查询和将这些更改与已知数据进行比较的流程，因此我们可以从用户的角度评估应用程序是否按预期工作。

* * *

###### 有什么问题吗？！走出这里，像一个疯子一样开始实现测试并祈祷它们通过，快乐编码！🙇🏻