# 从 HTML 生成简单的 PDF

> 原文：<https://dev.to/chuongtrh/generate-simple-pdf-from-html-41i0>

## html_to_pdf

使用[木偶师](https://github.com/GoogleChrome/puppeteer) & [手柄](http://handlebarsjs.com/)从 HTML 生成简单的 PDF 发票

[![Invoice](img/515d9fa35eb8efa7aa66e37b644b98fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ux4d0kvB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/chuongtrh/html_to_pdf/master/screenshot/invoice.png)

## 介绍

*   [木偶师](https://github.com/GoogleChrome/puppeteer)

    > Puppeteer 是 Node.js 库，可以让你访问一个无头的 Chrome 浏览器。这使得用 Node.js 生成 PDF 文件变得轻而易举

*   [车把](http://handlebarsjs.com/)

    > Handlebars 提供了必要的能力，让您可以有效地构建语义模板，而不会感到沮丧

## 如何使用

*   运行`npm install`在 package.json 中安装包
*   运行`node pdf.js`生成 invoice.pdf

## 来自 HTML 的 PDF 发票

1.  准备内容 html(invoice.html)
2.  使用手柄将数据绑定到内容 html
3.  使用 Puppeteer 从最终的 html 生成 pdf

```
const fs = require("fs");
const path = require("path");
const puppeteer = require('puppeteer');
const handlebars = require("handlebars");

(async () => {

    var dataBinding = {
        items: [{
                name: "item 1",
                price: 100
            },
            {
                name: "item 2",
                price: 200
            },
            {
                name: "item 3",
                price: 300
            }
        ],
        total: 600,
        isWatermark: false
    }

    var templateHtml = fs.readFileSync(path.join(process.cwd(), 'invoice.html'), 'utf8');
    var template = handlebars.compile(templateHtml);
    var finalHtml = template(dataBinding);
    var options = {
        format: 'A4',
        headerTemplate: "<p></p>",
        footerTemplate: "<p></p>",
        displayHeaderFooter: false,
        margin: {
            top: "40px",
            bottom: "100px"
        },
        printBackground: true,
        path: 'invoice.pdf'
    }

    const browser = await puppeteer.launch({
        args: ['--no-sandbox'],
        headless: true
    });
    const page = await browser.newPage();
    await page.goto(`data: text/html,${finalHtml}`, {
        waitUntil: 'networkidle0'
    });
    await page.pdf(options);
    await browser.close();
})(); 
```

Enter fullscreen mode Exit fullscreen mode

## 如何在发票上显示已支付的印花水印？

使用手柄检查参数`isWatermark`

```
 {{#if isWatermark}}
    <div style="border-width: 6px;border-style: solid; border-color: #008000;border-radius: 8px; color: #008000; opacity:0.6; position: absolute; z-index: 1; left:40%; top:30%; font-size: 60pt;-webkit-transform: rotate(-45deg);-ms-transform: rotate(-45deg);transform: rotate(-45deg); font-family: 'Helvetica Neue', 'Helvetica', Helvetica, Arial, sans-serif;">
        PAID </div>
    {{/if}} 
```

Enter fullscreen mode Exit fullscreen mode

改变`isWatermark: true`并再次运行`node pdf.js`
[![Invoice with stamp paid watermark](img/5a558d7d16777eed0de787d0aa54bfe7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CSidnRH0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/chuongtrh/html_to_pdf/master/screenshot/invoice_paid.png)

你可以在 [Github](https://github.com/chuongtrh/html_to_pdf) 找到源代码

感谢你花时间阅读这篇文章

PS:/这是 dev.to 上的第一篇帖子