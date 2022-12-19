# 所以你有一堆事情要做。为什么不建一条管道？

> 原文：<https://dev.to/krofdrakula/so-you-have-a-bunch-of-things-to-do-why-not-build-a-pipeline-31o0>

开发软件时，编写可读性好的代码是个好主意。而且，像任何一个好的讲故事的人一样，你想要省略不重要的细节。您还希望留下面包屑，以便读者在需要时了解细节。

坐下来，喝一杯热饮，让我们直接进入正题。

[![](img/30bae69daea509e3f0d4331879959df5.png)T2】](https://i.giphy.com/media/Lp4L5KOAHjCpUidRuq/giphy.gif)

## 一个好故事的要素

故事、程序、流程、函数、算法有什么共同点？

它们都有开始、中间和结束。

当我们描述过程时，我们从描述我们需要执行的先决条件和材料开始，过程的*输入*。我们描述了执行该过程所需的步骤。当一切都结束时，描述还包括预期的结果，即*输出*。

如果您认为这听起来非常像一个函数调用，那么您绝对是正确的。但是，如果您不理解这种推论，不要担心，通过这篇文章，您将熟悉这个概念。😁

## 定义输入

[![](img/3112b31eb460fecbfa628281ed2cea58.png)T2】](https://i.giphy.com/media/3ornjKd18tFIZ8PUKQ/giphy.gif)

让我们穿上我们的角色扮演服装。您在这个故事中的角色将是一个分析师，其任务是提交关于选定子数据集的报告。您将获得一个子编辑列表，以便根据页面生成几种类型的报告。

你的任务是为每个给定的子编辑首页生成一些报告:

1.  每篇文章字数的中位数
2.  每个帖子的评论数量的中位数
3.  附有图片的帖子与所有帖子的比率

至于 URL，您可以选择，但是在这个例子中，我们将使用`/r/dataisbeautiful`:

[https://www.reddit.com/r/dataisbeautiful/](https://www.reddit.com/r/dataisbeautiful/)

看完之后，尝试一下 JSON URL，这样您就会对数据的结构有所了解:

`const fetch = require('node-fetch');` `const url = 'https://www.reddit.com/r/dataisbeautiful.json'; fetch(url) .then(response => response.json()) .then(json => console.log(json));`

## 定义步骤

[![](img/ff4c4de1278c9e66e566955adcfa9be9.png)T2】](https://i.giphy.com/media/xThta8UkUaoqJoJQC4/giphy.gif)

所以首先，我们需要把问题分解成明确的步骤。粒度越细，就越容易理解、调试和重用。游戏规则是**做一件事，并且做好**。

我们先拿第一份报告，把步骤写下来。粒度越细越好。

1.  生成 URL
2.  获取 JSON 数据
3.  提取帖子
4.  提取每篇文章的文本和标题
5.  为每个文本生成字数统计
6.  计算所有文本的中值

理想情况下，您应该对每一步都进行测试。为了简洁起见，我省略了本文中的测试，但是如果我在代码审查中审查您的代码，这肯定行不通！

### 第一步:生成网址

这个很简单:获取一个 Reddit URL，删除尾部的斜杠(如果有的话)并追加`.json`字符串。

```
const getRedditJSONUrl = url => url.replace(/\/?$/, '.json'); 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:取 JSON 数据

用`fetch`进行简单的调用并将响应转换成 JSON 就可以做到这一点。

```
const fetchData = url => fetch(url).then(response => response.json()); 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:提取帖子

我们知道每个页面都包含了`data.children`属性，它保存了我们感兴趣的文章的数组。

```
const extractPosts = redditPage => redditPage.data.children; 
```

Enter fullscreen mode Exit fullscreen mode

### 第四步:提取每个帖子的帖子文本

每个帖子的标题可以在`data.title`属性中找到，文本在`data.selftext`中找到。我们将使用换行符`\n`将它们连接起来。

```
const extractPostTextAndTitle = post => post.data.title + '\n' + post.data.selftext; 
```

Enter fullscreen mode Exit fullscreen mode

### 第五步:生成每篇文章的字数

这个有点棘手。没有快速可靠地统计字数的方法，所以我们将使用来自 NPM 的更复杂的效用函数， [`@iarna/word-count`](https://www.npmjs.com/package/@iarna/word-count) 。

注意，我们仍然在创建一个包装库函数的函数。这是为了在我们需要改变实现的情况下，或者如果函数调用由于我们这边的代码重构而改变，将我们与库隔离开来。

```
const _wordCount = require('@iarna/word-count');

const countWords = text => _wordCount(text); 
```

Enter fullscreen mode Exit fullscreen mode

### 第六步:计算中位数

为了计算一组数字的中值，我们将它们从最小到最大排序。中位数是将有序集分成相等两半的值。对于具有奇数个值的集合，它将是中间值。对于均匀计数的集合，它将是中间两个值的中点。

这是一组奇数和偶数的中间值:

```
[1 1 2 3 5 8 13] ~ size = 7
       ^ median = 3

[1 1 2 3 5 8 13 21] ~ size = 8
        ^ median = (3+5)/2 
```

Enter fullscreen mode Exit fullscreen mode

下面是实现:

```
const numberValueSorter = (a, b) => a - b;

const calculateMedian = list => {
  // an empty list has no median
  if (list.length == 0) return undefined;

  // sort the values
  const sorted = Array.from(list).sort(numberValueSorter);

  if (sorted.length % 2 == 0) {
    // we're dealing with an even-sized set, so take the midpoint
    // of the middle two values
    const a = sorted.length / 2 - 1;
    const b = a + 1;
    return (list[a] + list[b]) / 2;
  } else {
    // pick the middle value
    const i = Math.floor(sorted.length / 2);
    return list[i];
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 连接步骤

[![](img/61f1c869ad179dd30970b893432c1667.png)T2】](https://i.giphy.com/media/gZqDdFC62X6KY/giphy.gif)

现在我们已经有了适当的步骤，让我们以经典的命令式风格写出代码，这样我们就能更好地理解这个过程是什么样子的。

`const fetch = require('node-fetch'); const _wordCount = require('@iarna/word-count'); const getRedditJSONUrl = url => url.replace(/\/?$/, '.json'); const fetchData = url => fetch(url).then(response => response.json()); const extractPosts = redditPage => redditPage.data.children; const extractPostTextAndTitle = post => post.data.title + '\n' + post.data.selftext; const countWords = text => _wordCount(text); const numberValueSorter = (a, b) => a - b; const calculateMedian = list => { if (list.length == 0) return undefined; const sorted = Array.from(list).sort(numberValueSorter); if (sorted.length % 2 == 0) { const a = sorted.length / 2 - 1; const b = a + 1; return (list[a] + list[b]) / 2; } else { const i = Math.floor(sorted.length / 2); return list[i]; } }` `const URL = 'https://www.reddit.com/r/dataisbeautiful/'; // because some of the steps require resolving Promises, we'll // use an async function so we can await the result (async () => { // step 1 const jsonURL = getRedditJSONUrl(URL); // step 2 – needs awaiting const pageData = await fetchData(jsonURL); // step 3 const posts = extractPosts(pageData); // step 4 – we need to map over the elements of the array const texts = posts.map(extractPostTextAndTitle); // step 5 - same here const wordCounts = texts.map(countWords); // step 6 const median = calculateMedian(wordCounts); console.log('Median word count for ' + URL, median); })();`

就讲故事而言，心流似乎无处不在。我们不是简单地列出步骤，而是依次调用每个步骤，保存中间结果并将结果交给下一步。

在这个故事中也有几个陷阱；有些要求`await` ing 结果，有些要求用`map`包装调用来处理每一项。

如果我们能把这些步骤连接起来，然后把结果传递下去，会怎么样呢？他眼睛闪闪发光地问道。

[![](img/dc2e6fc46d40a940be2efa9e6366a353.png)T2】](https://i.giphy.com/media/13vPE0A3DPqOcg/giphy.gif)

## 输入管道

这里我们需要引入一个新概念——`pipeline`函数。让我们从分析我们的原始流程开始，该流程获取一个 subreddit URL 并为页面生成一个中值字数:

```
const getMedianWordCountReport = async subredditUrl => {
  /* something something spaceship */
  return 'voilá!';
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们说过我们的流程是由上述六个步骤定义的。让我们假设`pipeline`存在，并编写让我们从一系列步骤中创建流程函数的代码:

```
const getMedianWordCountReport = pipeline(
  getRedditJSONUrl,
  fetchData,
  extractPosts,
  map(extractPostTextAndTitle),
  map(countWords),
  calculateMedian
);

const URL = 'https://www.reddit.com/r/dataisbeautiful/';

// it's an async function, so we need to wait for it to resolve
getMedianWordCountReport(URL)
  .then(median =>
    console.log('Median word count for ' + URL, median)
  )
  .catch(error => console.error(error)); 
```

Enter fullscreen mode Exit fullscreen mode

啊，但是那里的那个`map()`函数呢？只是改变了`Array::map`函数，以便在接受数组之前使用映射函数:

```
const map = mapper => array => array.map(mapper); 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，一切顺利。我们现在知道了函数*应该*做什么，我们只需要定义它。先来定义一下它的签名:

```
const pipeline = (...steps) => {  // take a list of steps,
  return async input => {         // return an async function that takes an input,
    return input;                 // and eventually returns a result
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们已经创建了一个函数，它接受任意数量的函数(`steps`)并返回一个`async function`，即流程函数。

对于每一步，该函数应该获取最后一个中间结果，将其提供给下一步，并保存该中间结果。

如果没有更多的步骤，返回最后一个中间结果。

准备好了吗？走吧。

```
const pipeline = (...steps) => {    // take a list of steps defining the process
  return async input => {           // and return an async function that takes input;
    let result = input;             // the first intermediate result is the input;
    for (const step of steps)       // iterate over each step;
      result = await step(result);  // run the step on the result and update it;
    return result;                  // return the last result!
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

你可能会想，*“不，那不可能。真的就这些吗？”*

没错。自己试试:

`const fetch = require('node-fetch'); const _wordCount = require('@iarna/word-count'); const getRedditJSONUrl = url => url.replace(/\/?$/, '.json'); const fetchData = url => fetch(url).then(response => response.json()); const extractPosts = redditPage => redditPage.data.children; const extractPostTextAndTitle = post => post.data.title + '\n' + post.data.selftext; const countWords = text => _wordCount(text); const numberValueSorter = (a, b) => a - b; const calculateMedian = list => { if (list.length == 0) return undefined; const sorted = Array.from(list).sort(numberValueSorter); if (sorted.length % 2 == 0) { const a = sorted.length / 2 - 1; const b = a + 1; return (list[a] + list[b]) / 2; } else { const i = Math.floor(sorted.length / 2); return list[i]; } }` `const map = mapper => array => array.map(mapper); const pipeline = (...steps) => { return async input => { let result = input; for (const step of steps) result = await step(result); return result; }; }; const getMedianWordCount = pipeline( getRedditJSONUrl, fetchData, extractPosts, map(extractPostTextAndTitle), map(countWords), calculateMedian ); const URL = 'https://www.reddit.com/r/dataisbeautiful/'; getMedianWordCount(URL) .then(median => console.log('Median word count', median));`

[![](img/814c8ad212026953149acc5873abe5fb.png)T2】](https://i.giphy.com/media/vA4EnqvJxDv2g/giphy.gif)

## 精简流水线

我们在管道上有几个弯，我们想把它们弄直。在某一点上，结果从单个值变为一个值列表(`extractPosts`)，然后再变回(`calculateMedian`)。如果我们能把必须处理单个项目的函数组合在一起，那就更好了。

为了做到这一点，让我们创建一个复合函数，该函数将采取许多步骤来处理单个值，并将它们串在一起以对值列表进行操作:

```
const map = (...mappers) =>                 // take an array of mappers,
  array =>                                  // and return a function that takes an array;
    array.map(                              // map each item of the array
      item => mappers.reduce(               // through a function that passes each item
        (result, mapper) => mapper(result)  // and runs them through the chain of mappers
      )
    ); 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个函数有一个警告:传递给这个`map`函数的映射函数必须是同步的。为了完整起见，让我们假设每个映射器可能是一个`async`函数，并且应该被相应地对待。

```
const map = (...mappers) =>
  async array => {                      // we now have to return an async function
    const results = [];
    for (const value of array) {        // for each value of the array,
      let result = value;               // set the first intermediate result to the first value;
      for (const mapper of mappers)     // take each mapper;
        result = await mapper(result);  // and pass the intermediate result to the next;
      results.push(result);             // and push the result onto the results array;
    }
    return results;                     // return the final array
  }; 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经解决了边缘情况，我们可以通过将两个单项功能组合成一个步骤来重新制定我们的流程功能:

`const fetch = require('node-fetch'); const _wordCount = require('@iarna/word-count'); const getRedditJSONUrl = url => url.replace(/\/?$/, '.json'); const fetchData = url => fetch(url).then(response => response.json()); const extractPosts = redditPage => redditPage.data.children; const extractPostTextAndTitle = post => post.data.title + '\n' + post.data.selftext; const countWords = text => _wordCount(text); const numberValueSorter = (a, b) => a - b; const calculateMedian = list => { if (list.length == 0) return undefined; const sorted = Array.from(list).sort(numberValueSorter); if (sorted.length % 2 == 0) { const a = sorted.length / 2 - 1; const b = a + 1; return (list[a] + list[b]) / 2; } else { const i = Math.floor(sorted.length / 2); return list[i]; } } const pipeline = (...steps) => { return async input => { let result = input; for (const step of steps) result = await step(result); return result; }; };` `const map = (...mappers) => async array => { const results = []; for (const value of array) { let result = value; for (const mapper of mappers) result = await mapper(result); results.push(result); } return results; }; const getMedianWordCount = pipeline( getRedditJSONUrl, fetchData, extractPosts, map( extractPostTextAndTitle, countWords ), calculateMedian ); const URL = 'https://www.reddit.com/r/dataisbeautiful/'; getMedianWordCount(URL) .then(median => console.log('Median word count', median));`

而且还能用！

[![](img/feda77c6a0fda1b37152427ce4709993.png)T2】](https://i.giphy.com/media/1xpTgPBebfe8lhj4yE/giphy.gif)

## 分叉管道

所以现在我们有了一个`pipeline`函数，我们可以用它来声明性地构造一个描述我们流程的函数。但到目前为止，我们只完成了角色扮演场景中三个最初目标中的*一个*。

哦，不！

让我们写下所有的过程，以评估我们还需要做什么。

```
const getMedianWordCount = pipeline(
  getRedditJSONUrl,
  fetchData,
  extractPosts,
  map(
    extractPostTextAndTitle,
    countWords
  ),
  calculateMedian
);

const getMedianCommentCount = pipeline(
  getRedditJSONUrl,
  fetchData,
  extractPosts,
  map(countComments),
  calculateMedian
);

const getImagePresentRatio = pipeline(
  getRedditJSONUrl,
  fetchData,
  extractPosts,
  map(hasImageAttached),
  calculateRatio
); 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们需要写下几个步骤，这样我们就有了组装流程的所有可用函数。现在就来补充一下:

```
const countComments = post => post.data.num_comments;

const hasImageAttached = post => post.data.post_hint == 'image';

const calculateRatio = array => {
  if (array.length == 0) return undefined;
  return array.filter(value => !!value).length / array.length;
}; 
```

Enter fullscreen mode Exit fullscreen mode

完成后，让我们看看这一切是否运行:

`const fetch = require('node-fetch'); const _wordCount = require('@iarna/word-count'); const getRedditJSONUrl = url => url.replace(/\/?$/, '.json'); const fetchData = url => fetch(url).then(response => response.json()); const extractPosts = redditPage => redditPage.data.children; const extractPostTextAndTitle = post => post.data.title + '\n' + post.data.selftext; const countWords = text => _wordCount(text); const numberValueSorter = (a, b) => a - b; const calculateMedian = list => { if (list.length == 0) return undefined; const sorted = Array.from(list).sort(numberValueSorter); if (sorted.length % 2 == 0) { const a = sorted.length / 2 - 1; const b = a + 1; return (list[a] + list[b]) / 2; } else { const i = Math.floor(sorted.length / 2); return list[i]; } } const pipeline = (...steps) => { return async input => { let result = input; for (const step of steps) result = await step(result); return result; }; }; const map = (...mappers) => async array => { const results = []; for (const value of array) { let result = value; for (const mapper of mappers) result = await mapper(result); results.push(result); } return results; }; const countComments = post => post.data.num_comments; const hasImageAttached = post => post.data.post_hint == 'image'; const calculateRatio = array => { if (array.length == 0) return undefined; return array.filter(value => !!value).length / array.length; }; const getMedianWordCount = pipeline( getRedditJSONUrl, fetchData, extractPosts, map( extractPostTextAndTitle, countWords ), calculateMedian ); const getMedianCommentCount = pipeline( getRedditJSONUrl, fetchData, extractPosts, map(countComments), calculateMedian ); const getImagePresentRatio = pipeline( getRedditJSONUrl, fetchData, extractPosts, map(hasImageAttached), calculateRatio );` `const URL = 'https://www.reddit.com/r/dataisbeautiful/'; // now we need to call all three processes and report the final count Promise.all([ getMedianWordCount(URL), getMedianCommentCount(URL), getImagePresentRatio(URL) ]).then(([medianWordCount, medianCommentCount, imagePresentRatio]) => { console.log( 'Results for ' + URL, { medianWordCount, medianCommentCount, imagePresentRatio } ); });`

很好，我们现在知道我们可以用这些构件构建流程。不过，有一个小问题。每个进程都必须做很多相同的事情，让每个进程每次都获取相同的数据并经历相同的动作似乎是一种浪费。

让我们创建一个`fork`函数来处理这个问题。

理想情况下，我们希望将管道分割成每个流程的特定管道，然后将它们连接在一起以获得最终结果。让我们写一些幻想的代码，让目标更清晰一点:

```
const getMedianWordCount = pipeline(
  map(
    extractPostTextAndTitle,
    countWords
  ),
  calculateMedian
);

const getMedianCommentCount = pipeline(
  map(countComments),
  calculateMedian
);

const getImagePresentRatio = pipeline(
  map(hasImageAttached),
  calculateRatio
);

// this is a convenience function that associates names to the results returned
const joinResults = ([
  medianWordCount,
  medianCommentCount,
  imagePresentRatio
]) => ({
  medianWordCount,
  medianCommentCount,
  imagePresentRatio
});

// the process function, now with forking!
const getSubredditMetrics = pipeline(
  getRedditJSONUrl,
  fetchData,
  extractPosts,
  fork(
    getMedianWordCount,
    getMedianCommentCount,
    getImagePresentRatio
  ),
  joinResults
); 
```

Enter fullscreen mode Exit fullscreen mode

根据以上要求，`fork`函数取一系列流水线。

在这一点上，我会建议你继续尝试编写自己的`fork`实现，考虑到上面的约束。您的实现可能非常类似于扩展的`map`。

下面是我对`fork`函数的理解:

```
const fork = (...pipelines) =>       // a function that takes a list of pipelines,
  async value =>                     // returns an async function that takes a value;
    await Promise.all(               // it returns the results of promises...
      pipelines.map(                 // ...mapped over pipelines...
        pipeline => pipeline(value)  // ...that are passed the value.
      )
    ); 
```

Enter fullscreen mode Exit fullscreen mode

如果它看起来令人困惑，不要担心。解开这个函数的作用需要很多时间。

诀窍是记住`Promise.all()`接受一个承诺数组，并返回一个在所有值都解析后解析的承诺。结果是以相同顺序排列的 promise 结果的数组。如果任何一个值不是一个承诺，它就把它当作一个有结果的立即解决的承诺。

## 最终结果

那么,`fork`会起作用并为我们节省额外的开销吗？让我们看看。

`const fetch = require('node-fetch'); const _wordCount = require('@iarna/word-count'); const getRedditJSONUrl = url => url.replace(/\/?$/, '.json'); const fetchData = url => fetch(url).then(response => response.json()); const extractPosts = redditPage => redditPage.data.children; const extractPostTextAndTitle = post => post.data.title + '\n' + post.data.selftext; const countWords = text => _wordCount(text); const numberValueSorter = (a, b) => a - b; const calculateMedian = list => { if (list.length == 0) return undefined; const sorted = Array.from(list).sort(numberValueSorter); if (sorted.length % 2 == 0) { const a = sorted.length / 2 - 1; const b = a + 1; return (list[a] + list[b]) / 2; } else { const i = Math.floor(sorted.length / 2); return list[i]; } } const pipeline = (...steps) => { return async input => { let result = input; for (const step of steps) result = await step(result); return result; }; }; const map = (...mappers) => async array => { const results = []; for (const value of array) { let result = value; for (const mapper of mappers) result = await mapper(result); results.push(result); } return results; }; const countComments = post => post.data.num_comments; const hasImageAttached = post => post.data.post_hint == 'image'; const calculateRatio = array => { if (array.length == 0) return undefined; return array.filter(value => !!value).length / array.length; }; const fork = (...pipelines) => async value => await Promise.all(pipelines.map(pipeline => pipeline(value)));` `const getMedianWordCount = pipeline( map( extractPostTextAndTitle, countWords ), calculateMedian ); const getMedianCommentCount = pipeline( map(countComments), calculateMedian ); const getImagePresentRatio = pipeline( map(hasImageAttached), calculateRatio ); // this is a convenience function that associates names to the results returned const joinResults = ([ medianWordCount, medianCommentCount, imagePresentRatio ]) => ({ medianWordCount, medianCommentCount, imagePresentRatio }); const getSubredditMetrics = pipeline( getRedditJSONUrl, fetchData, extractPosts, fork( getMedianWordCount, getMedianCommentCount, getImagePresentRatio ), joinResults ); const URL = 'https://www.reddit.com/r/dataisbeautiful/'; getSubredditMetrics(URL) .then(results => console.log('Report for ' + URL, results));`

[![](img/2cc83056ac5c063058e2fd1770a79fd5.png)T2】](https://i.giphy.com/media/ChzfTLSi47FYc/giphy.gif)

## 最后一个魔术

还和我在一起吗？好的，还记得当我们开始角色扮演时，我们想要为一个列表或 URL 生成这些报告，而不仅仅是一个？我们是否可以创建一个类似于 T2 的进程流程(T3 ),接受一个数组或 URL 并返回一个报告数组？

也许吧。

我们来分解一下这个问题。我们有一个 URL 数组。我们知道我们可以通过管道传递每个 URL，并返回解析到报告的承诺。如果我们用管道映射 URL 数组，那么我们得到的是承诺数组。

我们已经知道如何解决一系列的承诺！

```
const distribute = pipeline =>  // distribute takes a pipeline,
  values =>                     // and returns a function that takes a list of values;
    Promise.all(                // it returns a promise of all the values...
      values.map(pipeline)      // ...passed through each pipeline
    ); 
```

Enter fullscreen mode Exit fullscreen mode

是的，我想这就够了！让我们通过传递一组 URL 来试验一下，看看它是如何工作的:

`const fetch = require('node-fetch'); const _wordCount = require('@iarna/word-count'); const getRedditJSONUrl = url => url.replace(/\/?$/, '.json'); const fetchData = url => fetch(url).then(response => response.json()); const extractPosts = redditPage => redditPage.data.children; const extractPostTextAndTitle = post => post.data.title + '\n' + post.data.selftext; const countWords = text => _wordCount(text); const numberValueSorter = (a, b) => a - b; const calculateMedian = list => { if (list.length == 0) return undefined; const sorted = Array.from(list).sort(numberValueSorter); if (sorted.length % 2 == 0) { const a = sorted.length / 2 - 1; const b = a + 1; return (list[a] + list[b]) / 2; } else { const i = Math.floor(sorted.length / 2); return list[i]; } } const pipeline = (...steps) => { return async input => { let result = input; for (const step of steps) result = await step(result); return result; }; }; const map = (...mappers) => async array => { const results = []; for (const value of array) { let result = value; for (const mapper of mappers) result = await mapper(result); results.push(result); } return results; }; const countComments = post => post.data.num_comments; const hasImageAttached = post => post.data.post_hint == 'image'; const calculateRatio = array => { if (array.length == 0) return undefined; return array.filter(value => !!value).length / array.length; }; const fork = (...pipelines) => async value => await Promise.all(pipelines.map(pipeline => pipeline(value))); const getMedianWordCount = pipeline( map( extractPostTextAndTitle, countWords ), calculateMedian ); const getMedianCommentCount = pipeline( map(countComments), calculateMedian ); const getImagePresentRatio = pipeline( map(hasImageAttached), calculateRatio ); // this is a convenience function that associates names to the results returned const joinResults = ([ medianWordCount, medianCommentCount, imagePresentRatio ]) => ({ medianWordCount, medianCommentCount, imagePresentRatio }); const getSubredditMetrics = pipeline( getRedditJSONUrl, fetchData, extractPosts, fork( getMedianWordCount, getMedianCommentCount, getImagePresentRatio ), joinResults );` `const distribute = pipeline => values => Promise.all(values.map(pipeline)); const URLs = [ 'https://www.reddit.com/r/dataisbeautiful/', 'https://www.reddit.com/r/proceduralgeneration/' ]; const getAllReports = distribute(getSubredditMetrics); getAllReports (URLs) .then(results => { const reports = results.map((report, idx) => ({ url: URLs[idx], report })); console.log(reports); });`

[![](img/c5f629beedccdb844b1ac5ef64519e58.png)T2】](https://i.giphy.com/media/RWFpHUbc6s492/giphy.gif)

## ...从此他们幸福地生活在一起。

恭喜你走到这一步！您已经成功地从头开始设计和开发了整个异步协调机制系统，这不是一件容易的事情。

总结一下，让我们提取我们用来构建流程函数的通用实用函数，并使它们作为模块可用:

```
export const pipeline = (...steps) =>
  async input => {
    let result = input;
    for (const step of steps)
      result = await step(result);
    return result;
  };

export const map = (...mappers) =>
  async array => {
    const results = [];
    for (const value of array) {
      let result = value;
      for (const mapper of mappers)
        result = await mapper(result);
      results.push(result);
    }
    return results;
  };

export const fork = (...pipelines) =>
  async value =>
    await Promise.all(
      pipelines.map(pipeline => pipeline(value))
    );

export const distribute = pipeline =>
  values =>
    Promise.all(
      values.map(pipeline)
    ); 
```

Enter fullscreen mode Exit fullscreen mode

仅仅使用这四个函数，我们就成功地构建了一套完整的通用原语，可以在不到 350 个字符的小型代码中处理有限的工作量。😉

你现在可以脱下角色扮演服装了。

[![](img/411595fc835a4b9874cbbc428927c3f6.png)T2】](https://i.giphy.com/media/X6SPa7Q4BIc7K/giphy.gif)