# 将谷歌云功能用作谷歌浏览器

> 原文：<https://dev.to/_mertsimsek/to-use-google-cloud-function-as-google-chrome-3e7e>

谷歌几天前宣布:我们可以把谷歌云功能当做 Chromium。为此，我们在 Google Cloud 函数上使用 Node.js 8 运行时。让我们在 Google Cloud 上创建一个函数。点击**创建功能**按钮。

[![alt text](img/53b98ff1f2647b882bbedfa4101633f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FArFkYfD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ar1nirp3k1ad7d96h97j.png)

按照顺序，我们来填空。

*   设置名称。
*   将内存设置为少 1 GB。
*   我们的触发器是 http。
*   我内嵌了源代码。
*   可以选择 Python，Node.js 6，Node.js 8。选择 Node.js 8
*   将截图设置为执行名称的函数

[![alt text](img/3feff5379246da4ab3e56ea9dc3e9f95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---MUOpIsw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zx54vjqkd3qge10718ig.png)

然后，我们把我们的 **index.js** 写成如下。

```
const puppeteer = require('puppeteer');
let page;

async function getBrowserPage() {
  // Launch headless Chrome. Turn off sandbox so Chrome can run under root.
  const browser = await puppeteer.launch({args: ['--no-sandbox']});
  return browser.newPage();
}

exports.screenshot = async (req, res) => {
  const url = req.query.url;

  if (!url) {
    return res.send('Url is not found!');
  }

  if (!page) {
    page = await getBrowserPage();
  }

  await page.goto(url);
  const imageBuffer = await page.screenshot();
  res.set('Content-Type', 'image/png');
  res.send(imageBuffer);
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这之后，定义 **package.json** 。

```
{ 
  "name": "screenshot",
  "version": "0.0.1",
  "dependencies": { 
    "puppeteer": "^1.6.2" 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为此，我们使用了**木偶师**库。单击表单下方的按钮，函数创建完成。从现在开始，我们可以触发我们的网址。创建函数时，您将看到此页面。

[![alt text](img/d26549305bb9cdf96d643775d89fe03d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QtHBErTK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qd71nvvj6cgxrtsaigav.png)

如果你点击这个网址，你会得到一个错误。

[![alt text](img/3b21668343fd4a81c7cd9b0f157d265d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mkKZh-R7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/75xtfqlx9j74gb0xoi3o.png)

现在，为 url 添加查询字符串参数。如下图所示。

[![alt text](img/446d0823c56d2a97bd5cf3a5636f10cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qcx1kHbd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p6hvunfzsoqpf3udz7ge.png)

太酷了。当然，你可以得到整页或者你可以得到页面的标题。你可以用这种方式操作 DOM。你应该检查木偶库[https://github.com/GoogleChrome/puppeteer](https://github.com/GoogleChrome/puppeteer)的 api。