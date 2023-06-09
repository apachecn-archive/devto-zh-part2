# 让我们制作一个开发工具...一起

> 原文：<https://dev.to/joelnet/lets-make-a-devto-cli-together-4eh1>

为了 hacktoberfest 我要为 DEV.to 做一个 CLI...让我们一起努力吧！

[![devto CLI demo](img/a9149e5e001897bf496f364868d9ebe0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aorzlKIi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/freiusj8pih8h136g1u9.gif)

这是一个后续类型的教程...所以跟着走。但是如果你觉得自己太优秀了，学不到很酷的东西，你可以直接跳到最后。

如果我跳过的太快了，你想要更多的解释，可以在评论里问我！

# 设置

因为我是开车的人，所以我有权选择语言。我将使用 [MojiScript](https://github.com/joelnet/MojiScript) (当然)。

> ![Programming Wisdom profile image](img/c0e04370043baff7812aa9d1c701cd8f.png)编程智慧[@ code Wisdom](https://dev.to/codewisdom)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)“学习一门新编程语言的唯一方法就是用它编写程序。”——丹尼斯里奇2018 年 10 月 11 日下午 12:37[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1050364645050122241)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1050364645050122241)307[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1050364645050122241)1034

```
git clone https://github.com/joelnet/mojiscript-starter-app.git devto-cli
cd devto-cli
npm ci 
```

DEV.to 没有 API，那么所有没有 API 的网站会怎么样呢？他们被刮伤了！

```
# install axios
npm install --save-prod axios 
```

将 axios 依赖项添加到`index.mjs`

```
import log from 'mojiscript/console/log'
import run from 'mojiscript/core/run'
import axios from 'mojiscript/net/axios'
import main from './main'

const dependencies = {
  axios,
  log
}

run ({ dependencies, main }) 
```

# 创建 src/api.mjs

创建一个新文件`src/api.mjs`来包含我们的抓取 API。我们用的是`mojiscript/net/axios`，它是`axios`的咖喱版。

```
import pipe from 'mojiscript/core/pipe'

const getData = response => response.data

export const getUrl = axios => pipe ([
  url => axios.get (url) ({}),
  getData
])

export const getDevToHtml = axios => pipe ([
  () => getUrl (axios) ('https://dev.to')
]) 
```

将`getDevToHtml`导入`main.mjs`

```
import pipe from 'mojiscript/core/pipe'
import { getDevToHtml } from './api'

const main = ({ axios, log }) => pipe ([
  getDevToHtml (axios),
  log
])

export default main 
```

现在运行代码:

```
npm start 
```

如果一切顺利，你应该会看到一堆 HTML 充斥着控制台。

# JavaScript 互操作

现在，我不想每次调试代码时都用 HTTP 调用来攻击 DEV.to，所以让我们将输出缓存到一个文件中。

```
# this will get you the same version in this tutorial
curl -Lo devto.html https://raw.githubusercontent.com/joelnet/devto-cli/master/devto.html 
```

接下来，我将创建一个文件`interop/fs.mjs`，这就是`fs.readFile`所在的位置。我将它放在一个`interop`文件夹中，因为 MojiScript 需要将 JavaScript 互操作文件放在这里。JavaScript 的编写方式不同于 MojiScript，有时是不兼容的(除非在 interop 目录中)。

为了使`fs.readFile`与 MojiScript 兼容，我需要首先对它进行`promisify`。

```
promisify (fs.readFile) 
```

既然承诺了，我也要讨好一下。

```
export const readFile = curry (2) (promisify (fs.readFile)) 
```

我也在处理 UTF8，那就加一个助手吧，方便一下。

```
export const readUtf8File = file => readFile (file) ('utf8') 
```

还有完整的`interop/fs.mjs` :

```
import fs from 'fs'
import curry from 'mojiscript/function/curry'
import { promisify } from 'util'

export const readFile = curry (2) (promisify (fs.readFile))

export const readUtf8File = file => readFile (file) ('utf8') 
```

# 读取缓存

在`src/mocks/axios.mock.mjs`内部，我要创建`mockAxios`。这将在调用`get`时返回我们文件的内容。

```
import pipe from 'mojiscript/core/pipe'
import { readUtf8File } from '../interop/fs'

const mockAxios = {
  get: () => pipe ([
    () => readUtf8File ('devto.html'),
    data => ({ data })
  ])
}

export default mockAxios 
```

使用模拟很容易。我所要做的就是改变`dependencies`。`main.mjs`没有什么需要改变的！

```
// don't forget to add the import!
import mockAxios from './mocks/axios.mock'

const dependencies = {
  axios: mockAxios,
  log
} 
```

现在，当我们运行`npm start`时，没有发出 HTTP 请求。这很好，因为在我完成这件事之前，我可能要跑一大堆。

# 解析 HTML

我喜欢`cheerio`解析。我很确定这是酷小孩用的。

```
npm install --save-prod cheerio 
```

创建另一个互操作`interop/cheerio.mjs`。

```
import cheerio from 'cheerio';
import pipe from 'mojiscript/core/pipe';
import map from 'mojiscript/list/map';

export const getElements = selector => pipe ([
  cheerio.load,
  $ => $ (selector),
  $articles => $articles.toArray (),
  map (cheerio)
]) 
```

注意:当 cheerio 的`toArray`被调用时，元素会丢失所有这些好的 cheerio 方法。只好将`map` `cheerio`重新搬上所有的元素。

接下来将`getElements`添加到`main`中。

```
import { getElements } from './interop/cheerio'

const main = ({ axios, log }) => pipe ([
  getDevToHtml (axios),
  getElements ('.single-article:not(.feed-cta)'),
  log
]) 
```

再次运行`npm start`查看元素数组。

```
npm install --save-prod reselect nothis 
```

创建`interop/parser.mjs`。我将使用`reselect`从 HTML 中选择我需要的属性。我不打算详细讨论这个。基本上就是从一个元素中获取一大堆东西。代码很好读，你也可以跳过，不重要。

```
import reselect from 'reselect'
import nothis from 'nothis'

const { createSelector } = reselect
const isTextNode = nothis(({ nodeType }) => nodeType === 3)

const parseUrl = element => `http://dev.to${element.find('a.index-article-link').attr('href')}`
const parseTitle = element => element.find('h3').contents().filter(isTextNode).text().trim()
const parseUserName = element => element.find('.featured-user-name,h4').text().trim().split('・')[0]
const parseTags = element => element.find('.featured-tags a,.tags a').text().substr(1).split('#')
const parseComments = element => element.find('.comments-count .engagement-count-number').text().trim() || '0'
const parseReactions = element => element.find('.reactions-count .engagement-count-number').text().trim() || '0'

export const parseElement = createSelector(
  parseUrl,
  parseTitle,
  parseUserName,
  parseTags,
  parseComments,
  parseReactions,
  (url, title, username, tags, comments, reactions) => ({
    url,
    title,
    username,
    tags,
    comments,
    reactions
  })
) 
```

将`parseElement`加到`main`上。

```
import map from 'mojiscript/list/map'
import { parseElement } from './interop/parser'

const main = ({ axios, log }) => pipe ([
  getDevToHtml (axios),
  getElements ('.single-article:not(.feed-cta)'),
  map (parseElement),
  log,
]) 
```

现在，当您运行`npm start`时，您应该会看到类似这样的内容:

```
[
  { url:
     'http://dev.to/ccleary00/how-to-find-the-best-open-source-nodejs-projects-to-study-for-leveling-up-your-skills-1c28',
    title:
     'How to find the best open source Node.js projects to study for leveling up your skills',
    username: 'Corey Cleary',
    tags: [ 'node', 'javascript', 'hacktoberfest' ],
    comments: '0',
    reactions: '33' } ] 
```

# 格式化数据

增加`import`、`formatPost`并将`formatPost`增加到`main`并将`log`改为`map (log)`。

```
import $ from 'mojiscript/string/template'

const formatPost = $`${'title'}  ${'url'}\n#${'tags'}  ${'username'} ・ 💖 ${'comments'} 💬 ${'reactions'} `

const main = ({ axios, log }) => pipe ([
  getDevToHtml (axios),
  getElements ('.single-article:not(.feed-cta)'),
  map (parseElement),
  map (formatPost),
  map (log)
]) 
```

再次运行`npm start`，您应该会看到一些记录，如下所示:

```
The Introvert's Guide to Professional Development
http://dev.to/geekgalgroks/the-introverts-guide-to-professional-development-3408
#introvert,tips,development,professional
Jenn ・ 💖  1 💬  50 
```

终于，这开始有点眉目了！

我还将在`main.mjs`中添加一个条件，只有在`NODE_ENV`中设置了`production`时才使用`axios`。

```
import ifElse from 'mojiscript/logic/ifElse'

const isProd = env => env === 'production'
const getAxios = () => axios
const getMockAxios = () => mockAxios

const dependencies = {
  axios: ifElse (isProd) (getAxios) (getMockAxios) (process.env.NODE_ENV),
  log
} 
```

在有和没有`production`的情况下运行它，以确保两者都在工作。

```
# dev mode
npm start

# production mode
NODE_ENV=production npm start 
```

# 查看文章

这个列表很好，我正打算在这里停下来，但如果我也能读这篇文章，那就太酷了。

我希望能够像这样输入:

```
devto read 3408 
```

我注意到 url 的末尾有一个我可以使用的 ID:`http://dev.to/geekgalgroks/the-introverts-guide-to-professional-development-3408`<-就在那里。

所以我将修改`parser.mjs`来包含一个新的解析器来获取 id。

```
const parseId = createSelector(
  parseUrl,
  url => url.match(/-(\w+)$/, 'i')[1]
) 
```

然后只要按照模式把`parseId`变成`parseElement`就可以了。

现在 CLI 将有两个分支，一个显示提要，另一个显示文章。因此，让我们将提要逻辑从`main.mjs`分解到`src/showFeed.mjs`。

```
import pipe from 'mojiscript/core/pipe'
import map from 'mojiscript/list/map'
import $ from 'mojiscript/string/template'
import { getDevToHtml } from './api'
import { getElements } from './interop/cheerio'
import { parseElement } from './interop/parser'

const formatPost = $`${'title'}  ${'url'}\n#${'tags'}  ${'username'} ・ 💖 ${'comments'} 💬 ${'reactions'} `

export const shouldShowFeed = args => args.length < 1

export const showFeed = ({ axios, log }) => pipe ([
  getDevToHtml (axios),
  getElements ('.single-article:not(.feed-cta)'),
  map (parseElement),
  map (formatPost),
  map (log)
]) 
```

接下来，我要把`cond`包在`showFeed`上。有可能我们会有更多的分支机构(也许帮助？)中，但目前我们只有 1 条路径。

这就是`main.mjs`现在应该的样子。

```
import pipe from 'mojiscript/core/pipe'
import cond from 'mojiscript/logic/cond'
import { showFeed } from './showFeed'

const main = dependencies => pipe ([
  cond ([
    [ () => true, showFeed (dependencies) ]
  ])
])

export default main 
```

我们需要访问节点的参数。所以做出这些改变。我对它们做了一个`slice`,因为前两个参数是垃圾参数，我不需要它们。

```
// add this line
const state = process.argv.slice (2)

// add state to run
run ({ dependencies, state, main }) 
```

好了，在我们真正看到这篇文章之前，我们还有很多工作要做。所以让我们添加帮助。这很简单。

# 查看帮助

创建`src/showHelp.mjs`。

```
import pipe from 'mojiscript/core/pipe'

const helpText = `usage: devto [<command>] [<args>]

  <default>
    Show article feed
  read <id>    Read an article
`

export const showHelp = ({ log }) => pipe ([
  () => log (helpText)
]) 
```

现在我们可以简化`main.mjs`并将新案例添加到`cond`中。

```
import pipe from 'mojiscript/core/pipe'
import cond from 'mojiscript/logic/cond'
import { shouldShowFeed, showFeed } from './showFeed'
import { showHelp } from './showHelp'

const main = dependencies => pipe ([
  cond ([
    [ shouldShowFeed, showFeed (dependencies) ],
    [ () => true, showHelp (dependencies) ]
  ])
])

export default main 
```

现在，如果我们运行`npm start -- help`，我们应该会看到我们的帮助:

```
usage: devto [<command>] [<args>]

  <default>    Show article feed
  read <id>    Read an article 
```

如果我们运行`npm start`我们仍然应该看到我们的饲料！

# 文章来自缓存

就像我从缓存中读取主提要一样，我也想从缓存中读取文章。

```
curl -Lo article.html https://raw.githubusercontent.com/joelnet/devto-cli/master/article.html 
```

修改`axios.mock.mjs`也阅读文章。

```
import pipe from 'mojiscript/core/pipe'
import ifElse from 'mojiscript/logic/ifElse'
import { readUtf8File } from '../interop/fs'

const feedOrArticle = ifElse (url => url === 'https://dev.to') (() => 'devto.html') (() => 'article.html')

const mockAxios = {
  get: url => pipe ([
    () => feedOrArticle (url),
    readUtf8File,
    data => ({ data })
  ])
}

export default mockAxios 
```

# 解析文章

解析文章 HTML 要容易得多，因为我打算将整个`article-body`块格式化为文本。所以我只需要标题和正文。

创建`interop/articleParser.mjs`。

```
import reselect from 'reselect'

const { createSelector } = reselect

const parseTitle = $ => $('h1').first().text().trim()
const parseBody = $ => $('#article-body').html()

export const parseArticle = createSelector(
  parseTitle,
  parseBody,
  (title, body) => ({
    title,
    body
  })
) 
```

# 阅读文章

因为没有状态，所以当我发出`read`命令时，CLI 不会知道要拉什么 URL。因为我很懒，所以我将再次查询提要。并从提要中提取 URL。

所以我将跳回`showFeed.mjs`并展示这个功能。

我只是从`showFeed`中提取函数，并将它们放入`getArticles`。我没有在这里添加任何新代码。

```
export const getArticles = axios => pipe ([
  getDevToHtml (axios),
  getElements ('.single-article:not(.feed-cta)'),
  map (parseElement)
])

export const showFeed = ({ axios, log }) => pipe ([
  getArticles (axios),
  map (formatPost),
  map (log)
]) 
```

# 展示文章

现在我想写一个像下面这样的函数，但是我们会得到一个错误`id`没有定义。`id`是`pipe`的参数，但是在这里无法访问。`filter`的输入是文章数组，而不是`id`。

```
const getArticle = ({ axios }) => pipe ([
  getArticles (axios),
  filter (article => article.id === id), // 'id' is not defined
  articles => articles[0]
]) 
```

但是有一个技巧。使用 **W 组合子**我可以创建一个闭包，这样`id`就被暴露了。

```
const getArticle = ({ axios }) => W (id => pipe ([
  getArticles (axios),
  filter (article => article.id === id),
  articles => articles[0]
])) 
```

将该块与其上方的块进行比较，差别不大，只是增加了`W (id =>`和一个结束`)`。W Combinator 是一个非常棒的工具。关于函数组合子的更多内容将在以后的文章中讨论:)现在，让我们继续。

所有的加在一起`src/showArticle.mjs`应该是这样的:

```
import W from 'mojiscript/combinators/W'
import pipe from 'mojiscript/core/pipe'
import filter from 'mojiscript/list/filter'
import { getArticles } from './showFeed'

export const shouldShowArticle = args => args.length === 2 && args[0] === 'read'

const getArticle = ({ axios }) => W (id => pipe ([
  getArticles (axios),
  filter (article => article.id === id),
  articles => articles[0]
]))

export const showArticle = ({ axios, log }) => pipe ([
  getArticle ({ axios }),
  log
]) 
```

修改`main.mjs`的`cond`以包含新功能:

```
import { shouldShowArticle, showArticle } from './showArticle'

const main = dependencies => pipe ([
  cond ([
    [ shouldShowArticle, args => showArticle (dependencies) (args[1]) ],
    [ shouldShowFeed, showFeed (dependencies) ],
    [ () => true, showHelp (dependencies) ]
  ])
]) 
```

运行`npm run start -- 1i0a`(替换 id)，您应该会看到类似这样的内容:

```
{ id: '1i0a',
  url:
   'http://dev.to/ppshobi/-email-sending-in-django-2-part--1--1i0a',
  title: 'Email Sending in Django 2, Part -1',
  username: 'Shobi',
  tags: [ 'django', 'emails', 'consoleemailbackend' ],
  comments: '0',
  reactions: '13' } 
```

# HTML 转文本

我发现了一个很棒的 npm 包，看起来它可以帮我解决这个问题。

```
npm install --save-prod html-to-text 
```

我们已经奠定了大部分基础，所以要发出 HTTP 请求，解析 HTML 并将其格式化为文本，就像这样简单。打开`showArticle.mjs`。

```
const getArticleTextFromUrl = axios => pipe ([
  ({ url }) => getUrl (axios) (url),
  cheerio.load,
  parseArticle,
  article => `${article.title}\n\n${htmlToText.fromString (article.body)}`
]) 
```

我还想在没有找到`id`时创建一个视图。

```
const showArticleNotFound = $`Article ${0} not found.\n` 
```

我还将创建一个`isArticleFound`条件，使代码更具可读性。

```
const isArticleFound = article => article != null 
```

我将使用相同的 W Combinator 技术创建一个闭包，并公开`id`和修改`showArticle`。

```
export const showArticle = ({ axios, log }) => W (id => pipe ([
  getArticle ({ axios }),
  ifElse (isArticleFound) (getArticleTextFromUrl (axios)) (() => showArticleNotFound (id)),
  log
])) 
```

合在一起`showArticle.mjs`看起来是这样的:

```
import cheerio from 'cheerio'
import htmlToText from 'html-to-text'
import W from 'mojiscript/combinators/W'
import pipe from 'mojiscript/core/pipe'
import filter from 'mojiscript/list/filter'
import ifElse from 'mojiscript/logic/ifElse'
import $ from 'mojiscript/string/template'
import { getUrl } from './api'
import { parseArticle } from './interop/articleParser'
import { getArticles } from './showFeed'

const isArticleFound = article => article != null
const showArticleNotFound = $`Article ${0} not found.\n`
const getArticleTextFromUrl = axios => pipe ([
  ({ url }) => getUrl (axios) (url),
  cheerio.load,
  parseArticle,
  article => `${article.title}\n\n${htmlToText.fromString (article.body)}`
])

export const shouldShowArticle = args => args.length === 2 && args[0] === 'read'

const getArticle = ({ axios }) => W (id => pipe ([
  getArticles (axios),
  filter (article => article.id === id),
  articles => articles[0]
]))

export const showArticle = ({ axios, log }) => W (id => pipe ([
  getArticle ({ axios }),
  ifElse (isArticleFound) (getArticleTextFromUrl (axios)) (() => showArticleNotFound (id)),
  log
])) 
```

再次运行`npm start -- read 1i0a`，您应该会看到文章！

[![Minions are cheering](img/60c7956093e54233c76a2e004541e5f1.png)T2】](https://i.giphy.com/media/hEIuLmpW9DmGA/giphy.gif)

# 点睛之笔

我想让提要中的`id`更清晰。

```
const formatPost = $`${'id'}・${'title'}  ${'url'}\n#${'tags'}  ${'username'} ・ 💖 ${'comments'} 💬 ${'reactions'} ` 
```

将这个添加到`package.json`中，我将把这个命令命名为`devto`。

```
 "bin": {
    "devto": "./src/index.mjs"
  } 
```

在`src/index.mjs`中，在顶部加上这个神秘的魔法:

```
#!/bin/sh ':' //# comment; exec /usr/bin/env NODE_ENV=production node --experimental-modules --no-warnings "$0" "$@" 
```

运行此命令创建指向该命令的全局链接。

```
npm link 
```

如果一切顺利，您现在应该能够运行以下命令:

```
# get the feed
devto

# read the article
devto read <id> 
```

# 所以你决定跳到最后？

你可以把马牵到水边...或者什么的。

要赶上我们其他人，请遵循以下步骤

```
# clone the repo
git clone https://github.com/joelnet/devto-cli
cd devto-cli

# install
npm ci
npm run build
npm link

# run
devto 
```

# 关于 CLI 的警告

抓取网站不是个好主意。当网站发生变化时(这种情况肯定会发生)，您的代码就会出错。

这只是为了给 [#hacktoberfest](https://dev.to/t/hacktoberfest) 做一个有趣的演示，而不是一个可维护的项目。如果您发现一个 bug，请提交一个 pull 请求来修复它，并附上 bug 报告。我不会维持这个项目。

如果这是一个真实的项目，有些事情会很酷:

*   登录，这样你就可以阅读*你的*提要。
*   更多的互动，评论，喜欢，标签。也许贴一篇文章？

# Hacktoberfest 快乐！

对于那些通读了整本书的人，谢谢你的时间。我知道这很久了。我希望它是有趣的，我希望你学到了一些东西，最重要的是，我希望你玩得开心。

对于那些实际上一步一步地跟随并且自己创建了 CLI 的人来说:你使我完整了💖。

请在评论或 twitter 中告诉我你学到了什么，你发现了什么有趣的东西或任何其他评论，或你可能有的批评。

我的文章是非常实用的 JavaScript，如果你需要更多，请在这里关注我，或者在 Twitter 上关注我！

**更多文章**

[问我关于函数式编程的愚蠢问题](https://dev.to/joelnet/ask-me-dumb-questions-about-functional-programming-bho)
[让我们来谈谈 JavaScript 的自动生成文档工具](https://dev.to/joelnet/lets-talk-about-auto-generated-documentation-tools-for-javascript-49ol)

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)