# 使用数据科学寻找最重要的句子

> 原文：<https://dev.to/brandonskerritt/finding-the-most-important-sentences-using-data-science-1dg9>

GitHub 回购在这里是:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [蜂散](https://github.com/bee-san) / [ tldr-News](https://github.com/bee-san/tldr-News)

### 🏖️生成一个 TL；使用自然语言处理的新闻博士🏖️

<article class="markdown-body entry-content container-lg" itemprop="text">

# TL；速度三角形定位法(dead reckoning)

使用算法确定新闻文章中最重要的句子，并将其显示在新闻文章的顶部。仅适用于 BBC 新闻文章。也不是一个合适的 Firefox 扩展(尽管您可以自由创建一个)。

[![img](img/2f9a1581766316a2dfb31507487ea0d3.png)T2】](https://camo.githubusercontent.com/c9114c76c7f59177d7d7815af12f77e8303bea1070dc4c1b210d0766f60d3d2f/68747470733a2f2f736b6572726974742e626c6f672f636f6e74656e742f696d616765732f323031392f30312f696d6167652d3531322e706e67)

红框内的所有内容都被算法选为最重要的句子，从最重要到最不重要排序。

# 什么算法？

术语频率*逆文档频率

我在下面的博文中解释了所有的代码和算法。享受

[https://skerritt.blog/tfidf/](https://skerritt.blog/tfidf/)

</article>

[View on GitHub](https://github.com/bee-san/tldr-News)

我们将创建 BBC 新闻文章的摘要，并使用 Firefox 扩展将它们放在顶部。本文是关于正则算法词频-逆文档频(TF-IDF)的。我们将以 Firefox 扩展的形式创建一个真实世界的用法。我知道你在想什么。“TF-IDF？裂开😴“不过萧然跟我在一起，倒是挺有意思的！

当我们完成时，它会看起来像这样:

<figure>[![](img/e3c7fbe959a9dacc3e7968ccf240f35d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J1MJVpeH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/666/1%2Ab01QNPpkOMWG1g-GLuab4Q.png) 

<figcaption>新闻文章找到[这里](https://www.bbc.co.uk/news/business-45289432)。红框突出了最重要的句子。红框不在最终产品中，仅用于说明目的。</figcaption>

</figure>

我向你保证，这并不像算法的名字所描述的那样困难/无聊！

### 术语频率*逆文档频率

别担心，算法的名字让我每次听到它大声说出来都会睡着。这个算法是两个算法相乘。让我们看看这两者是如何工作的:

#### 术语频率

词频(TF)是一个词在文档中出现的频率，除以有多少个词。

<figure>[![](img/450ccae61ddb70a18472d874686b9635.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_q8_zNlJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/597/1%2AF4O-l5YkFEMU4WJQZOoBQw.png) 

<figcaption>一个术语的词频(TF)t，和一个文档的词频 d.</figcaption>

</figure>

假设你正在阅读一篇关于英国退出欧盟的新闻。“英国退出欧盟”这个词会出现很多，所以“英国退出欧盟”这个词的出现频率很高。

很多时候，我们希望在术语旁边建立一个术语频率字典(hashmap)。比如 *{word:那个词的词频}* 然后迭代这个字典，找出哪个词出现的次数最多。

现在，如果我告诉你词频词典看起来有点像这样会怎么样:

```
{"and": 0.87, "the": 0.73} 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到这些普通的英语单词对我们没有用。当然，大多数英语文本中都有这些单词，但我们称这些英语单词为*停用词*。停用词通常指一种语言中最常见的词，尽管没有单一的定义。你必须根据用法选择停用词。你必须决定用什么词。在处理一些文本之前，您通常希望删除停用词，以便更好地处理文本。

有大写字母的单词和没有大写字母的单词是不同的。在编程中，“非洲”和“非洲”是两回事。因此，我们希望将所有内容都转换成小写或大写，以便更好地处理我们的文本。我们要把所有的单词都变成小写。

给定一个字符串，我们想删除停用词并将其转换成小写。我们的扩展将给我们一个 BBC 新闻文章的所有文本的字符串。先不要担心我们从哪里得到文本，这将在稍后的 Firefox 扩展部分完成。现在，假设我们有这样的文本:

```
... These are external links and will open in a new windowA neat feature of many modern laptops is the ability to power them up through the USB port. Unlike the rectangular USB ports of old, the newer type - USB-C - can carry enough power to charge your 
machine.That’s great news: it means ... 
```

Enter fullscreen mode Exit fullscreen mode

为了防止读者睡着，上面的文字被缩短了。

```
function prettify(document){
    // Turns an array of words into lowercase and removes stopwords
    const stopwords = ["a", "share", "linkthese", "about", "above", "after", "again", "against", "all", "am", "an", "and", "any","", "are","aren't","as","at","be","because","been","before","being","below","between","both","but","by","can't","cannot","could","couldn't","did","didn't","do","does","doesn't","doing","don't","down","during","each","few","for","from","further","had","hadn't","has","hasn't","have","haven't","having","he","he'd","he'll","he's","her","here","here's","hers","herself","him","himself","his","how","how's","i","i'd","i'll","i'm","i've","if","in","into","is","isn't","it","it's","its","itself","let's","me","more","most","mustn't","my","myself","no","nor","not","of","off","on","once","only","or","other","ought","our","ours","ourselves","out","over","own","same","shan't","she","she'd","she'll","she's","should","shouldn't","so","some","such","than","that","that's","the","their","theirs","them","themselves","then","there","there's","these","they","they'd","they'll","they're","they've","this","those","through","to","too","under","until","up","very","was","wasn't","we","we'd","we'll","we're","we've","were","weren't","what","what's","when","when's","where","where's","which","while","who","who's","whom","why","why's","with","won't","would","wouldn't","you","you'd","you'll","you're","you've","your","yours","yourself","yourselves", "this"];
    // turn document into lowercase words, remove all stopwords
    var document = document.replace(/[.,]/g, '');
    let document_in_lowercase = document.split("  ").map(function(x){ return x.toLowerCase() });
    return document_in_lowercase.filter( x => !stopwords.includes(x) );
} 
```

Enter fullscreen mode Exit fullscreen mode

这是将“美化”我们的文档的功能。第 3 行是我在 [StackOverflow](https://stackoverflow.com/questions/5631422/stop-word-removal-in-javascript) 上找到的一组停用词。我添加了“*共享*和“*链接这些*”，因为这些是我们不想要的新闻文章中的常用词。

第 5 行是[正则表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)。方括号表示或。[,.]表示*“在逗号或句号上激活”*。/g 表示全局。一旦你找到一个*、*或*。”*不要停，继续搜索字符串。空字符串就是我们要替换的。如果我们发现一个句号或一个逗号，不用任何东西代替它——删除它。这是因为“非洲”这个词。如果没有这个，“非洲”会被归类为两个不同的词。

<figure>[![](img/9dab5bb2db115693545e63e06a5d6164.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PyljUgwk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AtBAPevEG7SV1GQMXA8OQWg.jpeg) 

<figcaption>来自我的 Instagram， [@Brandon.codes](https://www.instagram.com/brandon.codes/)</figcaption>

</figure>

第 4 行将文档分割成单独的单词。 *map* 函数将函数应用于数组中的每个元素。一旦字符串被拆分成单词数组，我们就对每个元素应用 *toLowerCase()* 方法。它让每个单词都变成小写。

<figure>[![](img/610486276a6e9a1371c22c70d15918ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NE5nCepp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8aM6hM6ewnoY4j-dwC2cWw.png) 

<figcaption>来自我的 Instagram， [@Brandon.codes](https://www.instagram.com/brandon.codes/) 。箭头是[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) (Lambda /匿名函数)。</figcaption>

</figure>

一旦我们过滤掉停用的单词，我们就返回小写的单词。 *Filter()* 创建一个新数组，其中只包含函数返回 True 的元素。

如果一个单词是一个停用词，它将导致 True，这意味着我们将得到一个新的数组**只有**文档中的停用词。我们使用否定运算符“！”得到相反的结果，这正是我们想要的。返回没有停用词的单词列表。

现在我们要计算每个单词在文档中出现的次数。这对术语频率和反向文档频率都很有用。首先，我们希望从一个单词数组中获取所有唯一的单词。

```
function uniqueWords(words){
    const unique_words_set = new Set(words);
    return unique_words = Array.from(unique_words_set);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将数组转换成集合，因为集合没有重复。这让我们只能得到数组中唯一的**字。集合也没有顺序，所以我们不能使用数组索引来访问元素。我们需要把它直接转换回数组。关于集合论的更多内容，请查看我写的[这篇文章](https://medium.com/brandons-computer-science-notes/a-primer-on-set-theory-746cd0b13d13)。**

好了，现在来数一个单词在单词数组中出现了多少次。

```
function countWords(words){
    // returns a dictionary of {WORD: COUNT} where count is
    // how many times that word appears in "words".
    const unique_words = uniqueWords(words);
    let dict = {};
    // for every single unique word
    for (let i = 0; i <= unique_words.length - 1; i++){
        dict[unique_words[i]] = 0
        // see how many times this unique word appears in all words
        for (let x = 0; x <= words_without_stopwords.length -1; x++){
            if (unique_words[i] == words[x]){
                dict[unique_words[i]] = dict[unique_words[i]] + 1;
            }
        }
    }
    return dict;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数遍历每一个唯一的单词，并计算这个单词在单词数组中出现的次数。频率函数这个词挺长的，我就分解一下。

```
function termFrequency(document){
    // calculates term frequency of each sentence
    words_without_stopwords = prettify(document);

    // gets rid of trailing spaces
    const sentences = document.split(".").map(item => item.trim());
    sentences[0] = sentences[0].substring(146);

    const TFVals = countWords(words_without_stopwords)
    const unique_words = uniqueWords(words_without_stopwords);

    // actually makes it TF values according to formula
    for (const [key, value] of Object.entries(TFVals)){
        TFVals[key] = TFVals[key] / words_without_stopwords.length;
} 
```

Enter fullscreen mode Exit fullscreen mode

第 6 行将文档分成句子。有时句子前面有空格。“布兰登。狗。”在“狗”之前有空格我们将 [trim()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/Trim) 方法应用于每一项，以消除这些尾随的空白。

关于第 7 行，第一个单词的前 146 个字符是社交媒体链接。这个词的其余部分是标题或副标题。来，看:

```
Share this withEmailFacebookMessengerMessengerTwitterPinterestWhatsAppLinkedInCopy this linkThese are external links and will open in a new window **Ryanair is tightening the rules on what passengers pay to take luggage onto the plane, in order to "speed up boarding".** 
```

Enter fullscreen mode Exit fullscreen mode

这很烦人，因为标题是故事必不可少的一部分，需要考虑进去。所以我们去掉第一个单词的前 146 个字符得到:

```
Ryanair is tightening the rules on what passengers pay to take luggage onto the plane, in order to "speed up boarding" 
```

Enter fullscreen mode Exit fullscreen mode

还记得这个公式吗？

<figure>[![](img/450ccae61ddb70a18472d874686b9635.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_q8_zNlJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/597/1%2AF4O-l5YkFEMU4WJQZOoBQw.png) 

<figcaption>一个术语的词频(TF)t，和一个文档的词频 d.</figcaption>

</figure>

变量“ *TFVals* ”正在计算这个公式。如果我们运行句子“你好，我叫布兰登。布兰登布兰登。大象跳过月亮“通过项频率函数，我们会得到类似这样的东西:

<figure>[![](img/6e2384e81e16fb78c653e877d4e1315e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QinEfqUq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/846/1%2AuTEkBVplTb57czjE2ZiuNw.png) 

<figcaption>TF 只返回 1 个“布兰登”，所以不是每个布兰登都有 TF 分值。对于这个例子，记住 TF 只返回 1 Brandon，因为我们正在计算单个术语(单词)的频率。</figcaption>

</figure>

我们有词的词频，但我们想计算最重要的**句**，而不是词。为了做到这一点，我们仔细检查每一个句子，看看在那个句子中出现了哪些在 *TFVals* 中的单词。

<figure>[![](img/51774a8d2c28730a3d6c80a6e2659221.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5vGhYM1e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/854/1%2AGn5T98Vkm3C8hW87x2KpRQ.png) 

<figcaption>还记得之前我说的我们不要把 3 个“布兰登”都算进去吗？我现在已经做到了。另外，请注意，我们把它分成了几个句子。这幅图中有三个句子。</figcaption>

</figure>

我们只需要把它们加起来，然后除以我们有多少单词。因为我们只是把非停用词的 TF 值相加，所以如果我们除以一个句子中有多少个非停用词，而不是句子中有多少个词，这才是公平的。如果不除以单词量，长句比短句更有优势。

<figure>[![](img/03a2ff6fa9a4cc038acbde2a35a2b0ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iT8FfxvP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/863/1%2A3L16z-glMZjfjvrAaYTKcg.png)

<figcaption>TF 最高的句子是“布兰登布兰登”</figcaption>

</figure>

这就是下面第 20 行的内容。我们遍历每一个句子，计算每个句子的 TF 值，就像我们上面做的一样。

```
function termFrequency(document){
    // calculates term frequency of each sentence
    words_without_stopwords = prettify(document);

    // gets rid of trailing spaces
    const sentences = document.split(".").map(item => item.trim());
    sentences[0] = sentences[0].substring(146);

    const TFVals = countWords(words_without_stopwords)
    const unique_words = uniqueWords(words_without_stopwords);

    // actually makes it TF values according to formula
    for (const [key, value] of Object.entries(TFVals)){
        TFVals[key] = TFVals[key] / words_without_stopwords.length;
    }

    // splits it up into sentences now
    var TFSentences = {};
    // for every sentence
    for (let i = 0; i <= sentences.length - 1; i ++){
        // for every word in that sentence
        let sentence_split_words = sentences[i].split("  ");
        // get the assiocated TF values of each word
        // temp.add is the "TF" value of a sentence, we need to divide it at the end
        let temp_add = 0.0;
        let words_no_stop_words_length = prettify(sentences[i]).length;
        for (let x = 0; x <= sentence_split_words.length - 1; x++){
            // get the assiocated TF value and add it to temp_add
            if (sentence_split_words[x].toLowerCase() in TFVals){
                // adds all the TF values up
                temp_add = temp_add + TFVals[sentence_split_words[x].toLowerCase()];
            }
            else{
                // nothing, since it's a stop word.
            }
        }
        // TF sentences divide by X number of items on top
        TFSentences[sentences[i]] = temp_add / words_no_stop_words_length;
    }

    return TFSentences;
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。但是我们有一个问题，只用术语频率。正如你之前看到的，“Brandon Brandon”是我们看的所有三个句子中得分最高的 TF。

光有人气是不够的。我们不希望句子中包含最多的关键字，因为它们可能没有意义，或者它们可能是彼此的重复。如“布兰登”中的“布兰登”句。它的 TF 值很高，但内容不多。

它包含的信息不多，也没什么用。我们想要一个既罕见又独特的句子，并且包含文章中常见的关键词。这就是逆文档频率出现的原因。

#### 逆文档频率

词频是一个词有多常见，逆文档频率(IDF)是一个词有多独特或罕见。IDF 的公式为:

<figure>[![](img/6f57373de19e6950c4c0a93063a6c308.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U00ZS9Lp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/651/1%2AWD4Z2DxSrAicsKwuQm26wA.png) 

<figcaption>t 是术语，d 是单据。我们有多个文档，我们把每个句子都当作它自己的文档。</figcaption>

</figure>

IDF 使用了许多文档，而 TF 是为一个文档而构建的。你可以决定什么是文档。在本文中，每个句子都是它自己的文档。

IDF 的前几步和 TF 一样。我们美化文档，统计文档中的单词，并获得所有唯一的单词。

```
function inverseDocumentFrequency(document){
    // calculates the inverse document frequency of every sentence
    const words_without_stopwords = prettify(document);
    const unique_words_set = uniqueWords(words_without_stopwords);

    const sentences = document.split(".").map(item => item.trim());
    sentences[0] = sentences[0].substring(146);

    const lengthOfDocuments = sentences.length;
    // prettifys each sentence so it doesn't have stopwords

    const wordCountAll = countWords(words_without_stopwords);

    // counts words of each sentence
    // as each sentence is a document
    wordCountSentences = [];
    for (let i = 0; i <= lengthOfDocuments - 1; i ++){
        wordCountSentences.push(countWords(prettify(sentences[i])));
    }

    // calculate TF values of all documents
let IDFVals = {}; 
```

Enter fullscreen mode Exit fullscreen mode

第 1–6 行并不新鲜。第 17 行的 for 循环遍历文档中的每个句子。因为每个句子都是一个新的“文档”，所以我们需要单独计算每个句子的字数。我们必须美化它们，去掉停用词，把它们变成一组单词。我们将每个新句子的单词计数对象推入*单词计数句子*。

<figure>[![](img/caf83d1f6658d75f3d3e32305f729c52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mwgb-IFy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/559/1%2AO9Lv6-QTG07EEqnLQNZ3xA.png) 

<figcaption>这里有 3 个句子。我们不关心 Brandon 在整个文档中出现了多少次。我试图在不使数学变得太大的情况下，选择一些词语来给你一些有趣的东西，但在这个过程中，我变得自恋了。</figcaption>

</figure>

我们现在将检查每个单词，并计算该单词在每个句子中出现的次数，然后使用下面的公式计算 IDF 得分。

<figure>[![](img/a0e69c6874f2667d026c4421b97ae3a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iKMda3CZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/658/1%2AkO4fXaJdu9QhbwICxbvc1w.png) 

<figcaption>我在这里用 log 10。注意:所有的对数都是固定的[比例](https://www.khanacademy.org/math/algebra2/exponential-and-logarithmic-functions/logarithmic-scale/v/logarithmic-scale)，所以你用什么对数没多大关系。</figcaption>

</figure>

<figure>[![](img/c5eee3a2bba324a1c5917e1c748af663.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JMES2ddL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/472/1%2A8pJZji2F5uy6E6zAxKL06Q.png)

<figcaption>IDF 如何使用的一个小例子。</figcaption>

</figure>

现在我们对每个不停的单词都这样做。

[![](img/9e968b3ad3a8f869d8fd440907b23d5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Oj0s8BOm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/602/1%2AomR9oAN2-yDYfOcIv7bPEw.png)

这个的代码是:

```
function inverseDocumentFrequency(document){
    // calculates the inverse document frequency of every sentence
    const words_without_stopwords = prettify(document);
    const unique_words_set = uniqueWords(words_without_stopwords);

    const sentences = document.split(".").map(item => item.trim());
    sentences[0] = sentences[0].substring(146);

    const lengthOfDocuments = sentences.length;
    // prettifys each sentence so it doesn't have stopwords

    const wordCountAll = countWords(words_without_stopwords);

    // counts words of each sentence
    // as each sentence is a document
    wordCountSentences = [];
    for (let i = 0; i <= lengthOfDocuments - 1; i ++){
        wordCountSentences.push(countWords(prettify(sentences[i])));
    }

    // calculate TF values of all documents
    let IDFVals = {};

    // how many times that word appears in all sentences (documents)
    wordCountSentencesLength = wordCountSentences.length;
    // for every unique word
    for (let i = 0; i <= unique_words_set.length - 1; i++){
        let temp_add = 0;
        // count how many times unique word appears in all sentences
        for (let x = 0; x <= wordCountSentencesLength - 1; x++){
            if (unique_words_set[i] in wordCountSentences[x]){
                temp_add =+ 1;
            }
        }
        IDFVals[unique_words_set[i]] = Math.log10(wordCountAll[unique_words_set[i]] / temp_add);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们想得到所有句子的 IDF 值，我们在这里使用来自 TF 的相同代码，但是替换了一些东西来使它工作。

如果我实话实说，我做了一个简单的“查找和替换”变量。我把评论里的“TF”换成了 IDF。我把“ *TFVals* ”换成了“ *IDFVals* ”。这里没有发生什么重要的事情，所以可以跳过这一部分。

```
function inverseDocumentFrequency(document){
    // calculates the inverse document frequency of every sentence
    const words_without_stopwords = prettify(document);
    const unique_words_set = uniqueWords(words_without_stopwords);

    const sentences = document.split(".").map(item => item.trim());
    sentences[0] = sentences[0].substring(146);

    const lengthOfDocuments = sentences.length;
    // prettifys each sentence so it doesn't have stopwords

    const wordCountAll = countWords(words_without_stopwords);

    // counts words of each sentence
    // as each sentence is a document
    wordCountSentences = [];
    for (let i = 0; i <= lengthOfDocuments - 1; i ++){
        wordCountSentences.push(countWords(prettify(sentences[i])));
    }

    // calculate TF values of all documents
    let IDFVals = {};

    // how many times that word appears in all sentences (documents)
    wordCountSentencesLength = wordCountSentences.length;
    // for every unique word
    for (let i = 0; i <= unique_words_set.length - 1; i++){
        let temp_add = 0;
        // count how many times unique word appears in all sentences
        for (let x = 0; x <= wordCountSentencesLength - 1; x++){
            if (unique_words_set[i] in wordCountSentences[x]){
                temp_add =+ 1;
            }
        }
        IDFVals[unique_words_set[i]] = Math.log10(wordCountAll[unique_words_set[i]] / temp_add);
    }

    let IDFSentences = {};
    // for every sentence
    for (let i = 0; i <= lengthOfDocuments - 1; i ++){
        // for every word in that sentence
        let sentence_split_words = sentences[i].split("  ");
        // get the assiocated IDF values of each word
        // temp.add is the "IDF" value of a sentence, we need to divide it at the end
        let temp_add = 0.0;
        let words_no_stop_words_length = prettify(sentences[i]).length;
        for (let x = 0; x <= sentence_split_words.length - 1; x++){
            // if the word is not a stopword, get the assiocated IDF value and add it to temp_add
            if (sentence_split_words[x].toLowerCase() in IDFVals){
                // adds all the IDF values up
                temp_add = temp_add + IDFVals[sentence_split_words[x].toLowerCase()];
            }
            else{
                // nothing, since it's a stop word.
            }
        }
        IDFSentences[sentences[i]] = temp_add / words_no_stop_words_length;
    }
    return IDFSentences;
} 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![](img/a23d46603dc4957f765600048a489a66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ic839E6J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/638/1%2Akxm0bf_KleZ31r1GszSw8g.png) 

<figcaption>既然我们已经编写了代码来计算语句的 IDF 值；我们已经完成了前面的公式。《是狗》IDF 值最高。</figcaption>

</figure>

我们现在知道一个句子是多么独特或罕见。这没什么用，因为我们也想让句子包含丰富的信息。我们希望有某种方式将 TF 的受欢迎程度与 IDF 的独特性结合起来。这将引导我们进入下一部分…

### TF-IDF 重访

我们现在已经实现了 TF 和 IDF 功能。剩下唯一要做的就是将它们相乘。

```
function TFIDF(documents){
    // calculates TF*IDF
    const TFVals = termFrequency(documents);
    const IDFVals = inverseDocumentFrequency(documents);

    let TFidfDict = {};

    for (const [key, value] of Object.entries(TFVals)){
        if (key in IDFVals){
            TFidfDict[key] = TFVals[key] * IDFVals[key];
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

对象 TF 和 IDF 都来自相同的数据，所以 TF 不会包含 IDF 中没有的内容。因此，我们可以遍历一个对象并使用同一个键。我们将 TFVals 中的值乘以 IDFVals 中的值。

我们的下一步是计算 TF-IDF 对象中最重要的 3 个句子。用几个 if 语句迭代对象的*【key，value】*可以很好地工作。

```
function TFIDF(documents){
    // calculates TF*IDF
    const TFVals = termFrequency(documents);
    const IDFVals = inverseDocumentFrequency(documents);

    let TFidfDict = {};

    for (const [key, value] of Object.entries(TFVals)){
        if (key in IDFVals){
            TFidfDict[key] = TFVals[key] * IDFVals[key];
        }
    }

    let max = 0.0;
    let max2 = 0.0;
    let max3 = 0.0;

    let max_sentence = "";
    let max2Sent = "";
    let max3Sent = "";

    // finds the top 3 sentences in TFidfDict
    for (const [key, value] of Object.entries(TFidfDict)){
        if (TFidfDict[key] > max){
            max = TFidfDict[key];
            max_sentence = key;
        }
        else if (TFidfDict[key] > max2 && TFidfDict[key] < max){
            max2 = TFidfDict[key];
            max2Sent = key;
        }
        else if (TFidfDict[key] > max3 && TFidfDict[key] < max2 && TFidfDict[key] < max){
            max3 = TFidfDict[key];
            max3Sent = key;
        }
    }
    return ("<br>" + "•" + max_sentence + "<br><br>" + "•" + max2Sent + "<br><br>" + "•" + max3Sent);
} 
```

Enter fullscreen mode Exit fullscreen mode

你会在底部看到我们返回格式化的字符串。我们把它格式化，这样当我们把它插入网页时，它看起来很漂亮。每个
是一个[换行符，](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/br)是文本中的一个空格。黑点是项目符号。我们现在要在 Firefox 扩展中实现这个算法。🔥🦊

### 获取&更改 BBC 新闻文章中的文本

转到任意一篇 [BBC](https://www.bbc.co.uk/news) 新闻文章，右键点击并按下“inspect element”。你会在屏幕底部看到一个漂亮的方框。使用左上角的元素选择器工具，将鼠标悬停在文章上。我们可以看到整篇文章都包含在一个 CSS 类“story-body”中。

[![](img/d18b7b789a0a1768b1470847befb4c99.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g-g5Cov2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AgtYohYjQ76R6kjcU.png)

如果我们进一步深入，我们可以看到文章中的所有实际文本都包含在这个 CSS 类中的段落标记中。

[![](img/680511f0ed4a6351713ec9d2d4817932.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jep_gphb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A5nY1VQtLqXTiPMHt.png)

我们将使用 [JQuery](https://jquery.com/) 来选择文本。

```
// get all text from .story-body within p tags on a BBC news web article
let $article = $('.story-body').find('p').text(); 
```

Enter fullscreen mode Exit fullscreen mode

这一行选择 story-body 类中的所有 [< p >](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/p) 标签。现在我们想要得到文本，我们通过应用*方法来实现。正文()*。

我们想把我们的文字添加到文章的顶部。JQuery 有一个名为 prepend 的方法，它允许我们将数据添加到对象的顶部。

```
// get all text from .story-body within p tags on a BBC news web article
let $article = $('.story-body').find('p').text();
// insert text into body of document
let insert = $('.story-body').prepend(TFIDF($article)); 
```

Enter fullscreen mode Exit fullscreen mode

我们完事了。我们现在可以识别 BBC 新闻文章中最重要的句子，并显示在顶部。只是时间把它变成一个延伸。

### 火狐扩展基础知识

Firefox 扩展有两个主要部分。你写的 Javascript 和 *manifest.json* 文件告诉 Mozilla 你的扩展是做什么的。我们现在正在检查*清单。* 

```
{  "manifest_version":  2,  "name":  "TL;DR - Summary of BBC news articles",  "version":  "1.0",  "description":  "This extension creates a summary of BBC news articles using TF*IDF",  "content_scripts":  [  {  "matches":  ["*://www.bbc.co.uk/news/*"],  "js":  ["jquery.js",  "tldr.js"]  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

告诉 Firefox 你正在使用哪个版本的清单。 *Name* 告诉 Firefox 你的扩展名是什么。*版本*告诉火狐你的扩展是什么版本号。这三个是必须的。

*描述*告诉 Firefox 你的扩展是做什么的。

content_scripts 告诉 Firefox 当 URL 与您输入的内容匹配时加载什么脚本。为了运行您指定的脚本，当前 URL 必须至少与您指定的一个 URL 匹配。您可以在这里使用两个特殊字符:

1.  " ***** "匹配零个或多个字符。在这个例子中，我不知道用户将加载 HTTP 还是 HTTPS，所以我让它一步加载两者。我也不知道用户会看哪篇文章，所以我把它设置为在任何文章上激活。

2.  **？**"恰好匹配一个字符。

Mozilla 开发者网络对这些有一个很好的解释:

比如:“*na？我”会匹配“光明会”和“安鲁那基”，但不会匹配“萨格那利”。

因为我们将使用 jQuery，所以在脚本执行之前，我们也将 jQuery JS 文件导入到网站中。你可以从[这里](https://code.jquery.com/jquery-3.3.1.slim.js)抓取 jQuery 文件。复制粘贴到一个名为*“jquery . js”*的文件中。

在您的 Firefox URL 中输入“ *about:debugging* ”来加载该页面:

[![](img/8b6fd146f4a38b21f47b6cf35a6bb35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j8m-BqrR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AG8AooP4-C9x4vjHK.png)

从这里，点击*“加载临时附件……”*，然后点击扩展中的任何文件。一旦你这样做了，你应该看到这个:

[![](img/c3b5830b8ad5860c199a228dbfe3316c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--klbb_5po--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AL0rVhBpG2bOjkxt9.png)

Mozilla 有一篇关于火狐扩展基础的好文章。

现在加载任意一篇 BBC 新闻文章来玩吧！

### 结论

您现在已经看到了 TF-IDF 的强大功能以及它的实际应用。我有这个想法是因为我有电子邮件焦虑症。我对阅读电子邮件感到非常紧张，所以我想要一个快速的摘要来平静我的思绪。唉，这是我第一次写 Javascript。我开始做一些比较容易的东西，比如 BBC 新闻文章。

如果你愿意的话，下面是一些改进这些代码的方法:

*   动态选择你想要的摘要中的句子数量。您可以在整篇文章中找到平均 TF*IDF 值，以及可以包含在摘要中的任何超过 X 的值。这使得长文章和短文章被同等对待。
*   扩展这一工作在任何其他网站上你想。

**想成为更好的开发者？注册我的电子邮件列表。[点击此处了解更多信息。](https://pages.convertkit.com/2ffbe6834c/8444e0640e)你会收到 7 篇每日一篇的我的最佳内容。没有垃圾邮件。没有承诺。随时退订。**

* * *