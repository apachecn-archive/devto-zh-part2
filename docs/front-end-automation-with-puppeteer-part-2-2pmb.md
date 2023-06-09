# 使用木偶师实现前端开发自动化。第二部分

> 原文：<https://dev.to/papaponmx/front-end-automation-with-puppeteer-part-2-2pmb>

[链接到第 1 部分](https://dev.to/papaponmx/front-end-development-automation-with-puppeteer-part-1-2k9n)

## 介绍段落

正如我以前告诉过你的，这个系列的灵感来自于我作为前端开发人员在日常工作中遇到的问题。以下是我的创意解决方案。我知道我不应该重复我自己，但这里是回购 T3 的 **[链接。](https://github.com/papaponmx/puppeteer-in-the-wild)**

在上一节中，我们等待选择器出现在屏幕上，点击并输入表单。

在本系列的这一部分中，我们将向场景中引入两个新变量。

## 场景 2:有东西停止工作了，你能看看吗？

受到上周出现的东西的启发。有一个内部 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 应用程序。

后端修复了一个令人讨厌的错误，这需要从前端删除不必要的逻辑。使用一个类似于本系列第 1 部分的脚本，我们再次检查了 CRUD 操作仍然以它们应该的方式发生。

但是，在网络请求成功完成后，其中一个参数没有显示在屏幕上。代码看起来像这样。

```
 <a>{ env.base_url + broken_reference } </a> 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

我放置了一个`debugger`，就在断开的链接被渲染之前。然后调整了脚本，做了两件事:

1.  打开 head-full Chrome 实例。也就是说，一个带有
2.  在开发者工具打开的情况下做，这样`[debugger statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/debugger)`实际上就可以工作了。

让我们浏览一下解决方案。[puppeter launch](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#puppeteerlaunchoptions)方法将一个对象作为参数，我们可以在这个对象中覆盖一些默认选项。

这在以下情况下很有用:

*   当我们需要一个特定的视窗时，想想移动设备没有显示的东西。
*   为了捕捉一些错误，假设一个网络请求没有按预期工作。
*   您需要打开开发人员工具，就像上面描述的场景。
*   您需要传递环境变量。
*   您希望启用一些扩展。

下面是它在代码中的样子:

```
 // Ideally this lives in another file
const options = {
    devtools: true,
    headless: false,
    viewport: {
      width: 1920,
      height: 1080,
    },
    args: [
      '--disable-extensions-except=/path/to/extension/',
      '--load-extension=/path/to/extension/',
    ],
}

// ... within an async function

const browser = await puppeteer.launch(options); 
```

Enter fullscreen mode Exit fullscreen mode

下面是在单个文件中运行的测试，以及到 repo 的[链接。](https://github.com/papaponmx/puppeteer-in-the-wild) 

```
 const fs = require('fs'); // Nodes File System
const puppeteer = require('puppeteer'); // High level API to interact with headless Chrome
const signale = require('signale');
const locators = require('./locators'); // A JSON with all the CSS locators we need.
const config = require('./config'); // Some project variables and the browser options

const {options} = config;
// 1\. Open browser.
const runTest = async (params) => {
  signale.debug('Opening browser...');
  const browser = await puppeteer.launch(options);
  const page = await browser.newPage();
  const d = new Date();
  const dateString = `${d.getDate()}_${d.getHours()}h${d.getMinutes()}`;
  const userName = `USER_FROM_TESTING_SCRIPT_${dateString}`;

  // 2\. Go to the website;
  await signale.watch('Navigating to the site 🚢');
  await page.goto(LOCAL_HOST_URL);
  await signale.watch('Filling up the form 🙌');
  await signale.success('Everything is working as expected ✅');

};

runTest(); 
```

Enter fullscreen mode Exit fullscreen mode

伙计们，现在就到这里吧。以下是我将在接下来的帖子中介绍的内容:

场景 3:比较本地快照和测试快照。
场景 4:无头，用户手动输入。