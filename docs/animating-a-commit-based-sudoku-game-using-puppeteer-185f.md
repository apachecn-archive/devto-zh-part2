# 使用木偶戏制作基于提交的数独游戏动画

> 原文：<https://dev.to/anishkny/animating-a-commit-based-sudoku-game-using-puppeteer-185f>

几个月前，我发现了一个有趣的 GitHub 回购:

## ![GitHub logo](../Images/375dfcc32199b4dedf2b526645c27ff7.png) [ xiegeo ](https://github.com/xiegeo) / [提交-数独](https://github.com/xiegeo/commit-sudoku)

### 接受拉请求，共同完成一个数独游戏。

<article class="markdown-body entry-content p-5" itemprop="text">

# 提交数独

[![Build Status](../Images/910f45a9620b2e9d728600d701bc5b8a.png)T2】](https://travis-ci.org/xiegeo/commit-sudoku)

| one | five | seven | Two | nine | six | eight | three | four |
| eight | nine | Two | three | four | five | six | one | seven |
| four | three | six | one | eight | seven | Two | five | nine |
| seven | four | one | six | five | Two | three | nine | eight |
| nine | Two | eight | seven | three | four | five | six | one |
| three | six | five | eight | one | nine | four | seven | Two |
| six | eight | four | nine | seven | three | one | Two | five |
| Two | one | nine | five | six | eight | seven | four | three |
| five | seven | three | four | Two | one | nine | eight | six |

## 规则

有两种类型的拉请求，移动和其他。

一步棋只能包括在拼图的一个单元格中填入一个数字。

*   一个玩家一天最多只能走一步。
*   玩家可以移除自己所做的任何移动，而不受上述限制。
*   按照第一次提交的顺序接受移动。
*   跳过冲突的移动，它们可以重新提交…

</article>

[View on GitHub](https://github.com/xiegeo/commit-sudoku)

它说这是*“接受拉动请求，共同完成一个数独游戏”*。我被迷住了。这个想法很简单。作者设置了一个空的[数独](https://en.wikipedia.org/wiki/Sudoku)棋盘，任何人都可以在上面添加一个数字，只要它是有效的数独棋步(&最多 1 步/天/作者)。很快我就发现自己在发送 PRs 在这里添加一个[4](https://github.com/xiegeo/commit-sudoku/commit/9a1934f61821835167f4533f13db911299e66e36)，或者在那里添加一个[7](https://github.com/xiegeo/commit-sudoku/commit/bad1ae0c46289740e23f986f9962dc1449546a43)。

指定您的预期移动有点困难，因为在回购的`README.md`文件中，棋盘被表示为一个简单的 HTML 表格。

```
<table>
  <tr>
    <td>1
    <td>&nbsp;
    <td>7
    <td>&nbsp;
    <td>9
    <td>&nbsp;
    <td>&nbsp;
    <td>&nbsp;
    <td>4
    ... 
```

所以我发了一个增强 PR [在板子](https://github.com/xiegeo/commit-sudoku/pull/32/files)上嵌入行号——瞧！-不仅 PR 被接受，而且作者 [@xiegeo](https://github.com/xiegeo) 甚至向我提供了主回购的提交权限！我兴奋极了！很快，随着发出“移动”公关，我也开始检查和合并其他贡献者发出的公关，并做一般的内务处理任务。

经过更长时间的拖延，当我回到回购处时，我注意到黑板上的内容比我记忆中的要多得多。这让我开始思考——从第一步开始，棋盘是如何演变的？所以我决定尝试从提交历史中动画化数独棋盘状态的演变。

## 第一步:获取提交列表

首先，我需要获得一个以相反顺序(最早的优先)提交给回购的提交 sha 列表。

```
const commitSHAs = execSync('git log --reverse --no-merges --format=format:%H')
  .toString().split('\n'); 
```

## 步骤 2:获取每次提交的数独状态

接下来，对于每次提交，我需要从回购的`README.md`文件中提取电路板的状态。我用`git show`做到了这一点。

```
const readmeContents = execSync(`git show ${commitSHA}:README.md`)
  .toString().split('\n'); 
```

## 第三步:在木偶师中渲染每个状态

现在有趣的部分来了。对于每次需要以 PNG 格式呈现的提交，我都以 HTML 的形式保存了棋盘的状态。为此，我使用了[木偶师](https://pptr.dev)——一个用于控制无头 Chrome 的高级 Node.js API。

```
await page.goto(`data:text/HTML,${HTMLHeader}  ${tableState}`);
await page.screenshot({ path: `tmp/T${new Date().getTime()}.png` }); 
```

这里的`tableState`包含了在前面的步骤中从`README.md`文件中获取的表示表格状态的 HTML。`HTMLHeader`是一个套用 GitHub 风格的小样板。

很有趣。它被称为[数据 URL](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs) 。基本上你可以把任何 HTML 放在它后面，浏览器就会呈现出来。比如点击这个网址: [`data:text/HTML,<h1>hello, world!</h1>`](https://dev.todata:text/HTML,<h1>hello,%20world!</h1) 。

*注意:规范推荐`text/html`(小写)而不是`text/HTML`(大写)，但是我在这篇博客中对它的正确表达有问题。两者似乎都有效。*

## 第四步:制作动画 GIF

最后，我使用优秀的 [`gifencoder`](https://github.com/eugeneware/gifencoder) 包从上一步生成的 PNG 文件列表中生成一个动画 GIF。

```
pngFileStream(`tmp/T*.png`)
  .pipe(encoder.createWriteStream({ repeat: 0, delay: 200, quality: 50 }))
  .pipe(fs.createWriteStream(outputGIF)); 
```

# 结果

这是最终的动画，展示了游戏是如何进行的:

[![animation](../Images/561259728ef727000d96283a93706e33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3jF5pSyL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://commit-sudoku.surge.sh/output.gif)

## 第五步(加成):挂钩 TravisCI 和 Surge.sh

我不想每次提交时都在我的机器上手动运行这个脚本来更新动画。首先，我错过了一些回购的提交，这就是为什么我一开始就走上这条路。幸运的是，让 TravisCI 在每次提交(到 master)时运行这个脚本是轻而易举的事情。

```
after_success:
  - if [ "$TRAVIS_BRANCH" = "master" -a "$TRAVIS_PULL_REQUEST" = "false" ]; then cd animation && npm install && npm run generate; fi 
```

这告诉 TravisCI 只有在实际提交给 master 并且所有测试都通过的情况下才生成动画(即数独板处于良好状态)。

最后，我将生成的 GIF 上传到[surge . sh](https://surge.sh)——它提供简单的静态资产托管——以便它可以在回购的`README.md`中展示。

```
SURGE_LOGIN=xxxxxxx@xxxx.com SURGE_TOKEN=xxxxxxxxxx npx surge folder my-endpoint.surge.sh 
```

就是这样！

为了简洁起见，这篇文章中忽略了一些细节——但是你可以在这里挖掘完整的代码[——包括你如何在本地运行它。](https://github.com/xiegeo/commit-sudoku/tree/master/animation)

让我知道你对这篇文章的看法！

干杯！