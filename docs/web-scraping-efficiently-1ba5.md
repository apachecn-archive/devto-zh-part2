# 网页抓取，高效！

> 原文：<https://dev.to/navonf/web-scraping-efficiently-1ba5>

Web 抓取是访问应用程序可能需要的数据的一种有用(而且非常酷)的方式。有时，您可能需要一些 API 或数据库中没有提供的非常具体的东西。今天，我们将对此进行调整，通过使用 python 中的一些异步库来使它更快，从而使它变得更酷！

[![img](img/5b06fe257f43bfbf48f3d37058d69c5d.png)T2】](https://i.giphy.com/media/13WpPwlMpUJiOA/giphy.gif)

我们将使用以下内容:

*   Chrome 开发工具*检查 html 元素*
*   Python 3 *just cuz*
*   用于刮削的美丽组 4
**   异步请求的请求*

 **为引起人的注意时所发的声音...您可能需要一些其他的库。因为我使用的是 python 3，所以我使用 pip 3 来安装我需要的任何东西，比如 lxml。

```
pip3 install BeautifulSoup4 grequests lxml 
```

Enter fullscreen mode Exit fullscreen mode

所以让我们先来看几件事情..

### 动机

所以你在浏览 dev.to，你最喜欢的网站，你在思考；

"你知道什么是最酷的，就是找出一篇文章的标题信息."

“但我不只是读一篇，我想要多篇文章，因为我喜欢一次读很多篇文章。”

所以标签、文章名称、用户和日期。我认识这个人，所以他同意我这样宣传他的文章；)

