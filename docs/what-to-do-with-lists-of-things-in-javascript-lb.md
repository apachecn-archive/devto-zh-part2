# 如何处理 JavaScript 中的列表

> 原文：<https://dev.to/krofdrakula/what-to-do-with-lists-of-things-in-javascript-lb>

*封面图片由[互联网档案馆图书图片](https://www.flickr.com/photos/internetarchivebookimages/14587278329/)T3】*

与大多数语言一样，在 JavaScript 中，我们有一个处理值列表的数据结构。这是一个非常方便的对象，可以让我们在一个有序列表中对值进行分组。但是 JavaScript 中的数组不仅仅是字符串索引和`length`属性。

JavaScript 借用了函数式编程语言在其标准库中实现的一些函数，并通过将它们绑定到`Array`原型使它们变得更加方便。在后续的文章中，我们将看到如何应用函数式方法来编写比标准过程代码更好的程序。

但首先，让我们深入基础。

## 第 1 部分:搜索

有很多方法可以剥这只猫的皮，这取决于你想达到什么目的。让我们来看一个有趣的数据源，它提供了一个列表，我们可以在上面练习 JS-fu:

``// we set up the data fetch and hand the data // to our main function const fetch = require('node-fetch'); const SOURCE_URL = 'https://www.reddit.com/r/reactiongifs.json'; fetch(SOURCE_URL) .then(response => response.json()) .then(main) .catch(err => console.error(err)); // ----[main]---- function main(json) { // here's where we deal with the data console.log(json.data.children); }`` `我们将在 Reddit 上使用 [`/r/reactiongifs`](https://www.reddit.com/r/reactiongifs/) 。运行上面的例子，看看我们在处理什么。

*提示:*任何 Reddit 页面都可以通过在 URL 后面加上后缀`.json`以 JSON 格式获取。试试看！

### 问题:每个列表项都符合特定的标准吗？

假设我们想要检查列表中的每个帖子在`title`字段中都包含缩写`MRW`。为此，我们使用 [`every()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every) 功能进行列表。

`// we set up the data fetch and hand the data // to our main function const fetch = require('node-fetch'); const SOURCE_URL = 'https://www.reddit.com/r/reactiongifs.json'; fetch(SOURCE_URL) .then(response => response.json()) .then(main) .catch(err => console.error(err));` `const postTitleContainsMRW = post => post.data.title.includes('MRW'); function main(json) { const posts = json.data.children; const eachContainsMRW = posts.every(postTitleContainsMRW); console.log('Every post contains MRW?', eachContainsMRW); }`

*注意:*当提供给`every()`的函数返回`false`时，停止对数组的迭代，立即返回`false`。如果数组中的所有项都解析为`true`，则返回`true`。

### 问题:列表中是否包含符合标准的*项？*

好吧，如果我们只想检查*是否有任何*值匹配呢？让我们用 [`some()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some) 来寻找题目中的`cat`这个词。

`// we set up the data fetch and hand the data // to our main function const fetch = require('node-fetch'); const SOURCE_URL = 'https://www.reddit.com/r/reactiongifs.json'; fetch(SOURCE_URL) .then(response => response.json()) .then(main) .catch(err => console.error(err));` `const postTitleContainsCat = post => post.data.title.includes('cat'); function main(json) { const posts = json.data.children; const anyContainsCat = posts.some(postTitleContainsCat); console.log('Does any post contain the word cat?', anyContainsCat); }`

*注意:*由于该函数是`every()`的补码，所以只要第一项解析到`true`就会停止迭代。如果没有一项解析为`true`，则返回`false`。

### 问题:列表中第一个符合条件的项目是什么？

假设上面的答案是正确的(毕竟是动态数据！)，让我们找到第一个包含了单词`cat`的帖子。对于这一点，我们可以用 [`find()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find) 。

`// we set up the data fetch and hand the data // to our main function const fetch = require('node-fetch'); const SOURCE_URL = 'https://www.reddit.com/r/reactiongifs.json'; fetch(SOURCE_URL) .then(response => response.json()) .then(main) .catch(err => console.error(err));` `const postTitleContainsCat = post => post.data.title.includes('cat'); function main(json) { const posts = json.data.children; const catPost = posts.find(postTitleContainsCat); console.log(catPost); }`

如果没有找到元素，则返回`undefined`。

### 问题:第一个找到的项目在哪个位置？

只要把`find()`替换成 [`findIndex()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex) 就行了！

`// we set up the data fetch and hand the data // to our main function const fetch = require('node-fetch'); const SOURCE_URL = 'https://www.reddit.com/r/reactiongifs.json'; fetch(SOURCE_URL) .then(response => response.json()) .then(main) .catch(err => console.error(err));` `const postTitleContainsCat = post => post.data.title.includes('cat') function main(json) { const posts = json.data.children; const catPostIndex = posts.findIndex(postTitleContainsCat); console.log(catPostIndex); }`

## 第 2 部分:转型

到目前为止，上面描述的方法只扫描内容，但其他更有用的方法允许我们将数组转换成其他东西。不过，让我们从基础开始。

### 任务:获取符合标准的帖子列表

以前，我们只关心数组中的单个(第一个)值。其余的呢？ [`filter()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 让你做到这一点。

`// we set up the data fetch and hand the data // to our main function const fetch = require('node-fetch'); const SOURCE_URL = 'https://www.reddit.com/r/reactiongifs.json'; fetch(SOURCE_URL) .then(response => response.json()) .then(main) .catch(err => console.error(err));` `const postTitleContainsCat = post => post.data.title.includes('cat'); function main(json) { const posts = json.data.children; const postsWithCats = posts.filter(postTitleContainsCat); console.log(postsWithCats); }`

### 任务:转换数组中的每一项

有时我们需要将一个对象转换成不同的格式，以供其他组件或函数使用。在这种情况下，我们可以使用 [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 功能。

`// we set up the data fetch and hand the data // to our main function const fetch = require('node-fetch'); const SOURCE_URL = 'https://www.reddit.com/r/reactiongifs.json'; fetch(SOURCE_URL) .then(response => response.json()) .then(main) .catch(err => console.error(err));` `const simplifyPost = post => ({ title: post.data.title, image: post.data.thumbnail, animation: post.data.url }); function main(json) { const posts = json.data.children; const simplerPosts = posts.map(simplifyPost); console.log(simplerPosts); }`

*注意:* `map()`返回一个新的项目数组，不改变原数组。

### 任务:创建项目列表的摘要

如果你需要在一个项目列表上产生任何类型的求和、汇总或转换，那么 [`reduce()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 就是你要走的路。这个操作的要点是，你给它一个初始值，提供给它的函数在依次处理每一项后，会返回下一个值。

对于这个例子，让我们为标题中使用的所有单词创建一个`Set`。`Set`非常有用，因为它们负责对集合中已有的项目进行重复数据删除。

`// we set up the data fetch and hand the data // to our main function const fetch = require('node-fetch'); const SOURCE_URL = 'https://www.reddit.com/r/reactiongifs.json'; fetch(SOURCE_URL) .then(response => response.json()) .then(main) .catch(err => console.error(err));` `const addWordsFromTitle = (set, post) => { // we lowercase the title first const title = post.data.title.toLowerCase(); // we split along every word boundary which isn't an apostrophe const words = title.split(/[^\w']+/); // for each non-empty word, we add it to the set words.filter(word => word.length > 0) .forEach(word => set.add(word)); // IMPORTANT: we return the set as the next value return set; }; function main(json) { const posts = json.data.children; // NOTE: here we start with an empty set and add words to it const allWords = posts.reduce(addWordsFromTitle, new Set()); console.log(allWords); }`

这是一个*非常*强大的转换方法，可以表达你能想到的几乎任何类型的操作，包括上面描述的所有操作！如果你想快速体验一下只用`reduce`(或者函数式语言中称之为`fold`)就能做的事情，看看下面布莱恩·朗斯多夫的演讲:

[https://www.youtube.com/embed/JZSoPZUoR58](https://www.youtube.com/embed/JZSoPZUoR58)

### 任务:对列表中的项目排序

如果我们想要对任意值进行排序，我们需要提供一个比较器，这样我们就可以告诉排序算法关于排序的信息。为此，我们需要提供一个函数，该函数从数组中取出两项，并返回三个值之一:

*   `-1`:第一项应该在第二项之前的时候(任何负数都可以)
*   `0`:两个项目顺序相等时
*   第二个项目应该在第一个项目之前(任何正数都可以)

让我们根据标题长度按降序对项目进行排序(最长的先排序)。如果两个标题长度相同，按字母顺序排列。

`// we set up the data fetch and hand the data // to our main function const fetch = require('node-fetch'); const SOURCE_URL = 'https://www.reddit.com/r/reactiongifs.json'; fetch(SOURCE_URL) .then(response => response.json()) .then(main) .catch(err => console.error(err));` `const comparePosts = (a, b) => { const titleA = a.data.title.toLowerCase(); const titleB = b.data.title.toLowerCase(); if (titleA.length > titleB.length) return -1; if (titleA.length < titleB.length) return 1; return titleA.localeCompare(titleB, 'en', { sensitivity: 'base' }); }; function main(json) { // Array.from() creates a copy of the array so that we don't // modify the original data const posts = Array.from(json.data.children); posts.sort(comparePosts); console.log(posts); }`

*注意:* `sort()`对数组进行就地排序，表示原始数组被修改。

## 包扎

这篇文章只讲述了数组方法的基础知识，当我们开始在例子中实现一个更函数化的方法时，我们会用到这些知识。在此之前，请记住，无论何时您觉得需要在`Array`之上编写一个`for`循环，可能有一种方法可以使用上述方法编写相同的内容。

保持好奇！`