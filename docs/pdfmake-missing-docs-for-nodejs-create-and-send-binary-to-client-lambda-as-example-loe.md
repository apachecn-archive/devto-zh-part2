# ` pdf make ` node . js 的缺失文档创建并向客户端发送二进制文件(以 Lambda 为例)

> 原文：<https://dev.to/pspegg/pdfmake-missing-docs-for-nodejs-create-and-send-binary-to-client-lambda-as-example-loe>

为什么我需要这个来通过 Twilio 的传真 API 发送纯文本传真，这需要一个 PDF 链接。

为什么是`pdfmake`？为什么不在将任何网页渲染成 PDF 的服务器/lambda 上使用`puppeteer`？`pdfmake`的布局语言对页面文档有更多的功能，我不需要 html+css+js 提供的花哨东西。`pdfmake`有表格和列以及良好的字体支持。使用 html 打印布局后，我知道这有多痛苦。

在文档上

首先，`pdfmake`是，我相信围绕着`pdfkit`的一个包装。`pdfkit`除了一栏式的东西之外，没有任何方便的功能来生成文档。`pdfmake`可以做很多，但是 API 文档不存在。

发现特性和用法的最佳地方是 [README](https://github.com/bpampuch/pdfmake/blob/master/README.md) 和[游乐场](http://pdfmake.org/playground.html)。两者都不全面。

这些文档还假设使用浏览器。服务器端非常不同。以下是我从各种 Github 问题和过去使用`pdfkit`的经验中收集到的信息，它也没有全面的文档。这个脚本创建了一个 pdf 格式的二进制文件，您可以将它直接发送给客户端。

您需要从 google fonts 下载 Roboto fonts，并将其放在脚本的项目文件夹中，然后在下面代码中的`fonts`对象中引用它们。

Roboto 是默认使用的字体，所以如果你在没有指定任何字体的情况下生成一个 pdf 文档，它就会使用这种字体。所以有了 Roboto 就避免了没有后备字体带来的所有错误。

```
import {Base64Encode} from 'base64-stream'
const PdfPrinter = require('pdfMake/src/printer')
const fonts = {
      Roboto: {
          normal: './fonts/Roboto-Medium.ttf',
          bold: './fonts/Roboto-Black.ttf',
          italics: './fonts/Roboto-MediumItalic.ttf',
          bolditalics: './fonts/Roboto-BlackItalic.ttf'
      }
    };
const printer = new PdfPrinter(fonts)
const docDef = {content: 'Hello World'}
const doc = printer.createPdfKitDocument(docDef)
const stream = doc.pipe(new Base64Encode())
doc.end()
let finalString = ''
stream.on('data', function(chunk) {
  finalString += chunk;
});
stream.on('end', function() {
  console.log('binary pdf: ', finalString)
}); 
```

Enter fullscreen mode Exit fullscreen mode

对于 express 服务器，只需在`body`中发送带有 finalString 的 respond，并将`Content-Type`设置为`application/pdf`。还需要帮助吗？搜索“在 express 中用 pdf 二进制文件响应”。有很多解决方法。

我通过[无服务器 nodejs 入门套件](https://github.com/AnomalyInnovations/serverless-nodejs-starter.git)在 AWS Lambda 上使用它。它不能与回调一起工作，只是承诺(可能有一个解决方案，但你不需要回调)。你可以用一个新的`Promsise`
来包装`stream.on`

```
return new Promise((resolve, reject) => {
  stream.on('end', function() {
    resolve(finalString)
  });
}) 
```

Enter fullscreen mode Exit fullscreen mode

另外，AWS Lambda 默认不支持二进制响应，所以你需要一个名为`serverless-apigw-binary`的插件。但是插件没有 PDF 二进制的说明，是:

```
custom:
  apigwBinary:
    types:  
      - '*/*' 
```

Enter fullscreen mode Exit fullscreen mode

由于我不知道的原因，让`application/pdf`作为类型在这里不起作用。

这种配置可能会导致正常 lambda 函数的错误(例如，CORS 不适用于您的其他函数，我不知道 pdf 函数本身，您可以测试)。因此，在同一个项目中，不应该有其他处理文本响应的函数。根本原因是同一项目中的所有函数共享一个 API 网关，该网关现在被配置为将所有内容类型视为二进制。

这是我需要做的项目所有丢失的文件。

联系我，如果你想要一个工作的例子。