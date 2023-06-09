# 使用 Node.js 解析 JSON

> 原文：<https://dev.to/flaviocopes/parsing-json-with-nodejs-1i3m>

如果你有 JSON 数据作为字符串的一部分，解析它的最好方法是使用从 ECMAScript 5 开始就是 JavaScript 标准的一部分的`JSON.parse`方法，它是由支持 [Node.js](https://flaviocopes.com.com/nodejs/) 的 JavaScript 引擎 [V8](https://flaviocopes.com.com/v8/) 提供的。

示例:

```
const data = '{ "name": "Flavio", "age": 35 }'
try {
  const user = JSON.parse(data)
} catch(err) {
  console.error(err)
} 
```

注意`JSON.parse`是同步的，所以 JSON 文件越大，程序执行被阻塞的时间就越长，直到 JSON 完成解析。

没有办法异步解析 JSON 文件。

如果您的 JSON 在一个文件中，您首先必须读取它。

一个非常简单的方法是使用`require()`:

```
const data = require('./file.json') 
```

由于您使用了`.json`扩展，`require()`足够聪明，能够理解这一点，并解析`data`对象中的 JSON。

一个警告是文件读取是同步的。另外，require()调用的结果会被缓存，所以如果因为更新了文件而再次调用它，在程序退出之前不会获得新的内容。

提供这个特性是为了将 JSON 文件用于应用程序配置，这是一个非常有效的用例。

您也可以使用`fs.readFileSync`手动读取文件:

```
const fs = require('fs')
const fileContents = fs.readFileSync('./file.json', 'utf8')

try {
  const data = JSON.parse(fileContents)
} catch(err) {
  console.error(err)
} 
```

这将同步读取文件。

还可以使用`fs.readFile`异步读取文件，这是最好的选择。在这种情况下，文件内容作为回调提供，在回调中您可以处理 JSON:

```
const fs = require('fs')

fs.readFile('/path/to/file.json', 'utf8', (err, fileContents) => {
  if (err) {
    console.error(err)
    return
  }
  try {
    const data = JSON.parse(fileContents)
  } catch(err) {
    console.error(err)
  }
}) 
```