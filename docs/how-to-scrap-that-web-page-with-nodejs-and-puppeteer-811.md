# 如何用 Node.js 和木偶师抓取那个网页

> 原文：<https://dev.to/napolux/how-to-scrap-that-web-page-with-nodejs-and-puppeteer-811>

如果你像我一样**有时你非常想抓取网页**。您可能想要一些可读格式的数据，或者只是需要一种方法来重新处理这些数据以用于其他目的。

> 我庄严宣誓我不怀好意。

在用 Guzzle、BeautifulSoup 等进行了多次尝试后，我找到了我的最佳设置...这是:

*   节点. js
*   木偶师:检查[https://github.com/GoogleChrome/puppeteer](https://github.com/GoogleChrome/puppeteer)
*   一个小树莓 Pi，我的脚本可以运行一整天。

Puppeteer 是一个节点库，它提供了一个高级 API 来控制 Chrome 或 DevTools 协议上的 Chrome。默认情况下，Puppeteer 是无头运行的，但是可以配置为运行完整的(非无头)Chrome 或 Chrome。

**这是什么意思？这意味着你可以运行一个 Chrome 实例，并为你服务。很酷，不是吗？**

我们来看看怎么做。

### 设置

是的，通常的设置。启动您的终端，为您的项目创建一个文件夹，并在文件夹中运行`npm init`。

当你设置的时候，你可能会有一个`package.json`文件。我们准备好了。现在运行`npm i -S puppeteer`来安装木偶师。

*小小警告。木偶师会在你的`node_modules`文件夹*中下载完整版的 Chromium

别担心:从版本`1.7.0`开始，谷歌发布了`puppeteer-core`包，这是一个默认情况下不下载 Chromium 的版本。

因此，如果您愿意尝试，只需运行`npm i -S puppeteer-core`

> 是一个轻量级版本的木偶师，用于启动现有的浏览器安装或连接到远程浏览器。

好了，我们可以走了。

### 你的第一个刮刀

触摸项目文件夹中的一个`index.js`文件，将这段代码粘贴到其中。