# ReactJS 和 AWS Lambda 的脸书邮报预览

> 原文：<https://dev.to/hugodias/facebook-post-preview-with-reactjs-and-aws-lambda-38kk>

我目前专注于学习更多关于 React 的知识。对我来说，最好的学习方法是建立一个不是待办事项的小项目(lol)。

也就是说，我已经决定构建一个小应用程序来接收 URL，获取 meta 标签(尤其是 open graph 标签)并基于 facebook 构建一个预览。

为了完成这项任务，我选择了 ReactJS 和 AWS Lambda(托管在 Netlify 上)。

### λ函数

lambdas 的任务非常简单。接收一个 URL，解析并获取 meta 标签，寻找打开的图形标签。

**open-graph-scraper** npm 包几乎完成了全部工作，所以这里是 lambda:
的最终代码

```
import ogs from "open-graph-scraper";
import getUrl from "get-urls";
import urlParser from "url";

export function handler(event, context, callback) {
  const text = event.queryStringParameters.q;
  const urls = getUrl(text);

  // Return if there is no urls in text
  if (!urls.size) {
    return callback(null, {
      statusCode: 200,
      body: JSON.stringify({
        text: text,
        meta: null,
        error: ["Empty url in text"]
      })
    });
  }

  // Retrieve first URL in text - urls are already normalized
  const url = [...urls][0];
  const options = { url };

  ogs(options, (error, results) => {
    const statusCode = results.success ? 200 : 500;
    callback(null, buildResponseObject(statusCode, results, text));
  });
}

function getUrlDomain(url) {
  const urlObj = urlParser.parse(url);
  return urlObj.host;
}

function cleanText(text) {
  return text.replace(/(?:https?|ftp):\/\/[\n\S]+/g, "");
}

function buildResponseObject(statusCode, result, text) {
  let meta = statusCode === 200 ? result.data : null;

  if (meta) {
    let images = meta.ogImage;

    if (images instanceof Array) {
      meta.ogImage = images[0];
    }

    let domain = meta.ogUrl;

    if (domain) {
      meta.ogUrl = getUrlDomain(meta.ogUrl);
    }
  }

  const body = {
    meta: meta,
    text: cleanText(text),
    error: statusCode !== 200 ? result.error : null
  };

  return {
    statusCode,
    body: JSON.stringify(body)
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

### 前端

正如我在文章开头提到的，react 被选择来构建客户端接口。

使用的软件包有:

```
 "dependencies":  {  "get-urls":  "^7.2.0",  "netlify-lambda":  "^0.4.0",  "open-graph-scraper":  "^3.3.0",  "react":  "^16.4.1",  "react-content-loader":  "^3.1.2",  "react-dom":  "^16.4.1",  "react-scripts":  "1.1.4",  "styled-components":  "^3.3.2"  }, 
```

Enter fullscreen mode Exit fullscreen mode

这是结果:

[![screenshot](../Images/50a7c50442476036ba1f8651529ff32c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M0n_imO3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pxfetg8lcuy3fr36i8n7.png)

### 投稿

它是免费和开源的！

代码[托管在 Github](https://github.com/hugodias/facebook-post-preview) 上，你可以[在这里](https://facebook-preview.netlify.com)看到它的运行。

我期待着你们的反馈，伙计们。

谢谢