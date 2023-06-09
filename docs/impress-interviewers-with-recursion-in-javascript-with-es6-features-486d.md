# 用带有 ES6 特性的 JavaScript 中的递归给面试官留下深刻印象

> 原文：<https://dev.to/hugo__df/impress-interviewers-with-recursion-in-javascript-with-es6-features-486d>

> 对于 JavaScript 面试来说，没有什么比递归更炫更有用的了。

如果你只是想对 JavaScript 中的递归印象深刻，这里有一些半真实世界(技术测试类型)的例子。

递归解决问题的简短定义(在计算机科学中)是:不要使用迭代。这通常意味着一个函数必须用同一个问题的一个更小的实例来调用自己，直到遇到一个无关紧要的情况(通常在问题中定义)。

因此，递归由几个步骤组成:

1.  基本或终止情况，这是我们想要结束的情况“一个递归函数定义有一个或多个*基本情况*，意味着输入函数产生一个结果(无递归)——[维基百科，递归(计算机科学)](https://en.wikipedia.org/wiki/Recursion_(computer_science)#Recursive_functions_and_algorithms)
2.  递归的情况下，程序递归(再次[维基百科，递归(计算机科学)](http://one%20or%20more%20recursive%20cases,%20meaning%20input(s)%20for%20which%20the%20program%20recurs%20(calls%20itself)))

在这篇文章中:

*   [🛰递归包装顺序 HTTP 请求](#recursion-to-wrap-sequential-http-requests)
*   [📃计算字符数](#count-number-of-characters)

这篇文章的例子在 [ObervableHQ](http://beta.observablehq.com) 上，这是一个超级酷的工具，允许你构建 JavaScript 笔记本:

*   [递归包装顺序 HTTP 请求](https://beta.observablehq.com/@hugodf/recursion-to-wrap-http-requests)
*   [计算字符数](https://beta.observablehq.com/@hugodf/count-something-in-something-else)

## 🛰递归包装顺序 HTTP 请求

假设您需要从 REST API 获取多个页面，并且您被迫使用本地 HTTPS 模块(ObservableHQ 上的[示例)。在这种情况下，我们将从 Reddit API 获取注释。](https://beta.observablehq.com/@hugodf/recursion-to-wrap-http-requests)

使用此 API:

*   如果有超过一个响应所能容纳的评论，它将在数据中返回一个`after`字段，该字段可以在获取下一个评论块的请求中用作查询参数
*   如果没有更多的评论，`after`将是假的

这定义了我们的终止和递归情况，我们从 Reddit API 获取数据，然后:

*   `after`是 falsy → **终止案例**，返回数据
*   `after`被定义→ **递归情况**，通过它获取下一页以及当前调用返回的数据

这里使用的技巧之一是从第一次传递开始将一个空的`data`数组传递给`recursiveCommentFetch`函数。这允许我们在遍历每个递归调用时不断注入越来越多的值，因此能够在终止的情况下解析出完整的集合。

```
const fetch = require('node-fetch');
const user = 'hugo__df';

function makeRedditCommentUrl(user, queryParams) {
  return `https://www.reddit.com/user/${user}/comments.json?${
    Object.entries(queryParams)
      .filter(([k, v]) => Boolean(v))
      .map(
        ([k, v]) => `${k}=${v}`
      ).join('&')
  }`;
}

function recursiveCommentFetch(user, data = [], { after, limit = 100 } = {}) {
  const url = makeRedditCommentUrl(user, { after, limit });
  return fetch(url)
    .then(res => res.json())
    .then(res => {
      const { after, children } = res.data;
      const newData = [...data, ...children];
      if (after) {
        // recursive case, there's a way to fetch more comments
        return recurseCommentFetch(user, newData, { after });
      }
      // base or terminating case
      return newData;
    });
}

recursiveCommentFetch(user)
  .then(comments => console.log(comments)); 
```

Enter fullscreen mode Exit fullscreen mode

我熟悉了这个 API，为 Reddit 贡献创建了以下可视化效果(GitHub 的贡献图风格)，[在 ObservableHQ 上看到](https://beta.observablehq.com/@hugodf/reddit-contributions-per-week-graph),[博客版本也是实时的](https://accountableblogging.com/post-frequency):

[![GitHub-style contribution graph for Reddit](img/41f7fd6a9105278f0a1205d45daf6224.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AfZyJ79s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_55FB281D4CEA97DF7A863A43508C5F8A03ED2CBE43CBD0A7C2D8693AF9B9134C_1538222014400_image.png)

## 📃计算字符数

当问题是这样的:“给定一个输入，返回一个包含每个字符在输入中出现多少次的对象”。

ObservableHQ 上有一个[现场演示。](https://beta.observablehq.com/@hugodf/count-something-in-something-else)

终止和递归的情况并不明显，所以这里有一些跳跃:

1.  理解一个输入可以被转换成一个字符串，这个字符串可以被`.split`成一个数组(即。大多数任意输入都可以转换成数组)。
2.  知道如何递归遍历一个数组，这可能是递归遍历最容易/最常见的事情之一，但是需要看几次才能感觉舒服

对于递归函数，这给了我们以下情况:

*   字符列表/数组为空→ **终止情况**，返回`characterToCount`图
*   字符列表/数组不为空→ **递归情况**,通过增加/初始化当前字符的条目来更新`characterToCountMap`。用更新的映射和列表/数组的剩余部分调用递归函数。

我写了一个更完整的帖子: [**用 ES6 在 JavaScript 中递归，析构和 rest/spread**](https://dev.to/hugo__df/recursion-in-javascript-with-es6-destructuring-and-restspread-4fln) ，它更详细地讲述了我们如何在 ES6 JavaScript 中递归遍历列表(数组)。它解释了像`[firstCharacter, ...rest]`符号这样的东西。

```
function recurseCountCharacters(
  [firstCharacter, ...rest],
  characterToCountMap = {}
) {
  const currentCharacterCount = characterToCountMap[firstCharacter] || 0;
  const newCharacterToCountMap = {
    ...characterToCountMap,
    [firstCharacter]: currentCharacterCount + 1
  };

  if (rest.length === 0) {
    // base/terminating case
    // -> nothing characters left in the string
    return newCharacterToCountMap;
  }
  // recursive case
  return recurseCountCharacters(rest, newCharacterToCountMap);
}

function countCharacters(input) {
  return recurseCountCharacters(String(input).split(''));  
}

console.log(countCharacters(1000000));
// { "0":6, "1": 1 }
console.log(countCharacters('some sentence'));
// { "s":2,"o":1,"m":1,"e":4," ":1,"n":2,"t":1,"c":1} 
```

Enter fullscreen mode Exit fullscreen mode

这就是你如何使用递归轻松通过面试🙂，绕着那些玩具问题跑圈。

面试问题的递归解决方案看起来比迭代解决方案更酷、更简洁，更吸引面试官的眼球。

如有任何问题，您可以通过 Twitter [@hugo__df](https://twitter.com/hugo__df) 联系我。

[Andre Mouton](https://unsplash.com/@andremouton?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)