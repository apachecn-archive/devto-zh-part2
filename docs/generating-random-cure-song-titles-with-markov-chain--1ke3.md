# 用马尔可夫链生成随机治愈歌曲标题

> 原文：<https://dev.to/raymondcamden/generating-random-cure-song-titles-with-markov-chain--1ke3>

在你继续之前，请注意这篇博文绝对没有任何有价值的内容。这是我昨晚想到的一个愚蠢的想法，今天早上我决定快速构建它。成功了。它让我发笑。但是这里没有任何有价值的东西。如果你的老板发现你在看这个，你很可能会被解雇。已经警告过你了。

因此，在我的理解中,[马尔可夫链](https://en.wikipedia.org/wiki/Markov_chain)是一种基于一组初始输入来确定一个值之后会出现另一个值的方法。因此，给定一组数据，比如说单词，你可以确定哪个单词最有可能出现在另一个单词之后。你可以找到一个很好的例子来生成逼真的电影标题:[“使用 Javascript 和马尔可夫链生成文本”](https://www.soliantconsulting.com/blog/title-generator-using-markov-chains)。不幸的是，博客中的代码样本被破坏了，但是这些例子非常有趣。

我做了一个快速搜索，发现了一个很棒的 npm 库，它简化了创建这样的演示: [titlegen](https://www.npmjs.com/package/titlegen) 。从文档中，这里有一个简单易用的例子:

```
var generator = titlegen.create();

generator.feed([
  'You Only Live Twice',
  'From Russia with Love',
  'The Man with the Golden Gun',
  'Live and Let Die',
  'Die Another Day'
]);

console.log(generator.next()); // -> "From Russia with the Golden Gun" 
console.log(generator.next()); // -> "You Only Live and Let Die Another Day" 
console.log(generator.next()); // -> "The Man with Love" 
```

Enter fullscreen mode Exit fullscreen mode

很酷，对吧？所以我想-如果我用治愈歌曲来试试呢？我从[维基百科](https://en.wikipedia.org/wiki/Category:The_Cure_songs)上搜集了一些内容，做了一些清理，然后制作了这个演示:

[https://cfjedimaster . github . io/web demos/generate cure/title gen . html](https://cfjedimaster.github.io/webdemos/generateCure/titlegen.html)

如果你不想点击，这里有一些例子:

[![Example Cure Songs](img/b0981882ec5c660dde159dbd87a3b339.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tGwN2OKn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/1/cure1.jpg)
[![Example Cure Songs](img/aaaf0077aa7b2ab766a547cc3abc7225.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n9SZEpJ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/1/cure2.jpg)
[![Example Cure Songs](img/024227630f4b174e9a190b4556232026.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iPOrfOW0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/1/cure3.jpg)
[![Example Cure Songs](img/2445366189f12c3ecd9732145526d8bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iS1d6urz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/1/cure4.jpg)

演示是一个愚蠢的简单的 Vue 应用程序。布局只有几个标签，所以我将跳过它，但这里是 JavaScript。注意我已经删除了大部分的治疗标题，以使它更短:

```
// source: https://en.wikipedia.org/wiki/Category:The_Cure_songs
let input = `10:15 Saturday Night
The 13th
Accuracy
LOTS OF STUFF REMOVED
The Walk
Why Can't I Be You?
Wrong Number`;

input = input.split('\n');

var generator = titlegen.create();
generator.feed(input);

const app = new Vue({
    el:'#app',
    data() {
        return {
            title:""
        }
    },
    created() {
        this.newTitle();
    },
    methods: {
        newTitle() {
            console.log('generating cureness');
            this.title = generator.next();
        }
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我不认为我理解这背后的 1%的数学，我不知道这有多现实，但我的上帝让我笑了。如果想看全部代码，可以在这里找到:[https://github . com/cfjedimaster/web demos/tree/master/generate cure](https://github.com/cfjedimaster/webdemos/tree/master/generateCure)

哦，最后，你可以在这里测试一个 Depeche 模式版本:[https://cfjedimaster . github . io/web demos/generateDepecheMode/title gen . html](https://cfjedimaster.github.io/webdemos/generateDepecheMode/titlegen.html)