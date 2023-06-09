# 用代码解决日语学习问题

> 原文：<https://dev.to/leoat12/solving-japanese-learning-problems-with-code-1k71>

在提高技能的同时，我也喜欢参与学习语言。我学过英语，我的母语是葡萄牙语，现在由于我对日本文化的热爱，我正在学习日语。昨天，我有了自动化一个相当无聊的任务的想法，这个任务阻碍了我使用 NodeJs 的学习。让我们从头开始。

## 背景

我从 2015 年开始学习日语，这是一个漫长的旅程，我还远未达到流利的程度，但我已经处于可以相对轻松地阅读漫画和借助字典阅读书籍的阶段。本周我开始了一本新书，并决定再给 [Anki](https://apps.ankiweb.net) 一个非常强大的抽认卡应用程序，在日本学习者中非常有名，但它可以用来学习几乎任何东西。我以前已经用了同样的方法:我打开字典阅读这本书，我把每个不认识的单词添加到一个. txt 文件中，然后添加到 Anki 中，然后开始记忆过程。但是，有一个问题，大概是这个问题让我之前不再使用 Anki。让我们深入探讨一下。

## 问题

Anki 有一个导入特性，你可以创建一个. txt 文件来声明抽认卡的两面，用分号隔开，就像这样:

```
傍ら;side, edge, beside, besides, nearby, while (doing)
飢える;to starve, to thirst, to be hungry
へたり込む;to sit down hard, to sink down to the floor
払い除ける;to ward off, to brush away, to fling off, to drive away 
```

Enter fullscreen mode Exit fullscreen mode

但是你必须以某种方式处理这个文件，起初我是手动处理的。我记下所有我不认识的单词，每天最多 50 个，以免一下子学太多，然后我去查字典，把意思复制到抽认卡的另一面。此外，在日语中有三种类型的字符:平假名、片假名和汉字。简单地说，汉字代表思想，例如，愛意味着爱，而平假名和片假名代表声音，用于描述日本汉字必须如何阅读。使用愛例如，它在平假名中的读法是あい，在我们的字母表中写为`ai`。更详细的解释，你可以参考[维基百科](https://en.wikipedia.org/wiki/Japanese_writing_system)，那里有一个关于它如何工作的很好的总结。因此，记住带有汉字的单词是如何读的也很重要，所以我不得不创建另一个文件，看起来像下面的文件，单词和它的读法用分号隔开:

```
傍ら;かたわら
飢える;うえる
へたり込む;へたりこむ
払い除ける;はらいのける 
```

Enter fullscreen mode Exit fullscreen mode

问题是，这种手工任务非常枯燥，非常耗时。我必须抄写每个单词，看字典，抄写意思和阅读，然后输入 Anki。这本字典是数字化的，所以这是一个 Crtl+C + Crtl+V 的问题，但仍然需要 30 分钟左右才能准备好 50 个单词。它也容易出错，因为我会混淆阅读和意义，放在错误的文件中，或者把单词的意思放在错误的行中。我必须做一些事情来改善这种体验，让阅读再次变得有趣，所以我想到了写一个剧本来做到这一点。

## 解

由于这是一个相对简单的脚本，我决定借此机会练习 NodeJS，我现在正在学习。然而，这并不像看起来那么简单，因为需要一个字典来支持应用程序。幸运的是，我在 DynamoDB 表上有一个字典，这是我为另一个项目创建的，使用 Lambda 和 API Gateway 来访问它。希望在不久的将来，我也可以谈论这个项目，但是现在假设脚本可以访问一个 API，该 API 根据作为参数给出的术语返回找到的单词，就像这样:`example.com?term=愛`。

这个主要问题解决后，只需要调用 API，解析响应并编写文件。整个脚本仅使用了三个库:

*   **axios** : http 客户端库调用 API。我过去有过非常好的体验，因为它似乎比我接触过的其他产品更简单。
*   **fs** :处理 nodejs 中文件 I/O 的标准库。
*   **进度**:通过一个进度条，让 it 在工作完成时反应更快。

首先我声明了一些变量来存储输入文件的内容，一个每个单词都在一行中的文件，我将它们分割并存储到一个数组中以备后用。将存储结果的变量也被声明:

```
let input = fs.readFileSync('input.txt', {encoding: 'utf8'});
let terms = input.split('\r\n');
let outputReading = "";
let outputMeaning = ""; 
```

Enter fullscreen mode Exit fullscreen mode

然后我创建了一个 axios 实例来使用，然后我使用这个函数来调用 API 并得到我想要的单词:

```
var instance = axios.create({
    baseURL: "https://api.example.com",
    headers: {'x-api-key': "xxxxxxxxxx"}
});

async function getWord(term){
    const response = await instance.get("/dictionary", {params: {term: term}});
    return response.data.body[0];
} 
```

Enter fullscreen mode Exit fullscreen mode

在函数中，我调用 API 并返回响应的主体。响应是一个包含搜索可能结果的数组。模式的简单描述如下:

```
{  "statusCode":  200,  "body":  [  {  "Id":  1,  "kanji":  [],  "kana":  [],  "sense":  [  {  "gloss":[]  }  ]  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

响应中有更多详细描述整个单词的元素，但对于我试图解决的问题来说，以下内容很重要:

*   kana:包含单词所有读数的数组。一个单词可以有多个读法，但是数组中的第一个是最流行的，通常也是我要找的那个。
*   意义:包含意义及其信息的数组:词性、方言、关联词、反义词等。一个单词可以有不同的意思，但是一个意思可以有很多单词是同义词。
*   gloss:同义词存储在数组中。

存储在上述数组中的所有对象都有一个存储我们感兴趣的信息的`text`字段。回到我们之前的例子愛概括地说，这就是响应的样子:

```
{  "statusCode":  200,  "body":  [{  "kanji":  [{  "common":  1,  "text":  "愛",  "tags":  []  }],  "kana":  [{  "appliesToKanji":  ["*"],  "text":  "あい",  "common":  1,  "tags":  []  }],  "Id":  1150410,  "sense":  [{  "gloss":  [{  "lang":  "eng",  "text":  "love"  },  {  "lang":  "eng",  "text":  "affection"  },  {  "lang":  "eng",  "text":  "care"  }]  },  {  "gloss":  [{  "lang":  "eng",  "text":  "attachment"  },  {  "lang":  "eng",  "text":  "craving"  },  {  "lang":  "eng",  "text":  "desire"  }]  }]  }]  } 
```

Enter fullscreen mode Exit fullscreen mode

得到响应后，为了处理它并以我想要的格式得到结果，我创建了两个函数来分别处理含义和读数。下面我们以`handleMeanings`函数为例:

```
function handleMeanings(term, word){
    let meaningsArray = []

    for(sindex in word.sense){
        let glosses = word.sense[sindex].gloss;
        for(gindex in glosses){
            meaningsArray.push(glosses[gindex].text);
        }
    }

    let joinMeanings = meaningsArray.join(", ");

    let result = term + ";" + joinMeanings + "\r\n";
    return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于每个`sense`,我遍历它的注释列表并推送到一个数组，然后我加入所有的东西，非常简单，这正是我想要的。

## 结论

对于那些看到标题和“可怕”的图像，并认为这是更复杂的东西的人，我很抱歉。这非常简单，甚至反高潮，但它真的帮助我跟上了我的学习。现在的问题是做好所有的复习，我会尽力的！:D
如果你认为某些东西可以编码得更好，请告诉我。NodeJS 对我来说还是新的！