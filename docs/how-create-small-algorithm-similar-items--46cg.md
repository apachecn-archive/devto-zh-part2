# 如何创建小的“算法相似项”

> 原文：<https://dev.to/gaserd/how-create-small-algorithm-similar-items--46cg>

嗨，很久以前我做了一个小搜索引擎。

我用 fts vector 在你数据库里搜索。

但我的项目需要类似的项目，我认为有必要采取一些复杂的算法，并实现它，但我可以用小而简单的算法从我的数据库中搜索类似的项目。

我开始搜索谷歌...哦，我的上帝，非常有趣的算法，但我没有看到节点。对不起，可能是我不好用谷歌。

嗯嗯停！我有 fts 向量，我们有快速算法 Levenstein。是啊。

FTS 向量-这是一个小矩阵，包含文本中非常重要的单词。我的数据库中有这样的结构:

项目
id |标题|价格|描述| fts

标题和描述的一般总和。

我们可以在 Levenstein 算法或其他算法中比较这些向量。这很容易。

好吧，怎么开始呢？

首先我给自己写了算法 Levenstein，但是他太慢了...而且我发现 NPM-package->[fast-leven stein](https://github.com/hiddentao/fast-levenshtein)

好了，去写代码吧。

```
 npm install fast-levenstein 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我创建了这个文件。

```
 var levenshtein = require('fast-levenshtein');

    var getSimilarItems = function(id,func) {
        //....
        //code of get items, sort this items
        //....

        let levenshtein_number = levenshtein.get(data[0].fts, category_items[i].fts)

        //compare numbers and return necessary       

    }

    module.exports.getSimilarItems = getSimilarItems 
```

Enter fullscreen mode Exit fullscreen mode

[![JUST DO IT](img/47d244820cde7724f23a4d2c7eaa4aeb.png)T2】](https://i.giphy.com/media/14pBFSq5dRDkFa/giphy.gif)

仅此而已。这个你怎么看，我什么做的比较好？