# 带有 AWS Lambda、Node.js 8.10 和标准 Http 库的 http 请求

> 原文：<https://dev.to/scottlepp/http-request-with-aws-lambda-nodejs-810-and-standard-http-library-2cee>

在我之前的文章中，我展示了一种使用 AWS Lambda:[https://dev . to/scottlepp/extending-traditional-software-with-servers-micro services-442m](https://dev.to/scottlepp/extending-traditional-software-with-serverless-microservices-442m)扩展“传统”(单片架构)应用的方法

让我们仔细看看 Lambda 函数，以及如何使用 Node.js 8.10 和标准 http 库发出 http 请求。

在 node.js 的早期版本中，处理函数包含一个回调参数，如下所示:

```
exports.handler = function (event, context, callback) { 
```

Enter fullscreen mode Exit fullscreen mode

当您的 http 请求完成时，您将执行回调来指示该函数已经完成:

```
const req = http.request(options, (res) => {
   callback('Success');
}); 
```

Enter fullscreen mode Exit fullscreen mode

然而，即使是旧版本的 node.js 也没有回调函数，你可以像这样使用“context . success”:

```
const req = http.request(options, (res) => {
   context.succeed();
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是，在 node.js 8.10 中，这种情况发生了变化。再次不需要回调参数。现在，您只需包装返回承诺的函数。然后不执行回调函数，而是执行 Promise resolve 函数(或者 reject 函数，如果它失败的话)，如下:

```
const http = require('http');

exports.handler = async (event, context) => {

    return new Promise((resolve, reject) => {
        const options = {
            host: 'ec2-18-191-89-162.us-east-2.compute.amazonaws.com',
            path: '/api/repos/r1639420d605/index?delta=true&clear=false',
            port: 8000,
            method: 'PUT'
        };

        const req = http.request(options, (res) => {
          resolve('Success');
        });

        req.on('error', (e) => {
          reject(e.message);
        });

        // send the request
        req.write('');
        req.end();
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！node.js 版本之间的这些变化让我有点困惑，所以我想分享一下最新的方法。希望这对某人有所帮助！