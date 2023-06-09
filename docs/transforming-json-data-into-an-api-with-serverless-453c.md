# 用无服务器将 JSON 数据转换成 API

> 原文：<https://dev.to/raymondcamden/transforming-json-data-into-an-api-with-serverless-453c>

这个东西已经在我的“写”特雷罗板上放了一段时间了，今天我终于开始制作一个演示。我最喜欢做的一件事就是构建 API 包装器。有成千上万的 API，但是很多时候你需要操作或改变数据，使其更适合你的使用。虽然您可以在客户机上这样做，但在服务器上这样做可能更有效。当然，当您可以使用无服务器功能时，谁愿意设置一个服务器来更改 API 呢？这方面的一些例子有:

*   转换:几个月前，我使用了一个纯 XML 的 API。因为现在不是 1995 年，所以我用了一个无服务器函数将其转换成 JSON。
*   Reduction:不久前，我使用了一个返回大量信息的音乐 API。然而，我只需要其中的一小部分。通过构建一个减少返回数据的无服务器代理，我的客户端，一个移动应用程序，收到的数据少得多，因此对最终用户来说更快。
*   组合:如果一个 API 不能满足您的需求，那么两个或更多 API 可能可以，您可以使用一个无服务器的函数来获取和组合这些 API。一个很好的例子就是从多个提供商那里获取运输信息。
*   代理:最后——拥有自己的端点意味着你可以在未来的任何时候做服务器端缓存，甚至是“真正的”API 的大规模替换。

对于我今天的演示，我正在做一些我认为有趣的事情。有时候一个 API 并不是真正的 API，而只是一个数据转储。它可能会不时更新，但它不支持参数。它只是一个返回 JSON 的 URL。我认为在 JSON 转储之前展示如何构建自己的 API 会很酷。我将使用 [Webtask.io](https://webtask.io/) ,但显然任何无服务器提供商都可以。

对于我的数据，我这里用的是一个口袋妖怪数据的 JSON 包:[https://raw . githubusercontent . com/Biuni/Pokemon go-Pokedex/master/Pokedex . JSON](https://raw.githubusercontent.com/Biuni/PokemonGO-Pokedex/master/pokedex.json)。我通过 jdorfman 的[awesome-JSON-datasets](https://github.com/jdorfman/awesome-json-datasets)repo 找到了这个数据集。JSON 数据包含 151 个不同的口袋妖怪。我不知道这有多准确。我有这样做的孩子，但他们不在我身边，我也不在乎谷歌搜索。；)

对于我的函数的第一稿，我只是返回数据。我还使用了缓存。如你所知(或可能知道！)，serverless 是无状态的。然而，大多数无服务器提供商会在短时间内保持你的功能“温暖”(认为是活跃的)。这意味着在一定时间内重复调用可以利用本地缓存的值来更快地得到结果。

```
const JSON_URL = 'https://raw.githubusercontent.com/Biuni/PokemonGO-Pokedex/master/pokedex.json';

let rp = require('request-promise');
let cached;

module.exports = async (context, cb) => {

  let data = await getData();  

  cb(null, { data });
};

async function getData() {
  if(cached) {
    console.log('using cache');
    return cached;
  }
  else {
    return new Promise((resolve, reject) => {
      rp(JSON_URL)
      .then(res => {
        cached = JSON.parse(res).pokemon;
        resolve(cached);
      });

    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个相当简单的函数。基本上返回一个缓存或者点击一个 URL 并返回。没有变形，没有操纵，没有其他。尽管我也有一些直接的好处。如果托管数据的服务器有点慢，我的缓存可以帮助解决这个问题。如果网址消失了，或者如果他们决定开始对数据收费，我可能会换到另一个提供商，而我的客户永远不会知道。(如果数据改变了，我可以把它改回来！)

好吧-现在让我们添加一些过滤！

```
const JSON_URL = 'https://raw.githubusercontent.com/Biuni/PokemonGO-Pokedex/master/pokedex.json';

let rp = require('request-promise');
let cached;

module.exports = async (context, cb) => {

  let data = await getData();  

  // support filter by name
  if(context.query.name) {
    console.log('filter to name '+context.query.name);
    let sname = context.query.name.toLowerCase();
    data = data.filter(p => {
      let lname = p.name.toLowerCase();
      return lname.indexOf(sname) >= 0;
    });
  }

  // support filter by type
  if(context.query.type) {
    let type = context.query.type.toLowerCase();
    console.log('filtering to type '+type);
    data = data.filter(p => {
      //rewrite types to lowercase 
      let types = p.type.join(',').toLowerCase().split(',');
      return types.indexOf(type) >= 0;
    });
  }

  cb(null, { data });
};

async function getData() {
  if(cached) {
    console.log('using cache');
    return cached;
  }
  else {
    return new Promise((resolve, reject) => {
      rp(JSON_URL)
      .then(res => {
        cached = JSON.parse(res).pokemon;
        resolve(cached);
      });

    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里使用查询字符串添加了两个可能的过滤器。(这是通过所有 Webtasks 都可以访问的[上下文](https://webtask.io/docs/context)对象来完成的。)在这两种情况下，我只做简单的基于数组的过滤。这里唯一真正的“工作”是小写，这样你就不用担心匹配相同的大小写。我的核心 API 可以在这里找到:

[https://wt-C2 bde 7d 7 DFC 8623 f 121 b 0 eb5a 7102930-0 . sandbox . auth 0-extend . com/eoApi](https://wt-c2bde7d7dfc8623f121b0eb5a7102930-0.sandbox.auth0-extend.com/eoApi)

您可以按名称或类型进行搜索，也可以两者都用。例如:

[https://wt-C2 bde 7d 7 DFC 8623 f 121 b 0 eb5a 7102930-0 . sandbox . auth 0-extend . com/eoApi？name=Ba](https://wt-c2bde7d7dfc8623f121b0eb5a7102930-0.sandbox.auth0-extend.com/eoApi?name=Ba)

或者:

[https://wt-C2 bde 7d 7 DFC 8623 f 121 b 0 eb5a 7102930-0 . sandbox . auth 0-extend . com/eoApi？type=ghost](https://wt-c2bde7d7dfc8623f121b0eb5a7102930-0.sandbox.auth0-extend.com/eoApi?type=ghost)

或者:

[https://wt-C2 bde 7d 7 DFC 8623 f 121 b 0 eb5a 7102930-0 . sandbox . auth 0-extend . com/eoApi？type=fire & name=ca](https://wt-c2bde7d7dfc8623f121b0eb5a7102930-0.sandbox.auth0-extend.com/eoApi?type=fire&name=ca)

就是这样。如果你有任何问题，请在下面给我留言。