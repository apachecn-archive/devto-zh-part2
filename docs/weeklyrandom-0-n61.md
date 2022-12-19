# weekly 随机#0

> 原文：<https://dev.to/vintagesucks/weeklyrandom-0-n61>

*欢迎来到 [**WeeklyRandom**](https://dev.to/t/weeklyrandom) ，这是一个博客系列，在这个系列中，我挑战自己，每周探索一个由我主演的随机 GitHub 知识库。*

让我们开始吧。我需要一种方法来检索一个由我命名的随机 GitHub 库。我发现[这个要点](https://gist.github.com/derhuerst/19e0844796fa3b62e1e9567a1dc0b5a3)显示了如何检索用户的星号存储库。

```
// https://gist.github.com/derhuerst/19e0844796fa3b62e1e9567a1dc0b5a3
const stars = (user) =>
    got(`https://api.github.com/users/${user}/starred`)
    .then((res) => JSON.parse(res.body))
    .then((starred) => starred.map((s) => ({
          owner:       s.owner.login
        , repo:        s.name
        , description: s.description
        , language:    s.language
        , isFork:      false
        , stargazers:  s.stargazers_count
        , watchers:    s.watchers_count
}))) 
```

我不是 JavaScript 专家🧙，不知道 [`got`](https://github.com/sindresorhus/got) 依赖在这里是否真的有必要。让我们暂时保留它。

问题:这只返回 30 个存储库。由于 [GitHub API](https://developer.github.com/v3/) 是分页的，所以我需要想出一种方法，在尝试获取随机页面和条目之前，获取页面总数。我通过解析包含以下内容的响应头来解决这个问题:`Link` :

```
<https://api.github.com/user/13335308/starred?page=2>; rel="next", <https://api.github.com/user/13335308/starred?page=93>; rel="last" 
```

完美。我可以使用`rel="last"`生成一个介于 1 和 93 之间的随机页码。如果我发送一个页面请求，剩下唯一要做的事情就是从返回的 30 个库中随机选择一个。

这是当前脚本:

> 注:2018 年 10 月 16 日更新，以反映由 [u/ChucklefuckBitch](https://www.reddit.com/r/learnjavascript/comments/9onbd6/looking_for_feedback_on_a_script_to_get_a_random/e7vdm0t/) 和 [u/dulac91](https://www.reddit.com/r/learnjavascript/comments/9onbd6/looking_for_feedback_on_a_script_to_get_a_random/e7vky1a/) 建议的更改。谢谢！

```
// index.js

"use strict";

const got = require("got");

let user = process.argv.slice(2)[0];
let randomEntry = Math.floor(Math.random() * 30);
let lastPage = new RegExp(/(.*)page=(.*)>; rel=\"last\"/);

if (!user) {
  console.log("Usage: `node index.js username`");
  process.exit(1);
}

const getStars = (user, page) =>
  got(`https://api.github.com/users/${user}/starred?page=${page}`)
    .then((res) => JSON.parse(res.body))
    .then((starred) =>
      starred.map((s) => ({
        owner: s.owner.login,
        repo: s.name
      }))
    );

const getRandomPage = (user) =>
  got(`https://api.github.com/users/${user}/starred`)
    .then((res) =>
      res.headers.link
        .replace(lastPage, "$2")
    )
    .then((pages) => Math.floor(Math.random() * pages) + 1);

getRandomPage(user, randomEntry)
  .then((page) => getStars(user, page))
  .then((result) =>
    console.log("https://github.com/" + result[randomEntry].owner + "/" + result[randomEntry].repo)
  ); 
```

我很确定这是一个非常低效的方法，但是它应该可以完成任务。这个系列的整个目的就是想学点东西，提升自己。我确实学到了一些关于使用 GitHub API 和 JavaScript 中的箭头函数的知识。

我们来试试:

```
node index.js vintagesucks
https://github.com/lgkonline/mavo-snippets

node index.js vintagesucks
https://github.com/simonepri/geo-maps

node index.js vintagesucks
https://github.com/genuinetools/apk-file 
```

有用！🎉

你可以在 GitHub 的[vintage sucks/random-starred-repository](https://github.com/vintagesucks/random-starred-repository)找到完整的代码。非常欢迎有改进的拉动请求！

直到下周，我将检查我的第一个 [*WeeklyRandom*](https://dev.to/t/weeklyrandom) 库。