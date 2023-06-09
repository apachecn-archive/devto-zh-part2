# 在节点和浏览器中使用“graphql.js”的 JavaScript GraphQL 客户端请求

> 原文：<https://dev.to/hugo__df/javascript-graphql-client-requests-in-node-and-the-browser-using-graphqljs-45n5>

> 使用`graphql.js`从节点和浏览器使用 JavaScript 中的 GraphQL API 的示例

看例子直播:[https://codewithhugo.com/js-graphql-client-example/](https://codewithhugo.com/js-graphql-client-example/)。

全额回购:[https://github.com/HugoDF/js-graphql-client-example](https://github.com/HugoDF/js-graphql-client-example)。

*   从节点获取
*   从浏览器获取
*   GraphQL 文档工具

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

## 从节点取数

`fetch.js` :

```
const graphql = require('graphql.js');

const graph = graphql('https://graphql-pokemon.now.sh/');

const query = graph(`{
  pokemon(name: "Pikachu") {
    attacks {
      special {
        name
      }
    }
  }
}`);

if (require.main === module) {
  query().then(
    res => console.log(JSON.stringify(res, null, 2)),
    err => console.error(err)
  );
}

module.exports = {
  query
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
 $ node fetch.js
{
  "pokemon": {
    "attacks": {
      "special": [
        {
          "name": "Discharge"
        },
        {
          "name": "Thunder"
        },
        {
          "name": "Thunderbolt"
        }
      ]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 从浏览器中抓取

`graphql.js`是同构的，它也将在浏览器中运行，我们将使用包将所有东西缝合在一起。

默认情况下，我们使用来自`fetch.js`的查询进行获取，然后当用户单击`Try it`按钮时，我们使用 textarea 的内容。

这段代码将 fetch 逻辑与一些从 DOM 读取查询并在完成时更新输出 div 连接起来，`client.js` :

```
const { query, graph } =require('./fetch');

const $queryElement = document.querySelector('.query');
const $output = document.querySelector('.output');
const $submitButton = document.querySelector('button');

$submitButton.onclick = () => {
  const queryData = $queryElement.value;
  runQuery(graph(queryData))
}

runQuery(query);

function runQuery (query) {
  query().then(
    res => {
      $output.innerHTML = `<pre><code>${JSON.stringify(res, null, 2)}</code></pre>`;
    },
    err => {
      $output.innerHTML = `Error: <pre><code>${JSON.stringify(err, null, 2)}</code></pre>`;
    }
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

`index.html` :

```
<!doctype html>
<html class="no-js" lang="en">

<head>
  <meta charset="utf-8">
  <meta http-equiv="x-ua-compatible" content="ie=edge">
  JavaScript GraphQL Client Example
  <meta name="description" content="JavaScript GraphQL Client example">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <style>
    body {
      font-family: -apple-system, BlinkMacSystemFont,
          'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell,
          'Open Sans', 'Helvetica Neue', sans-serif;
    }
  </style>
</head>

<body>
  <p>For full documentation see: <a href="https://graphql-pokemon.now.sh/">https://graphql-pokemon.now.sh/</a></p>
  <h2>Input: </h2>
  <textarea class="query" style="min-width: 285px; min-height: 150px">
{
  pokemon(name: "Pikachu") {
    attacks {
      special {
        name
      }
    }
  }
}
  </textarea>
  <button>Try it</button>
  <h2>Output: </h2>
  <div class="output"></div>
  <script src="./client.js"></script>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
$ npm install --save-dev parcel

$ npx parcel index.html 
```

Enter fullscreen mode Exit fullscreen mode

打开 [http://localhost:1234](http://localhost:1234) 。

[![Sample GraphQL query result displayed in the browser](img/a29005cca2062f5a5ef60679b3198a93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wDdhNjX7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_436CC3FB0186E19AFA848D5D9BBDDD002D6719E8B00591E0944C85836193A66D_1533923400947_image.png)

为了进行测试，我们可以将 textarea 的内容改为:

```
{
  pokemon(name: "Pikachu") {
    attacks {
      fast {
        name
        type
        damage
      }
      special {
        type
        damage
        name
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

并点击`Try it`。这产生了以下结果:

[![new GraphQL query output in browser](img/73609e402ebf2ed9c3a634f4a13ef1aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1LI5EYs9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_436CC3FB0186E19AFA848D5D9BBDDD002D6719E8B00591E0944C85836193A66D_1534187747016_image.png)

## GraphQL 文档工具

对于 pokemon GraphQL API 的托管 GraphQL 文档，请参见 https://graphql-pokemon.now.sh/的[，它打开 GraphQL，您可以在其中探索 API，使用`CTRL + space`显示字段建议，默认情况下使用`CMD + enter`扩展所有嵌套字段。您也可以右键单击某个字段来查看其类型等。](https://graphql-pokemon.now.sh/)

[![GraphiQL for pokemon GraphQL API](img/c3e14019e0629d3624f246fb9e4a9d0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---CpsZ5yo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_436CC3FB0186E19AFA848D5D9BBDDD002D6719E8B00591E0944C85836193A66D_1533923625458_image.png)

关于 GraphQL 的更多信息将于下周在 Hugo 时事通讯的[代码中发布，所以](https://buttondown.email/hugo)[如果你还没有订阅](https://buttondown.email/hugo)。

如果你有任何问题，欢迎发微博给我。

安德鲁·庞斯