[![davidisrawi](img/8747ff47997e395903d7435b35f12aa4.png)](/davidisrawi) [## 用 Python 和 NLTK 构建一个快速摘要器

### 大卫伊斯拉维 8 月 17 日 174 分钟阅读

#python #nlp #dataanalytics #learning](/davidisrawi/build-a-quick-summarizer-with-python-and-nltk)

然后你会想，

“好的！我想我已经让我的生活够艰难了”

### 问题

要抓取某个内容，你*基本上*向一个 url 发出一个请求，该请求包含该页面的所有 html，有了它，你可以使用一个像 BeautifulSoup 4 这样的解析库，解析并找到你想要的元素(比如一个 h1 标签)，然后提取你需要的数据(h1 标签中的一个标题)。

但是，我们将更进一步，让它变得更酷。我们将使用异步请求来更快地做到这一点。

异步过程的一个很好的类比是，想象你上班迟到了，所以你把华夫饼放入烤面包机(开始请求 1)，在你按下开始后，你继续刷牙(开始请求 2)，同时等待你的华夫饼完成。嘣，异步华夫饼。

[![img](img/7c3666d1513bf43b6e900a05ea37297d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pRailTua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/7770092fe0af9ce56e8f0dd9e0142f1c/tenor.gif)

<center>Let's get started!</center>

* * *

### 进口

```
from bs4 import BeautifulSoup

import grequests
import requests
import time 
```

Enter fullscreen mode Exit fullscreen mode

这些是您需要导入的库。 *BeautifulSoup* 用于解析， *grequests* 是允许我们进行异步请求的库， *requests* 只是为了举例——我们将演示一个较慢的版本(不需要)，而 *time* 用于测量(不需要)。顺便说一下，如果你要输入所有这些导入，确保 grequests 在 requests 之前，就像上面一样，否则你会遇到一个非常严重的错误，可能要花 45 分钟才能弄明白。

### 刮削

```
links = ['https://dev.to/davidisrawi/build-a-quick-summarizer-with-python-and-nltk',
    'https://dev.to/kbk0125/web-servers-explained-by-running-a-microbrewery-48ie',
    'https://dev.to/dan_abramov/react-beginner-question-thread--1i5e',
    'https://dev.to/ben/im-ben-and-i-am-a-rails-developer-1j67',
    'https://dev.to/devteam/changelog-mentor-matchmaking-3bl0'
] 
```

Enter fullscreen mode Exit fullscreen mode

所以我选择了 5 篇非常酷的文章。这些是我们将通过向它们发出请求来提取 html 的 URL。让我们构建一个没有异步请求的干巴巴的刮刀。

*我们不想对 dev.to 的服务器提出太多要求，还是尊重点吧:)*

因此，我们需要遍历链表并发出一个简单的请求。

```
for link in links:
    req = requests.get(link) 
```

Enter fullscreen mode Exit fullscreen mode

在我们发出请求后，我们想要创建一个 BeautifulSoup 对象。这将允许我们调用真正有用的函数，如。find()轻松提取我们想要的东西。

*呼唤。请求中的文本将转储该页面的所有 html，试试吧！*

```
soup = BeautifulSoup(req.text, 'lxml') 
```

Enter fullscreen mode Exit fullscreen mode

现在有了汤对象我们就可以调用。find()，检索当前页面的标题。在我们的。find()你可以看到我们用 medium 类指定了一个 h1 标签，并调用。什么都带上绳子。这一切意味着什么？

```
# article print(soup.find('h1', class_='medium').string.lstrip().rstrip()) 
```

Enter fullscreen mode Exit fullscreen mode

要精确定位一个特定的元素(在本例中是一个标题)，只需检查页面，使用允许您通过单击来选择 html 元素的选项，选择该元素，然后它会突出显示该元素在 html 模板中的位置。

这将允许我们获取该元素的属性，为我们的。查找()。的。string 让我们获得标签之间的所有内容，就像这样:

```
<h1>Calling.stringGrabsThis</h1> 
```

Enter fullscreen mode Exit fullscreen mode

lstrip()和 rstrip()删除了开头和结尾的空格。

[![img](img/100750537a9f129d5651bfa08bd77d65.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--01ft1lG6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://s15.postimg.cc/3uh5bnz2j/giphy.gif)

对于名称、日期和标签，过程是相同的。

```
for link in links:
    req = requests.get(link)
    soup = BeautifulSoup(req.text, 'lxml')

    # article
    print(soup.find('h1', class_='medium').string.lstrip().rstrip())

    # name
    print(soup.find('span', itemprop="name").string)

    # date
    print(soup.find('span', class_="published-at").string)

    # tags
    tags = list(map(lambda x: x.string, soup.find_all('a', class_='tag')))
    print(tags, "\n") 
```

Enter fullscreen mode Exit fullscreen mode

虽然，我们在检索标签方面做了一些奇特的事情。因为有多个标记，所以我们调用。find_all()，因为每个文章标签都在它自己的 span 元素中。使用映射，然后我们使用 lambda 函数从标签中剥离字符串，就像上面一样。然后，我们把它们放在一个列表里。

这是我们得到的:

```
Build a quick Summarizer with Python and NLTK
David Israwi
Aug 17 '17
['#python', '#nlp', '#dataanalytics', '#learning'] 

Web Servers Explained by Running a Microbrewery
Kevin Kononenko
May 17
['#tutorial', '#beginners', '#webdev'] 

React Beginner Question Thread ⚛
Dan Abramov
Dec 24 '17
['#react', '#javascript', '#beginners', '#webdev'] 

I’m Ben and I am a Rails developer
Ben Halpern
Apr 17
['#ruby', '#rails', '#productivity', '#career'] 

Changelog: Mentor Matchmaking!
Jess Lee
Jul  6
['#changelog', '#meta', '#mentorship'] 

Took 1.877747106552124 seconds 
```

Enter fullscreen mode Exit fullscreen mode

* * *

既然我们已经理解了传统的抓取方式是如何工作的，我们可以对其进行编辑以适应异步请求。我们将创建一个包含请求的响应对象，而不是遍历链表并在循环中发出请求。让我们看看这是如何工作的。

使用 grequests 库，我们可以创建一个未发送请求的列表。

```
reqs = [grequests.get(link) for link in links] 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以使用 map 发出这些请求，并将它们存储在一个响应对象中。我们使用 map，因为它允许我们执行下一个请求，而不必等待当前请求完成。

```
resp = grequests.map(reqs) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经创建了包含请求的响应对象，而不是循环遍历链接，所以我们将迭代遍历这个对象。

```
for r in resp:
    # we've already made our request, now just parse! 
```

Enter fullscreen mode Exit fullscreen mode

所以我们最终的异步实现应该是这样的..所有的刮擦都应该保持不变！

```
reqs = [grequests.get(link) for link in links]
resp = grequests.map(reqs)

for r in resp:
    soup = BeautifulSoup(r.text, 'lxml')

    # article
    print(soup.find('h1', class_='medium').string.lstrip().rstrip())

    # name
    print(soup.find('span', itemprop="name").string)

    # date
    print(soup.find('span', class_="published-at").string)

    # tags
    tags = list(map(lambda x: x.string, soup.find_all('a', class_='tag')))
    print(tags, "\n") 
```

Enter fullscreen mode Exit fullscreen mode

最终输出:

```
Build a quick Summarizer with Python and NLTK
David Israwi
Aug 17 '17
['#python', '#nlp', '#dataanalytics', '#learning'] 

Web Servers Explained by Running a Microbrewery
Kevin Kononenko
May 17
['#tutorial', '#beginners', '#webdev'] 

React Beginner Question Thread ⚛
Dan Abramov
Dec 24 '17
['#react', '#javascript', '#beginners', '#webdev'] 

I’m Ben and I am a Rails developer
Ben Halpern
Apr 17
['#ruby', '#rails', '#productivity', '#career'] 

Changelog: Mentor Matchmaking!
Jess Lee
Jul  6
['#changelog', '#meta', '#mentorship'] 

Took 0.8569440841674805 seconds 
```

Enter fullscreen mode Exit fullscreen mode

这是一个巨大的时间节省。第一次平均约为 1.5-1.8 秒，第二次平均约为 0.8-1.0 秒。我们可以有把握地说，我们为每个请求节省了大约 0.1 到 0.2 秒。乍一看，这似乎不是很大，但这只是 5 个请求！当进行许多异步调用时，您将节省大量时间。请自行测试，每个应用程序可能会有所不同。您可以发出大量的请求，一次 20 个或一次 5 个，尝试一下找出最适合您的应用程序需求的请求！

如果你想节省更多的时间，看看 SoupStrainer 的 BeautifulSoup，一个你可以应用到你的。查找()以缩小搜索范围。它可能不会像上面那样产生收益，但是它是帮助保持代码高效的一个很好的工具！*也许这是改天的文章*

我希望你喜欢这个刮削小指南！如果您有任何问题或疑问，请随时发表评论或联系我。希望这有帮助！

*平安出来***