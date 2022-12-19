# 一个业余爱好者的冒险~第三部分

> 原文：<https://dev.to/link2twenty/adventures-of-a-hobbyist--part-three-l1m>

# 思考`conf`文件

## 这是什么？

这是我的“学习编码”系列的第三部分，虽然进展缓慢，但所有的进步都是进步。如果你对阅读这个项目感兴趣，这里是这个系列的前两部分:

*   [序言](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-one-2e0n)
*   [学习第一周](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-two-2g5a)

## 什么是 conf 文件？

我有 Linux 和服务器维护的背景，程序拥有包含所有设置的`.conf`文件是很常见的。对于 web 应用程序，我过去总是硬编码数据库的位置，然后将所有其他设置存储在那里。当你想制作开源软件时，这种方法并不真正有效，因为它需要任何人都可以在他们的基础设施中轻松使用。

为此，我开始考虑用 node 搭配`.conf`。我想，这应该很容易，我们已经内置了`fs`，这就是我们所需要的。所以我开始写一些东西来处理读取 conf 文件和导入设置。这就是我的结局。

#### conf 文件

```
"General": {
  "name": "ignis"
},
"MongoDB": {
  "host": "localhost",
  "port": "27017",
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 功能读取它

```
module.exports = {
  "loadConf": async () => {
    const fs = require('fs');
    const ConfFileLoc = "ignis.conf";

    async function getConfFile() {
      return new Promise((resolve) => {
        fs.readFile(ConfFileLoc, (err, data) => {
          if (err) throw err;
          resolve(JSON.parse(`{${data}}`));
        });
      });
    }

    let conf = await getConfFile();
    return conf;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 那么有什么问题呢？

我试图从另一个文件中访问这些数据，因为我认为从任何地方读取`.conf`文件都会有所帮助，但它只会告诉我有一个待定的承诺。我想出了一个办法来解决这个问题，但感觉有点粗糙，我相信有一个更简单的解决方案。

```
const ch = require('./conf_helper');

(async () => {
  let conf = await ch.loadConf()
  console.log(conf)
})() 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我已经将整个部分设置为一个`async`函数，但是除了导入之外，我必须将整个文件放在`async`函数中，这感觉是个坏主意。

## 我想帮忙。

如果你想帮助我，你可以在这个帖子上留下评论，或者回复我关于它的 GitHub 问题。如果我是用一种完全愚蠢的方式做这件事，请随意告诉我，我的目标是学习如何正确地做事情。

## 侧注。

你可能已经注意到我在上面的例子中提到了 MongoDB，我还不确定我是否想使用它。历史上，我一直使用 MySQL，但 MongoDB 被认为是更好的解决方案，所以我正在研究它。如果你有任何关于数据库讨论的意见，GitHub 问题是你应该去的地方。

## 鳍。

非常感谢你读到这里，并在我的学习之旅中与我同行。如果我能做些什么来使这些帖子更有趣/更吸引人，请在评论中告诉我，我真的很感谢所有人的投入，并希望为你、读者以及我自己打造一个好的系列。

再次感谢安德鲁。