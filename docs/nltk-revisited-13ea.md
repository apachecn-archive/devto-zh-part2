# NLTK 重访

> 原文：<https://dev.to/lukaszkuczynski/nltk-revisited-13ea>

## NLTK 重访:为什么

当您开始处理一些文本分析项目时，您迟早会遇到以下问题:在哪里找到示例文本，如何获得资源，我应该从哪里开始。当我[第一次接触 NLP(波兰语帖子)](http://lukcreates.pl/dajsiepoznac2017/porownanie-tekstu-scikit-learn-i-nltk-nlp/)时，我并没有意识到 NLTK 背后的力量——NLP 的 Python 首选库。然而，几年后，我发现我可以更早地使用它。为什么？NLTK 提供了对各种文本来源的简单访问。我将向你们展示在研究一本[官方书籍](http://nltk.org/book/)的前三章时，我特别喜欢什么，以及什么引起了我的注意。

但是主业是什么？我想(最后)建立一些*建议工具*，将允许提供一些虚拟助理，以帮助决策过程。

## 捆绑资源可用

### 棕色及其分类

NLTK 有各种各样的语料库，那么大的文本包。您可以使用它们，如下例所示。你需要做的就是下载合适的语料库并开始探索。现在让我们看看。

```
import nltk
nltk.download('brown')
files = nltk.corpus.brown.fileids()
print(f"'Brown' corpus contain {len(files)} files")

[nltk_data] Downloading package brown to /root/nltk_data...
[nltk_data] Unzipping corpora/brown.zip.
'Brown' corpus contain 500 files 
```

在这个语料库中，你会发现不同类别的文本。所以它非常适合机器学习的分类领域。下面是这些文章的分类和一些例子。

```
print("'brown' contains following categories %s" % nltk.corpus.brown.categories())
brown_adventure = nltk.corpus.brown.sents(categories='adventure')[0:5]
brown_government = nltk.corpus.brown.sents(categories='government')[0:5]
print("Following we have some sentences from 'adventure' category:")
for sent in brown_adventure:
  print(" > "+ " ".join(sent))
print("And here we have some sentences from 'government' category:")
for sent in brown_government:
  print(" > "+ " ".join(sent)) 

'brown' contains following categories ['adventure', 'belles_lettres', 'editorial', 'fiction', 'government', 'hobbies', 'humor', 'learned', 'lore', 'mystery', 'news', 'religion', 'reviews', 'romance', 'science_fiction']
Following we have some sentences from 'adventure' category:
 > Dan Morgan told himself he would forget Ann Turner .
 > He was well rid of her .
 > He certainly didn't want a wife who was fickle as Ann .
 > If he had married her , he'd have been asking for trouble .
 > But all of this was rationalization .
And here we have some sentences from 'government' category:
 > The Office of Business Economics ( OBE ) of the U.S. Department of Commerce provides basic measures of the national economy and current analysis of short-run changes in the economic situation and business outlook .
 > It develops and analyzes the national income , balance of international payments , and many other business indicators .
 > Such measures are essential to its job of presenting business and Government with the facts required to meet the objective of expanding business and improving the operation of the economy .
 > Contact
 > For further information contact Director , Office of Business Economics , U.S. Department of Commerce , Washington 25 , D.C. . 
```

在*布朗*语料库中有一个非常重要的 NLP 组成部分，即词性标注(POS)。它是如何组织的？让我们看看几分钟前印刷的句子中的一个例子。

```
adv_words = nltk.corpus.brown.words(categories='adventure')
print(adv_words[:10])
adv_words = nltk.corpus.brown.tagged_words(categories='adventure')
print(adv_words[:10])

['Dan', 'Morgan', 'told', 'himself', 'he', 'would', 'forget', 'Ann', 'Turner', '.']
[('Dan', 'NP'), ('Morgan', 'NP'), ('told', 'VBD'), ('himself', 'PPL'), ('he', 'PPS'), ('would', 'MD'), ('forget', 'VB'), ('Ann', 'NP'), ('Turner', 'NP'), ('.', '.')] 
```

是啊！它被标记并准备好进行分析。这些符号是什么意思？它们是词类的象征，这里很好地描述了。F.e. `NP`代表*专有名词*，`VBD`是*动词，过去式*。

### 感悟

使用 NLP 的领域之一是情感分析，在数字营销中扮演着重要的角色。想象一下，处理大量的意见并立即识别产品是否被社区认可是多么美好的事情。用它来观察趋势也是可能的。那么 NLKT 捆绑的处理情绪的工具是什么呢？这是`opinion_lexicon`

```
from nltk.corpus import opinion_lexicon
nltk.download('opinion_lexicon')
negatives = opinion_lexicon.negative()[:10]
positives = opinion_lexicon.positive()[:10]
print("If you find some negative words, here you are: %s" % negatives)
print("But let us try to see the positive side of life! Described with these words: %s" % positives)

[nltk_data] Downloading package opinion_lexicon to /root/nltk_data...
[nltk_data] Unzipping corpora/opinion_lexicon.zip.
If you find some negative words, here you are: ['2-faced', '2-faces', 'abnormal', 'abolish', 'abominable', 'abominably', 'abominate', 'abomination', 'abort', 'aborted']
But let us try to see the positive side of life! Described with these words: ['a+', 'abound', 'abounds', 'abundance', 'abundant', 'accessable', 'accessible', 'acclaim', 'acclaimed', 'acclamation'] 
```

### 还有更多…

那里有很多其他的语料库。这个帖子/笔记本的任务不是重复他/她在[官方文件中读到的东西，就像这里的](http://www.nltk.org/howto/corpus.html#tagged-corpora)。通过 NLTK，在下载一些资料后，您可以访问以下资料:

*   多语言语料库(如《世界人权宣言》，有 300 多种语言)
*   词汇资源( *WordNet*
*   发音词典( *CMU 发音词典*)

很多东西可以浏览。但是，只要看一眼其中的一些，有一种“我在哪里见过它”的感觉，这是值得的当面对一些 NLP 任务时。

## 取任何东西

如果附加的资源对你来说不够，就开始使用不同的资源。

### 请求

Python 有各种库，所以只要有可用的 URL，就可以使用应用程序中可用的网络资源。

```
import requests
url = 'https://databricks.com/blog/2018/09/26/whats-new-for-apache-spark-on-kubernetes-in-the-upcoming-apache-spark-2-4-release.html'
resp = requests.get(url)
blog_text = resp.text
blog_text[:200]

'\r\n<!DOCTYPE html>\r\n<html lang="en-US" prefix="og: http://ogp.me/ns# fb: http://ogp.me/ns/fb# video: http://ogp.me/ns/video# ya: http://webmaster.yandex.ru/vocabularies/">\r\n<head>\r\n <meta charset="UTF' 
```

### RSS

准备消费 RSS 了吗？使用 Python，没有比这更简单的了。您可以创建一个将 RSS 提要作为输入的`nltk.Text`实例。这是如何做到这一点的片段

```
!pip install feedparser
import feedparser
url = 'http://jvm-bloggers.com/pl/rss.xml'
d = feedparser.parse(url)
title = d['feed']['title']
entries = d['entries']
print("Look ma! I've just parsed RSS from a very nice Polish blogging platform. It has a title %s" % title)
print("And there we go with 5 exemplary entries:")
for entry in entries[:5]:
  print(' > ' + entry.title)

Collecting feedparser
Successfully built feedparser
Installing collected packages: feedparser
Successfully installed feedparser-5.2.1
Look ma! I've just parsed RSS from a very nice Polish blogging platform. It has a title JVMBloggers
And there we go with 5 exemplary entries:
 > Odpowiedź: 42
 > Thanks for explaining the behaviour of dynamic (partition overwrite) mode.
 > Non-blocking and async Micronaut - quick start (part 3)
 > Strefa VIP
 > Mikroserwisy – czy to dla mnie? 
```

### 清洗

当你的 HTML 文档被获取时，你可能有一个充满 HTML 混乱的文档，在你的文本中有`<body>`没有任何附加价值。因此，可以做一些清理工作，也有工具可以做到这一点，但它们不是 nltk 包的一部分。让我们以美丽的泡沫为例。

```
from bs4 import BeautifulSoup
soup = BeautifulSoup(blog_text)
content = soup.find("div", {'class':"blog-content"})
text_without_markup = content.get_text()[:100]
text_without_markup

'\n\n\nWhat’s New for Apache Spark on Kubernetes in the Upcoming Apache Spark 2.4 Release\n\n\nSeptember 26' 
```

## 正常化——语言不易

你的语言不容易。如果你像我一样是波兰人，那就太对了。但是，即使是英语和其他欧洲语言也增加了 NLP 的复杂性。为什么？单词有不同的形式，当机器分析文本时，我们必须遵循语法规则。以英语`going`单词为例，你指的是`go`动词，但也有它的引理`-ing`，在分析时必须识别和跳过。NLTK 中有内置支持的 3 个进程是什么？阅读以下内容。

### 标记化

文本由句子组成，而句子又包含单词。很多时候，我们希望用向量来表示单词，因为我们会用到一些代数。最简单的标记化方法可以如下实现，但是有一些限制。您可以使用`nltk.tokenize` [包](https://www.nltk.org/api/nltk.tokenize.html)中提供的各种*记号化器*。

```
# write tokenizer yourself? import re
text = "Two smart coders are coding very quickly. Why? The end of the sprint is coming! The code has to be finished!"
tokens_manual = re.split(r"[\s+]", text)
print("Tokens taken manually %s " % tokens_manual)

# or maybe choose the one from the abundance in `nltk` import nltk
from nltk.data import load
from nltk.tokenize.casual import (TweetTokenizer, casual_tokenize)
from nltk.tokenize.mwe import MWETokenizer
from nltk.tokenize.punkt import PunktSentenceTokenizer
from nltk.tokenize.regexp import (RegexpTokenizer, WhitespaceTokenizer,
                                    BlanklineTokenizer, WordPunctTokenizer,
                                    wordpunct_tokenize, regexp_tokenize,
                                    blankline_tokenize)
from nltk.tokenize.repp import ReppTokenizer
from nltk.tokenize.sexpr import SExprTokenizer, sexpr_tokenize
from nltk.tokenize.simple import (SpaceTokenizer, TabTokenizer, LineTokenizer,
                                    line_tokenize)
from nltk.tokenize.texttiling import TextTilingTokenizer
from nltk.tokenize.toktok import ToktokTokenizer
from nltk.tokenize.treebank import TreebankWordTokenizer
from nltk.tokenize.util import string_span_tokenize, regexp_span_tokenize
from nltk.tokenize.stanford_segmenter import StanfordSegmenter
from nltk.tokenize import word_tokenize

nltk.download('punkt')
tokens = word_tokenize(text)
print(tokens)

Tokens taken manually ['Two', 'smart', 'coders', 'are', 'coding', 'very', 'quickly.', 'Why?', 'The', 'end', 'of', 'the', 'sprint', 'is', 'coming!', 'The', 'code', 'has', 'to', 'be', 'finished!'] 
[nltk_data] Downloading package punkt to /root/nltk_data...
[nltk_data] Unzipping tokenizers/punkt.zip.
['Two', 'smart', 'coders', 'are', 'coding', 'very', 'quickly', '.', 'Why', '?', 'The', 'end', 'of', 'the', 'sprint', 'is', 'coming', '!', 'The', 'code', 'has', 'to', 'be', 'finished', '!'] 
```

### 词干

所以可以完成的任务之一是*词干*，所以去掉单词 ending。让我们看看流行的*词干分析器*对我们之前已经标记过的文本做了什么。

```
porter = nltk.PorterStemmer()
tokens_stemmed = [porter.stem(token) for token in tokens]
print(tokens_stemmed)

['two', 'smart', 'coder', 'are', 'code', 'veri', 'quickli', '.', 'whi', '?', 'the', 'end', 'of', 'the', 'sprint', 'is', 'come', '!', 'the', 'code', 'ha', 'to', 'be', 'finish', '!'] 
```

### 词汇化

如果词干化还不够，还需要完成*词汇化*，这样你的单词就可以根据真正的字典进行分类。下面是一个为我们的文本样本运行它的例子。

```
nltk.download('wordnet')
wnl = nltk.WordNetLemmatizer()
lemmas = [wnl.lemmatize(token) for token in tokens]
print(lemmas)

[nltk_data] Downloading package wordnet to /root/nltk_data...
[nltk_data] Unzipping corpora/wordnet.zip.
['Two', 'smart', 'coder', 'are', 'coding', 'very', 'quickly', '.', 'Why', '?', 'The', 'end', 'of', 'the', 'sprint', 'is', 'coming', '!', 'The', 'code', 'ha', 'to', 'be', 'finished', '!'] 
```

## 总结

那么我们去了哪里？我刚刚分析了 NLKT 的在线书籍，第 2 章和第 3 章。我尝试了这个自然语言工具包中大量可用工具中的几个。现在有时间去探索那边的其他章节了。敬请关注。我必须建立我的*建议工具*。

所有这些都是作为 Jupyter 笔记本创建的。