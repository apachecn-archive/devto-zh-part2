# 用 Python 和 Selenium 抓取我的 Twitter 社交图

> 原文：<https://dev.to/swyx/scraping-my-twitter-social-graph-with-python-and-selenium--hn8>

我在 Twitter 上呆了 9 年，但刚刚意识到这一点:Twitter 在像 Messenger 或 WhatsApp 那样使用时处于最佳状态，而不是像脸书那样使用时。

> ![shawn swyx wang 🇸🇬 profile image](../Images/9da0698fedd18ceea92c0514249f4915.png)shawn swyx 王[@ swyx](https://dev.to/swyx)![twitter logo](../Images/ad0c7b03deabfe1a161345efb2d537eb.png)Twitter 既是 Facebook 又是 Messenger。当你浏览主要信息时，推特是最糟糕的，被喜欢的东西污染了，引用推特&关注你甚至不关注的人。当你和你认识或将会认识 IRL 的有共同兴趣的人联系在一起时，Twitter 处于最佳状态。2018 年 2 月 16 日下午 13:53[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=964497956987654146)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=964497956987654146)0[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=964497956987654146)2

换句话说，当我用 Twitter 与有共同兴趣的真实的人联系时，我得到了最大的好处，而不是为了跟上新闻或公司或名人，也绝对不是为了与随机的互联网陌生人争论。

# 寻找开发者推特

在 Twitter 上呆了 9 年(大部分时间处于休眠状态)，我已经积累了大约 4000 名 Twitter 粉丝。他们反映了我的背景:一些是金融客户，一些音乐家，一些产品/制造商，一些玩笑客户，一些开发者。但是根据我上面的认识，我发现自己想减少噪音，把我的 Twitter 使用变成有助于改善我的新职业的东西。

不管是好是坏，很大一部分开发者社区都在 Twitter 上。大约在我从金融转向软件工程的中途，我才开始参与到“开发推特”中，但是很快就迷失在这种喧嚣中。

# Dev Twitter 的状态

Dev Twitter 非常棒:你可以[与高级开发人员](https://twitter.com/sophiebits/status/960539240655241216)接触，[遇到麻烦时获得帮助](https://twitter.com/kyleshevlin/status/963111519851495424)，[宣传你的工作](https://twitter.com/ryanflorence/status/964682338981486593)，甚至[获得工作](https://twitter.com/sehurlburt/status/923830594240372736)。

然而，Twitter 也可能完全像人们所说的那样是一个垃圾箱:一个令人困惑的背景的连续不断的不和谐声音——轻的批评——重的评论，涵盖从体育到政治到名人到政治到科技到政治到金融到政治到机器人的一切。即使在对政治的廉价抨击之外，你也会在 Dev Twitter 上看到没有人真正需要的[偶尔](https://twitter.com/acemarke/status/9631241516649553920) [崩溃](https://twitter.com/littlecalculist/status/963946952403505152)。(Javascript 甚至有 [Horse_JS](https://twitter.com/horse_js) ，一个专门的但受人喜爱的 troll 帐户，可以调用东西！)甚至促使 [SecondCareerDev 的](https://secondcareerdevs.com/) [Kyle Shevlin](https://twitter.com/kyleshevlin) 制定 [Twitter 交战规则](https://twitter.com/kyleshevlin/status/964636669390409728)(我强烈推荐)。

现在澄清一下:我支持政治参与。我也相信人们应该有各种各样的兴趣，应该有公开表达不同意见的自由。这个帖子不是关于任何事情。

像许多社交平台一样，Twitter 有一个“相信我，我知道什么最适合你”的推荐算法。当你向下滚动主订阅源时，你会看到被你关注的人所关注的人的推文。如果你进入搜索标签(在移动应用上)并点击连接，你会看到一个由“因为你关注”、“你可能认识的人”和“基于你的活动”算法(后者是最差的，因为它根据单个数据点进行推荐)推荐的人的列表。如果你稍微使用过 Twitter，你会认出这些算法正在进行的分组:这里是“技术领域的女性”组，这里是“大规模流行内容创作者”组。虽然从技术上来说是正确的，但很多选择最终只是感觉*错了*。我关注了[的 ReactJS twitter 账户](https://twitter.com/reactjs)，它建议我关注 [Angular](https://twitter.com/angular) 和 [EmberJS](https://twitter.com/emberjs) 账户。它们是很好的框架，但只是我现在不想跟进的客户。我不是美式足球的粉丝，但我敢打赌，这个相同的算法会建议爱国者队也向海鹰队球迷解释，这似乎是它的思维方式。

总之。

Twitter 用户通过转发其他人来补充这种自动推荐，并在特别的帖子中呼吁他们。这甚至有了自己的特殊标签，被称为 [#FollowFriday](https://www.lifewire.com/a-guide-to-follow-friday-2655376) 。因为偏见的存在，偶尔会有一些特殊的帖子[像这些](https://twitter.com/sehurlburt/status/936530247092133890?lang=en)来自杰出的社区成员，帮助那些代表性不足的群体。但是它是非常临时和手动的。

因此，作为一名开发人员，自然会出现这样一个问题:如果我把推荐算法掌握在自己手中会怎么样？

# 基本想法

开发人员熟悉这样一个概念，即[一切都是图](http://tcl.sfs.uni-tuebingen.de/~cornell/prolog/Graphs001.html)。Twitter 是一个人工探索的用户社交图，具有不同的(甚至是概率性的)信号质量和不清楚的、不同的优化功能。最高的信号是关注，这更持久，而喜欢、转发和回复也是信号，但更多的是一次性的。如果你追随一群你认为是高质量追随者的人，那么他们的追随者也有比随机更好的机会让你感兴趣。“follow-squared”没有真正的术语，所以我称之为“fofollows”。

所有这些当然有比我有资格谈论的更多的学术基础，但基本上你会想看看[网络中心性算法](https://en.wikipedia.org/wiki/Centrality)来看看学术界如何正式定义网络中心性的各种措施。

老实说，我不喜欢用“fofollows 的数量”来定义“好 follow”的想法。因为人们(包括我自己)有从众心理，这过度偏向名人文化，不利于那些也发布优质内容但出于任何原因尚未获得认可的人。例如，这种算法会偏向于某个名人，他刚刚在 instagram 上设置了自己的 twitter 账户来交叉发布，可能会获得大量关注、喜欢和转发，*即使这个人甚至不使用 twitter* 。我肯定会喜欢这样的人，他实际上给了人们深思熟虑的答复，但却没有多少追随者。我有一些关于如何做到这一点的想法，但只有在未来的帖子中才能解决它们。(我只是想提前声明我知道这是一个非常有缺陷的算法，并邀请建设性的建议。)

# 技术挑战

虽然我无法独自在这篇文章中解决社会问题，但我们可以利用现有的信息做一些有趣的事情:

1.  自动化:首先，我们必须从 Twitter 上收集数据。如果你正在编码，这将是这篇文章的大部分价值。
2.  分析:第二，我们必须将数据处理成我们想要的表面度量，也就是特征工程
3.  显示:最后，我们必须以一种容易理解的方式显示结果，这样我(和其他感兴趣的人)就可以迭代它，然后最终采取行动

这三样东西是非常不同的技能组合，在一个真正的公司里，不同的人会有一堆不同的工作。但我只是利用自己的时间来改善自己的个人状况。因此，尽管我很想产生一个权威的结果，但坦白地说，我对仅仅 10%的更好的体验感到满意(这甚至无法衡量)。

# 自动化-抓取推特

首先:我不是法律专家，所以你要小心行事。但是如果你写了一个的话，我们只能说 Twitter 有比你更大的机器人要处理。

好的。虽然我是一个专业的 JS 人员，并且在 NodeJS 中有[种方法来做抓取，但是 Python 抓取和数字处理生态系统已经存在很久很久了，所以这就是我要做的。](https://codeburst.io/a-guide-to-automating-scraping-the-web-with-javascript-chrome-puppeteer-node-js-b18efb9e9921)

要继续学习，请确保您有 [Jupyter 笔记本和 Python](http://jupyter.org/install) 的 Anaconda 发行版。如果你对 Python/Jupyter Notebook 完全陌生，你需要找到另一个教程来指导你，我们在这里不做介绍性的东西。下面的代码片段直接对应于 Jupyter 笔记本单元格。

## selenium 和 python 入门

现在导入我们将要需要的所有东西(pip 安装你缺少的任何东西):

```
%matplotlib inline
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import Select
from selenium.webdriver.support.ui import WebDriverWait
from selenium.common.exceptions import TimeoutException
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import NoSuchElementException
from selenium.common.exceptions import NoAlertPresentException
import sys

import unittest, time, re
from bs4 import BeautifulSoup as bs
from dateutil import parser
import pandas as pd
import itertools
import matplotlib.pyplot as plt 
```

现在你可以看到我们将使用 [Selenium](http://www.seleniumhq.org/) 来实现自动化。我们将使用它来自动化 Firefox，这样它可以在后台继续运行，同时我们继续使用我们的普通浏览器(我知道[超过 60%的人使用 Chrome](https://netmarketshare.com/browser-market-share.aspx) )。

```
driver = webdriver.Firefox()
driver.base_url = "https://twitter.com/swyx/following"
driver.get(driver.base_url) 
```

把我的用户名换成你的。如果您运行这段代码，它会打开 Firefox 到 twitter 登录页面。如果您使用自己的凭据登录，它会转到您的后续页面。抓取这个页面的问题是它是一个“无限滚动”的页面，所以仅仅抓取第一个视图中加载的内容是不够的。你必须向下滚动，等待它加载，然后再向下滚动，一次又一次，直到你加载完所有的关注。你可以试着从官方 Twitter API 获得这个，但是他们每 15 分钟只给你 15 个请求。所以我们刮。

登录后，您可以使用 Firefox devtools 检查器查看您感兴趣的 HTML 标签和属性。如果你是 HTML/Devtools 的新手，那也没关系，但是我没有足够的空间在这里教你。查看[自由代码营](https://www.freecodecamp.org/)、[代码学院](https://www.codecademy.com/learn/learn-html)或 [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML) 。

## 一个基本的无限滚动策略

自动化无限滚动最简单的方法是这样做:

```
for i in range(1,230):
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(2)
    print(i) 
```

我有 4000 个跟随者，所以我只是做了一些测试，然后计算我需要多少个循环来完成所有跟随者。由于其他人的关注人数会少于或多于 4000，我们将不得不使这成为一个动态策略，我将在下面介绍这一点。

我使用`time.sleep(2)`来允许页面加载发生。基于我的高速连接，这可能比我需要的时间要长，但是我选择牺牲更长的自动执行时间来降低不加载我需要的所有数据的风险。我也`print`我的进展只是作为一种方式来表明我在我的过程中走了多远，因为有时很难说我离完成还有多远。在这种情况下，这只需要大约 8 分钟运行，但我们将运行未来的东西更长的时间，我想解释一下基本的直觉。

## 保存数据

```
html_source = driver.page_source
sourcedata= html_source.encode('utf-8')
soup=bs(sourcedata)
arr = [x.div['data-screen-name'] for x in soup.body.findAll('div', attrs={'data-item-type':'user'})]
bios = [x.p.text for x in soup.body.findAll('div', attrs={'data-item-type':'user'})]
fullnames = [x.text.strip() for x in soup.body.findAll('a', 'fullname')][1:] # avoid your own name d = {'usernames': arr, 'bios': bios, 'fullnames': fullnames}
df = pd.DataFrame(data=d)
df.to_csv('data/BASICDATA.csv') 
```

这给了你一个数据框架`df`,里面有你关注的每个人的用户名、全名和个人简介。呜哇！你完了！对吗？？

没有。你才刚刚开始。

我们现在必须将你刚才为一个用户(你)所做的事情扩大到你的所有用户。

一些快速自动化数学——比如我们刚刚做的所有事情都花了 10 分钟。10 分钟 x 4000 用户= 40000 分钟= 666 小时= 28 天！！！那不是不可能，但是太高了，不合理。我们如何在合理的时间内做到这一点？

# 并行化

这个刮擦过程的伟大之处在于它们可以同时发生。如果我们有 4000 台机器，我们可以在一台机器上运行每台机器，并在十分钟内完成所有 4000 台机器。但是我们没有。

我解决这个问题的方法是将它分成 8 个 500 个用户的块。28 天的工作大约需要 1.4 小时。不算太坏？

在本节结束时，您将使用硒元素施展完全的黑魔法:

> ![unknown tweet media content](../Images/616fba83090a08db2314252276b0d4b6.png)![Play butt](../Images/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/ext_tw_video/962604438367055873/pu/vid/320x180/_X5GSngG2ETnMmYa.mp4" type="video/mp4"></video>![shawn swyx wang 🇸🇬 profile image](../Images/9da0698fedd18ceea92c0514249f4915.png)Shawn swyx 王[@ swyx](https://dev.to/swyx)![twitter logo](../Images/ad0c7b03deabfe1a161345efb2d537eb.png)用硒自动化碎碎念刮...开始不顺利，但结果很好。开源太神奇了！2018 年 2 月 11 日上午 08:29[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=962604465886040064)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=962604465886040064)1[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=962604465886040064)16

旋转 8 个不同的 jupyter 笔记本，在每个 Firefox 实例上登录 Twitter(见上面的`driver = webdriver.Firefox()`)。清楚地给它们命名，这样你就不会不小心把每个笔记本搞混了。

现在，在每个笔记本中，您可以读取您首次跑步输出的数据:

```
df = pd.read_csv('data/BASICDATA.csv', encoding = "ISO-8859-1")
arr = df.usernames 
```

## 一种动态无限滚动策略

不要执行这段代码，只是向你展示如何使上面的基本无限滚动策略更加动态:

```
 loopCounter = 0
    lastHeight = driver.execute_script("return document.body.scrollHeight")
    while True:
        if loopCounter > 499:
            break; # if the account follows a ton of people, its probably a bot, cut it off
        driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
        time.sleep(2)
        newHeight = driver.execute_script("return document.body.scrollHeight")
        if newHeight == lastHeight:
            break
        lastHeight = newHeight
        loopCounter = loopCounter + 1 
```

本质上，存储文档的高度，如果在你滚动到底部后它停止增长，那么推断你已经到达了末尾(`lastHeight == newHeight`)并退出循环。

## 并行化的代码

然后为每台笔记本电脑设置合适的范围。所以这本书涵盖了用户 500 - 999:

```
for i in range(500,1000):
    currentUser = arr[i]
    print('now doing user ' + str(i) + ': ' + currentUser)
    driver.base_url = "https://twitter.com/" + currentUser + "/following"
    driver.get(driver.base_url)
    time.sleep(3) # first load
    loopCounter = 0
    lastHeight = driver.execute_script("return document.body.scrollHeight")
    while True:
        if loopCounter > 499:
            break; # if the account follows a ton of people, its probably a bot, cut it off
        if loopCounter > 0 and loopCounter % 50 == 0:
            print(loopCounter)
        driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
        time.sleep(2)
        newHeight = driver.execute_script("return document.body.scrollHeight")
        if newHeight == lastHeight:
            break
        lastHeight = newHeight
        loopCounter = loopCounter + 1
    print('ended at: ' + str(loopCounter))
    html_source = driver.page_source
    sourcedata = html_source.encode('utf-8')
    soup=bs(sourcedata)
    temparr = [x.div['data-screen-name'] for x in soup.body.findAll('div', attrs={'data-item-type':'user'})]
    tempbios = [x.p.text for x in soup.body.findAll('div', attrs={'data-item-type':'user'})]
    fullnames = [x.text.strip() for x in soup.body.findAll('a', 'fullname')][1:] # avoid your own name
    d = {'usernames': temparr, 'bios': tempbios, 'fullnames': fullnames}
    df = pd.DataFrame(data=d)
    df.to_csv('data/' + currentUser + '.csv') 
```

我想非常清楚当我在过多的日志站点上出错时会发生什么。当像这样开发自动化时，你会不时地遇到错误，你不希望不得不返回并重新启动运行良好的自动化。因此，从你坠落的地方捡起来的能力是一件好事。(你也可以实现更好的错误处理，但这会限制你在错误发生时的反应能力和*修复未来错误的能力*。)

## 收集更深一级数据如下

我第一次这样做的时候，我只做了上面这些，但是我很快发现我需要更多的数据来完成我的第一学位。所以我点燃了另一个笔记本。这一次，我想访问每个用户的“with_replies”页面，从他们的时间线中获取一些数据。有了这个，我可以了解一些“参与度”(原创内容的评论、点赞和转发的总量)及其积极性(基于推文自动解析的情绪得分，以查看账户主要是积极还是消极)。

在 firefox 中进行与上面相同的登录过程，然后读入原始数据:

```
df = pd.read_csv('data/BASICDATA.csv', encoding = "ISO-8859-1")
arr = df.usernames 
```

我们只是将它用于用户名列表。

然后我们初始化数据帧:

```
main = pd.DataFrame(data = {
        'user': ['swyx'],
        'text': ['text'],
        'tweetTimestamps': ['tweetTimestamps'],
        'engagements': ['engagements'],
        'name': ['name'],
        'loc': ['loc'],
        'url': ['url'],
        'stats_tweets': ['stats_tweets'],
        'stats_following': ['stats_following'],
        'stats_followers': ['stats_followers'],
        'stats_favorites': ['stats_favorites'],
    }) 
```

现在我们在`arr`数组:
中查看每个用户的个人资料

```
def getTimestamps(x):
    temp = x.findAll('span', '_timestamp')
    if len(temp) > 0:
        return temp[0].get('data-time')
    else:
        return None
# now get the user's own timeline for i in range(0,len(arr)):
    currentUser = arr[i]
    print('doing user:' + str(i) + ' ' + currentUser)
    driver.base_url = "https://twitter.com/" + currentUser + '/with_replies'
    driver.get(driver.base_url)
    html_source = driver.page_source
    dailyemail_links = html_source.encode('utf-8')
    soup=bs(dailyemail_links, "lxml")
    time.sleep(2)
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(1)
    # name
    name = soup.find('a', "ProfileHeaderCard-nameLink").text
    # loc
    temp = soup.find('span', 'ProfileHeaderCard-locationText')
    temp = temp.text if temp else ''
    loc = temp.strip() if temp else ''
    # url
    temp = soup.find('span', 'ProfileHeaderCard-urlText')
    temp = temp.a if temp else None
    temp2 = temp.get('title') if temp else None
    url = temp2 if temp2 else (temp.get('href') if temp else None)
    # stats
    temp = soup.find('a',{'data-nav': 'tweets'})
    stats_tweets = temp.find('span', 'ProfileNav-value')['data-count'] if temp else 0
    temp = soup.find('a',{'data-nav': 'following'})
    stats_following = temp.find('span', 'ProfileNav-value')['data-count'] if temp else 0
    temp = soup.find('a',{'data-nav': 'followers'})
    stats_followers = temp.find('span', 'ProfileNav-value')['data-count'] if temp else 0
    temp = soup.find('a',{'data-nav': 'favorites'})
    stats_favorites = temp.find('span', 'ProfileNav-value')['data-count'] if temp else 0
    # all text
    text = [''.join(x.findAll(text=True)) for x in soup.body.findAll('p', 'tweet-text')]
    # most recent activity
    alltweets = soup.body.findAll('li', attrs={'data-item-type':'tweet'})
    tweetTimestamps = list(map(getTimestamps, alltweets)) if len(alltweets) > 0 else 0
    # engagements
    noretweets = [x.findAll('span', 'ProfileTweet-actionCount') for x in alltweets if not x.div.get('data-retweet-id')]
    templist = [x.findAll('span', 'ProfileTweet-actionCount') for x in alltweets if not x.div.get('data-retweet-id')]
    templist = [item for sublist in templist for item in sublist]
    engagements = sum([int(x.get('data-tweet-stat-count')) for x in templist if x.get('data-tweet-stat-count')])
    main = pd.concat([main, pd.DataFrame(data = {
        'user': [currentUser],
        'text': [text],
        'mostrecentTimestamp': [tweetTimestamps],
        'engagements': [engagements],
        'name': [name],
        'loc': [loc],
        'url': [url],
        'stats_tweets': [stats_tweets],
        'stats_following': [stats_following],
        'stats_followers': [stats_followers],
        'stats_favorites': [stats_favorites],
    })])
    main.to_csv('data/BASICDATA_profiles.csv') 
```

现在，我们的`main`数据框架中有了每个账户的所有更详细的数据！它也被导出到`BASICDATA_profiles.csv`文件中。

# 分析

当所有自动化正在进行时，我们可以继续我们的主数据集！

旋转一个新的 jupyter 笔记本，这次只是为了数据分析。导入通常的东西，但是这次我们也将使用 Textblob 进行情感分析，所以继续导入 TextBlob: `from textblob import TextBlob`

请注意，您还需要下载一些用于 Texblob 的 corpuses，但是当您运行下面的代码时，错误提示会引导您非常容易地完成下载(这是 Anaconda 中的一行程序)。

我们可以对从 Twitter 上获得的微薄数据进行一些特征工程。特别是，我们可以尝试:

*   对账户类型进行分类(开发者、制造者、创始人等)
*   猜测帐户的性别(基于用户的全名)-人们希望关注科技领域的女性
*   评价账户推文的积极度——人们希望他们的推文中有更多积极的内容。

这些都是容易出错的，但仍然值得一试，如果他们能提出一个更好的信号，我可以使用。

```
df1 = pd.read_csv('data/BASICDATA.csv', encoding = "ISO-8859-1")
df2 = pd.read_csv('data/BASICDATA_profiles.csv', encoding = "ISO-8859-1").set_index('user')[1:].drop(['Unnamed: 0'], axis=1).drop(['tweetTimestamps'], axis=1)
df2['bios'] = df1.set_index('usernames')['bios']
arr = df1.usernames
jslist = [ 'react', 'webpack', ' js', 'javascript','frontend', 'front-end', 'underscore','entscheidungsproblem', 'meteor']
osslist = [' oss', 'open source','maintainer']
designlist = ['css', 'designer', 'designing']
devlist = [' dev','web dev', 'webdev', 'code', 'coding',  'eng',  'software', 'full-stack', 'fullstack', 'backend', 'devops', 'graphql', 'programming',  'computer', 'scien']
makerlist = ['entrepreneur', 'hacker', 'maker', 'founder', 'internet', 'web']
def categorize(x):
    bio = str(x).lower()
    if any(s in bio for s in jslist):
        return 'js'
    elif any(s in bio for s in osslist):
        return 'oss'
    elif any(s in bio for s in designlist):
        return 'design'
    elif any(s in bio for s in devlist):
        return 'dev'
    elif any(s in bio for s in makerlist):
        return 'maker'
    else:
        return ''
df2['cat'] = list(map(categorize,df2['bios']))
df2['stats_followers'] = list(map(lambda x: int(x), df2['stats_followers']))
df2['stats_following'] = list(map(lambda x: int(x), df2['stats_following']))
df2['stats-ratio'] = df2.apply(lambda x: x['stats_followers']/x['stats_following'] + math.sqrt(x['stats_followers']) if x['stats_following'] > 1 else math.sqrt(x['stats_followers']), axis=1) 
df2['stats-ratio'] = list(map(lambda x: min(200,x), df2['stats-ratio']))
df2['positivity'] = df2['text'].apply(lambda y: sum([x.sentiment.polarity for x in TextBlob(' '.join(y)).sentences]))
df2['eng_ratio'] = df2.apply(lambda x: math.log(int(x['engagements']))/math.log(x['stats_followers']) if int(x['engagements']) > 0 and int(x['stats_followers']) > 1 else 0, axis=1) 
```

所以如果你检查一下`df2`，你现在有几个可以使用的字段。“cat”字段表示我们根据其简历中的关键字将我们的关注者分成不同的组。从某种程度上来说，没有人真的能被放在一个桶里，这是一个永无止境的任务，但我们可以尝试:)(如果我们要对此应用一些机器学习，一个 [K 最近邻](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm)方法可能会在这里工作，因为我们可以使用 Textblob 分解关键字)

下面是我的分类是如何爆发的:

```
print(len(df2[df2['cat'] == 'maker'])) # 573 print(len(df2[df2['cat'] == 'design'])) # 136 print(len(df2[df2['cat'] == 'oss'])) # 53 print(len(df2[df2['cat'] == 'js'])) # 355 print(len(df2[df2['cat'] == 'dev'])) # 758 
```

好了，现在我们有进展了。

我们还设计了一系列其他指标，例如`stats-ratio`，它是关注者与关注者的比率加上关注者的平方根，最大值为 200。这是一个任意的公式，允许高影响力的人的影响力，但限制超级巨星的影响力。

`eng_ratio`是参与度比率，它试图将参与度(原创内容的点赞、转发和评论)作为关注者的比率(如果你有更多的关注者，你自然可能会有更多的参与度，所以最好看一下比率)。

我们跳过了很多关于分析和特性工程的工作，但这就是我现在所做的:)。

# 显示

好的，这实际上是最难的部分。如果我调出并合并 355 个 twitter 账户的 fofollower 数据，归类为“js”dev，我会得到来源和目的地之间超过 200，000 条边:

```
import os.path
def getData(x):
    fp = 'data/' + x + '.csv'
    if  os.path.isfile(fp):
        temp = pd.read_csv(fp, encoding = "ISO-8859-1")[['usernames', 'bios']] 
        temp.columns = ['target', 'bios']
        temp['source'] = x
        temp['cat'] = list(map(categorize,temp['bios'])) # categorize the bios of the fofollows
        return temp
temp = list(map(getData, list(df2[df2['cat'] == 'js'].index)))
combined = pd.concat(temp) # all target-source relationships originating from 'js' 
```

然后我可以选择显示数据:

```
screened = combined.groupby(by='target').count().sort_values(by='source', ascending=False)[:50][['bios']]
screened.columns = ['fofollow_count'] 
screened_with_combined_info = screened
screened_with_combined_info['bios'] = combined.groupby(by='target').first()[['bios']]
screened_with_combined_info['cat'] = combined.groupby(by='target').first()[['cat']] 
```

降价显示的格式...

```
 df = screened_with_combined_info.reset_index()[['target','fofollow_count','cat','bios']]
df['target'] = df['target'].apply(lambda x: "[" + x + "](https://twitter.com/" + x + ")")
# Get column names cols = df.columns

# Create a new DataFrame with just the markdown
# strings df2 = pd.DataFrame([['---',]*len(cols)], columns=cols)

#Create a new concatenated DataFrame df3 = pd.concat([df2, df])

#Save as markdown df3.to_csv("nor.md", sep="|", index=False) 
```

# 排名前 50 的 JS Dev Twitter 账户

| 目标 | fofollow_count | 猫 | bios |
| --- | --- | --- | --- |
| [dan_abramov](https://twitter.com/dan_abramov) | Two hundred and ten | 射流研究… | 正在研究@reactjs。Redux 和 Create React App 的合著者。为人类制造工具。 |
| [保罗 _ 爱尔兰人](https://twitter.com/paul_irish) | One hundred and ninety | 制造者 | 网络太棒了，让我们把它变得更好？我从事 web 性能方面的工作，@ _ _ _ _ light house & @ ChromeDevTools。黑麦威士忌，数据和奇思妙想的忠实粉丝 |
| [反应堆](https://twitter.com/reactjs) | One hundred and eighty-nine | 射流研究… | React 是一个声明式的、高效的、灵活的 JavaScript 库，用于构建用户界面。 |
| [加法器](https://twitter.com/addyosmani) | One hundred and eighty-one | 偏差 | 英语。谷歌的经理在研究@GoogleChrome & Web DevRel？TodoMVC 的创作者，@Yeoman，材料设计 Lite，批判？Team @workboxjs？？ |
| [莎拉 _ 埃多](https://twitter.com/sarah_edo) | One hundred and eighty-one | 设计 | 获奖的演讲者。微软资深开发者代言人。@vuejs 核心团队，作家@Real_CSS_Tricks，联合创始人@ webanimworkshop，作品:？ |
| 吸烟 | One hundred and seventy-three |  | @zeithq |
| 游戏 | One hundred and sixty-nine | 射流研究… | 脸书的法国前端工程师。致力于反应，反应自然，漂亮，瑜伽，核素和其他一些很酷的东西... |
| [杰克逊](https://twitter.com/mjackson) | One hundred and fifty-eight | 射流研究… | Thriller，创始人@ReactTraining，创建者@unpkg，组织者@shape_hq，成员@LDSchurch |
| [肯特多德](https://twitter.com/kentcdodds) | One hundred and fifty-seven | 射流研究… | 让软件开发更容易理解丈夫、父亲、摩门教徒、教师、OSS、GDE、@ TC39 @ pay paleng @ eggheadio @ frontend masters？ |
| sebmarkbage | One hundred and fifty-seven | 射流研究… | 脸书的推文是私人的 |
| [mxstbr](https://twitter.com/mxstbr) | One hundred and fifty-seven | 射流研究… | 联合创始人@ with spectrum Advisor[@ educativeinc](https://dev.to/educativeinc)制作风格组件、反应样板和微分析专业咖啡极客？ |
| [ryanflorence](https://twitter.com/ryanflorence) | One hundred and fifty-six | 射流研究… | 业主 [http://Workshop.me](http://Workshop.me%C2%A0) 和 http://TotalReact.com[和](http://TotalReact.com%C2%A0) |
| [thelarkin](https://twitter.com/TheLarkInn) | One hundred and fifty-five | 射流研究… | 演讲者、工程师、#webpack 核心团队、开发者倡导者、农民。观点是我自己的。TPM @ Microsoft @ MSEdgeDev @ EdgeDevTools。？ |
| [jeresig](https://twitter.com/jeresig) | One hundred and forty-nine | 射流研究… | @jquery 的创造者，JavaScript 程序员，作者，日本木刻书呆子([http://ukiyo-e.org](http://ukiyo-e.org%C2%A0))，工作于@khanacademy。 |
| [sebmck](https://twitter.com/sebmck) | One hundred and forty-seven | 射流研究… | 澳大利亚人我写 JavaScript 嫁给了 [@anagobarreto](https://dev.to/anagobarreto) |
| [_ develoit](https://twitter.com/_developit) | One hundred and forty-five | 射流研究… | 谷歌的 Chrome DevRel。@preactjs 的创建者。少花钱多办事。[http://github.com/developit](http://github.com/developit%C2%A0) |
| [林肯拉克](https://twitter.com/linclark) | One hundred and forty-four | 偏差 | 往我脑子里塞代码，然后把它变成 code 卡通。还有，在@mozilla 上修补 WebAssembly、@ServoDev 和一点点@rustlang |
| [sophiebits](https://twitter.com/sophiebits) | One hundred and forty-three | 射流研究… | 我喜欢修理东西。脸书@reactjs 的工程经理。 [ex-@khanacademy](mailto:ex-@khanacademy) 。她/她。善良，交叉女权主义，音乐。 |
| [絮凝剂](https://twitter.com/floydophone) | One hundred and forty-three | 射流研究… | 联合创始人兼首席执行官@HelloSmyte。Ex-FB 和 Instagram。在 React.js 上工作。 |
| [jlongster](https://twitter.com/jlongster) | One hundred and forty-two | 偏差 | 承包为移位复位有限责任公司。使用@actualbudget。创建了@PrettierCode。前 Mozilla。喜欢函数式编程。 |
| [肯 _ 惠勒](https://twitter.com/ken_wheeler) | One hundred and forty-one | 航天科学局 | OSS 主任@FormidableLabs？职业美国人？曼希尔德？爸爸？@baconbrix 的爸爸？所有意见都是米勒 Lite 的意见？@toddmotto 粉丝 |
| [左 _ 垫](https://twitter.com/left_pad) | One hundred and forty |  | 一位社区志愿者和@babeljs 的管家。@Behance，@Adobe。Soli Deo Gloria |
| 痤疮 | One hundred and forty | 射流研究… | @reactjs 核心在脸书。嗨！ |
| [节点 j](https://twitter.com/nodejs) | One hundred and thirty-seven | 射流研究… | Node.js JavaScript 运行时 |
| [jordwalke](https://twitter.com/jordwalke) | One hundred and thirty-five | 射流研究… | 事物的制造者:ReactJS。正在处理:@reasonml。At:脸书工程公司。 |
| [github](https://twitter.com/github) | One hundred and thirty-two | 偏差 | “人们如何构建软件。需要帮助吗？请在 http://git.io/c 给我们发消息寻求支持。” |
| [里布](https://twitter.com/leeb)里布 | One hundred and thirty-two | 射流研究… | 从 2008 年开始在脸书做东西:React，GraphQL，Immutable.js，Mobile，JavaScript，废话 |
| [布伦丹内克](https://twitter.com/BrendanEich) | One hundred and thirty | 射流研究… | 创建了 JavaScript。共同创建了 Mozilla 和 Firefox。现创始人& CEO @ Brave Software([https://brave.com/](https://brave.com/%C2%A0))。 |
| [cpojer](https://twitter.com/cpojer) | One hundred and twenty-nine | 偏差 | 曾任脸书地铁 Jest Yarn 的 Pojer 工程经理 |
| 劳施马 | One hundred and twenty-eight | 射流研究… | “JavaScript: blog @2ality，books @ExploringJS，training，newsletter @ESnextNews。ReasonML: tweets @reasonmlhub，时事通讯？” |
| [韦斯博斯](https://twitter.com/wesbos) | One hundred and twenty-five | 射流研究… | 全栈开发？JS CSS 节点？ [https://ES6.io](https://ES6.io%C2%A0) ？https://LearnNode.com[？http://ReactForBeginners.com](https://LearnNode.com%C2%A0)[？http://JavaScript30.com](http://ReactForBeginners.com%C2%A0)？小贴士？@KaitBos？@SyntaxFM |
| [wycats](https://twitter.com/wycats) | One hundred and twenty-five | 航天科学局 | Tilde 联合创始人，OSS 爱好者，世界旅行家。 |
| 本莱什 | One hundred and twenty-one | 偏差 | @Google，#RxJS 核心团队软件工程师。偶尔我会在@moderndotweb 播客上装傻。观点是我自己的。 |
| 辛德雷索胡斯 | One hundred and twenty | 航天科学局 | 事物的制造者；macOS 应用程序和 CLI 工具。目前进入 Swift 和 Node.js .全职开源。开始@AVA__js。 |
| tjholowaychuk | One hundred and nineteen | 偏差 | [https://apex.sh](https://apex.sh%C2%A0) 创始人& solo 开发者，非创业公司。【https://github.com/tj[https://medium.com/@tjholowaychuk](https://github.com/tj%C2%A0)T4。阿霞的。 |
| [尤娜](https://twitter.com/Una) | One hundred and eighteen | 偏差 | 产品设计总监@bustle，谷歌开发专家&联合主持@toolsday。数字海洋。旅行生活:[http://Instagram.com/unakravets](http://Instagram.com/unakravets%C2%A0) |
| [指针数组](https://twitter.com/peggyrayzis) | One hundred and seventeen | 航天科学局 | 通过代码、旅行和音乐探索世界开源工程师@apollographql |
| 埃隆马斯克 | One hundred and seventeen |  |  |
| [jaffathecake](https://twitter.com/jaffathecake) | One hundred and fifteen | 制造者 | 谷歌人。我想让网络做本地最擅长的事情，而且要快。没有什么思想是不公开的。IMO '隐含的。 |
| [youyuxi](https://twitter.com/youyuxi) | One hundred and fifteen | 射流研究… | 设计、代码&介于两者之间的东西。全职开源。创作者@vuejs，之前是@meteorjs & @google，@parsonsamt 校友。 |
| [jdalton](https://twitter.com/jdalton) | One hundred and thirteen | 射流研究… | JavaScript 修补者、错误修复者和基准测试者？洛达什的创造者？前脉轮性能 PM？当前 Web 应用和框架 PM @Microsoft。 |
| [雄性化](https://twitter.com/samccone) | One hundred and thirteen |  | harbourmaster @google |
| [markdalgleish](https://twitter.com/markdalgleish) | One hundred and thirteen | 设计 | CSS 模块共同创建者，@MelbJS 组织者。全栈 ECMAScript 爱好者、UI 设计爱好者、咖啡饮用者 design ops @ seek jobs 负责人 |
| thejamesstyle | One hundred and twelve |  |  |
| 汤姆戴尔 | One hundred and twelve | 射流研究… | JavaScript thinkfluencer |
| [_ 成楼](https://twitter.com/_chenglou) | One hundred and nine | 射流研究… | 我的名字前面有一个下划线 |
| 马蒂亚斯 | One hundred and seven | 射流研究… | 我在 Google 做@v8js，通过 TC39 做 ECMAScript。JavaScript，HTML，CSS，HTTP，性能，安全，Bash，Unicode，i18n，macOS。 |
| [iam_preethi](https://twitter.com/iam_preethi) | One hundred and six | 偏差 | 区块链工程师。建立新公司(谢林)。明矾@coinbase @a16z @GoldmanSachs。对区块链和加密充满热情。狂热？ |
| [滴答声](https://twitter.com/threepointone) | One hundred and six | 射流研究… | Entscheidungsproblem |
| [JavaScriptDaily](https://twitter.com/JavaScriptDaily) | One hundred and five | 射流研究… | 每日 JavaScript / JS 社区新闻、链接和事件。前往@reactdaily 获取 React 新闻。 |

这些是排名前 50 的 JS 开发人员，其他开发人员紧随其后！喔！在 4100 个单词之后，这是个不错的地方，是吧？

当然，我还有更多的数据分析要做，但我会把结果放在一个单独的帖子上，按性别、地点等划分更多的参与度和关注率数据。不要脸的插时间:[如果你想在我把它放出来的时候得到通知，请跟我来](https://twitter.com/swyx)！

你还能用你的数据做什么？把它贴在某个地方，我很乐意把它发布出去！

* * *

这是关于应用数据科学来搜集和分析我的 Twitter 网络图的一系列文章中的第一篇，所以我可能会跟进更多关于这样做的细节。请在评论中让我知道你的反馈，这样我就可以在未来的帖子中加入它。