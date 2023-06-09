# weekly random # 1:sindresorhus/ua-string

> 原文：<https://dev.to/vintagesucks/weeklyrandom-1-sindresorhusua-string-2bkg>

*欢迎来到 [**WeeklyRandom**](https://dev.to/t/weeklyrandom) ，这是一个博客系列，在这个系列中，我挑战自己，每周探索一个由我主演的随机 GitHub 知识库。*

第一周的条目本应在上周发布，但《生命》《T2》【红色死亡救赎 2】阻挠了我的计划。🤠

没关系！让我们看看这个星期我可以探索哪种回购。

🥁

```
node index.js vintagesucks
https://github.com/sindresorhus/ua-string 
```

[ua-string](https://github.com/sindresorhus/ua-string) 作者[辛德雷·索胡斯](https://github.com/sindresorhus)。这不会花很长时间。回购的描述如下:

> 让 Chrome 最新版本的用户代理在网络请求中伪装成浏览器

好吧。让我们试一个要求去[whatismybrowser.com](https://www.whatismybrowser.com/)而不去`ua-string` :

```
// index.js
const got = require('got');
const HTMLParser = require('fast-html-parser');

(async () => {
    try {
        got('https://developers.whatismybrowser.com/useragents/parse/?analyse-my-user-agent=yes')
        .then((res) =>
            HTMLParser.parse(res.body).querySelector('.useragent')
        )
        .then((result) =>
            console.log(result.childNodes[0].rawText)
        );
    } catch (error) {
        console.log(error.response.body);
    }
})(); 
```

让我们运行它:

```
node index.js
got/9.2.2 (https://github.com/sindresorhus/got) 
```

自从我用了 [`got`](https://github.com/sindresorhus/got) (由[辛德雷创造的](https://github.com/sindresorhus)也是👋)提出请求，被报告的用户代理是`got`。

现在让我们试试`ua-string`。

```
npm install ua-string 
```

这个脚本，现在使用`ua-string` :

```
const got = require('got');
const uaString = require('ua-string');
const HTMLParser = require('fast-html-parser');

(async () => {
    try {
        got('https://developers.whatismybrowser.com/useragents/parse/?analyse-my-user-agent=yes', {
            headers: {
                'user-agent': uaString
            }
        })
        .then((res) =>
            HTMLParser.parse(res.body).querySelector('.useragent')
        )
        .then((result) =>
            console.log(result.childNodes[0].rawText)
        );
    } catch (error) {
        console.log(error.response.body);
    }
})(); 
```

开始了:

```
node index.js
Mozilla/5.0 (Macintosh; Intel Mac OS X 10\_13\_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.75 Safari/537.36 
```

完美。🎉

你可以在 GitHub 的[vintage sucks/weekly random](https://github.com/vintagesucks/weeklyrandom/tree/master/sindresorhus/ua-string)找到完整的代码。非常欢迎有改进的拉动请求！

直到下周，我将检查我的下一个 [*WeeklyRandom*](https://dev.to/t/weeklyrandom) 库。