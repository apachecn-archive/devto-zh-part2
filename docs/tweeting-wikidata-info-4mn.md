# 在推特上发布维基数据信息

> 原文：<https://dev.to/rmhogervorst/tweeting-wikidata-info-4mn>

在这个解释器中，我将带你浏览我创建一个推特按钮的步骤，这个按钮每天都在推特上发布关于在那一天死去的人的信息。

我创建了一个脚本来查询维基数据，获取信息并创造一个句子。那句话随后被推文。

例如:

[![A tweet I literally just send out from the docker container](img/2afa493ae6d611126670478720f241bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n2f_aW8c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/post/2018-11-19-tweeting-wikidata-info_files/Screenshot%2520from%25202018-11-19%252021-32-11.png)

我刚刚从 docker 容器中发出的推文

我希望你和我一样对这个项目感到兴奋。它来了！

有 3 个部分:

1.  与维基数据交谈，检索今天死亡的 10 个人的信息
2.  抓住其中一个死亡，创造一个句子
3.  将这句话发布到推特账户[维基百科](https://twitter.com/WikidataB)
4.  将它们全部放入 docker 容器中，这样它就可以在其他人的计算机上运行(又名:THA CLOUD)

你可能会想，为什么死去的人？对此，我着重回答了一个问题，但不是很有帮助:“瓦拉·摩根利斯”。

# 1。与维基数据对话并检索信息

我认为维基数据是世界上最酷的知识库之一，它包含了关于人类、其他动物、地方和世界的事实。它为你在维基百科页面上看到的许多盒子提供动力。例如，关于查理一世的随机页面右边有一个方框，写着他的祖先、继任者和加冕典礼。同样的信息可以显示在[荷兰语](https://nl.wikipedia.org/wiki/Karel_I_van_Engeland)中。这非常酷，节省了维基百科的大量工作。但是，我们也可以用！

您可以在查询编辑器中创建自己的查询。但是很难弄清楚如何做到这一点。这些查询需要以特定的方式进行。我刚刚用了一个来自维基数据的例子:“今天是谁的生日？”并对其进行了修改，以搜索人的死亡(我就是这样学习的，修改一些东西，看看我是否打破了它)。它看起来很像 SQL，但略有不同。

当然，这个编辑器对我们人类来说很好，但我们希望计算机来做，这样我们就可以向维基数据发送查询。我非常懒，用了维基大师米哈伊尔·波波夫 [@bearlogo](%5Bhttps://twitter.com/bearloga%5D(https://twitter.com/bearloga)) 创建的`WikidataQueryServiceR`。

这是我最终使用的查询(它看起来非常像生日查询，但添加了更多信息):

```
querystring <- 
'SELECT # what variables do you want to return (defined later on)
  ?entityLabel (YEAR(?date) AS ?year) 
  ?cause_of_deathLabel 
  ?place_of_deathLabel 
  ?manner_of_deathLabel  
  ?country_of_citizenshipLabel 
  ?country_of_birth
  ?date_of_birth
WHERE {
  BIND(MONTH(NOW()) AS ?nowMonth) # this is a very cool trick
  BIND(DAY(NOW()) AS ?nowDay)
  ?entity wdt:P570 ?date.
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
  ?entity wdt:P509 ?cause_of_death.
  OPTIONAL { ?entity wdt:P20 ?place_of_death. }
  OPTIONAL { ?entity wdt:P1196 ?manner_of_death. }
  FILTER(((MONTH(?date)) = ?nowMonth) && ((DAY(?date)) = ?nowDay))
  OPTIONAL { ?entity wdt:P27 ?country_of_citizenship. }
  OPTIONAL { ?entity wdt:p19 ?country_of_birth}
  OPTIONAL { ?entity wdt:P569 ?date_of_birth.}
}
LIMIT 10' 
```

Enter fullscreen mode Exit fullscreen mode

在[查询编辑器](https://query.wikidata.org/)中试试这个

当我写这篇博文时(每天的结果都会不同)，结果看起来是这样的:

```
library(WikidataQueryServiceR)
result <- query_wikidata(querystring)

## 10 rows were returned by WDQS

result[1:3,1:3]# first 3 rows, first 3 columsn

## entityLabel year cause_of_deathLabel
## 1 Rafael García-Plata y Osma 1918 influenza
## 2 Dobroslava Menclová 1978 traffic crash
## 3 Alan J. Pakula 1998 traffic crash 
```

Enter fullscreen mode Exit fullscreen mode

该查询返回姓名、年份、死因、死亡方式(不知道使用哪一种)、死亡地点、国籍、出生国家和出生日期。我现在可以把所有这些部分粘在一起，组成一个句子

# 2。抓住其中一个死亡，创造一个句子

我将使用胶水来制作文本，但是 base R 的粘贴功能也很好。

这些是第一行，例如:

```
library(glue)
glue_data(result[1:2,], "Today in {year} in the place {place_of_deathLabel} died {entityLabel} with cause: {cause_of_deathLabel}. {entityLabel} was born on {as.Date(date_of_birth, '%Y-%m-%d')}. Find more info on this cause of death on www.en.wikipedia.org/wiki/{cause_of_deathLabel}. #wikidata")

## Today in 1918 in the place Cáceres died Rafael García-Plata y Osma with cause: influenza. Rafael García-Plata y Osma was born on 1870-03-04\. Find more info on this cause of death on www.en.wikipedia.org/wiki/influenza. #wikidata
## Today in 1978 in the place Plzeň died Dobroslava Menclová with cause: traffic crash. Dobroslava Menclová was born on 1904-01-02\. Find more info on this cause of death on www.en.wikipedia.org/wiki/traffic crash. #wikidata 
```

Enter fullscreen mode Exit fullscreen mode

# 把那句话发布到账户 wikidatabot 的 twitter 上

我创建了 twitter 账户 [wikidatabot](https://twitter.com/WikidataB) 并添加了 2fa 的图片和一些个人信息。我想说明这是一个机器人。要代表你在 twitter 上发布内容，需要一个 developer 帐户。去 https://developer.twitter.com 的[创建账户。在我的例子中，我不得不手动验证两次，因为显然我做的每件事都在 twitter 上引起了 bot activityto(他们并没有完全错)。你必须签署一些文件，承认行为准则，并理解 twitter 的条款。](https://developer.twitter.com)

下一步是创建一个 twitter 应用程序，但我会把这个解释留在 tweet 上，因为这个小插曲非常非常有帮助。

完成后，你可以在消费者密钥、访问密钥、消费者令牌和访问令牌的帮助下，在你的账户上发布到 twitter。你将需要它们，并且你必须保守它们的秘密(否则其他人会在你的账户上发帖，这是你不希望的)。

有了这些秘密和 rtweet 包，你就可以创建一个令牌，让你可以向 twitter 发帖。

这非常简单:

```
rtweet::post_tweet(status = tweettext, token = token ) 
```

Enter fullscreen mode Exit fullscreen mode

[![Again the same tweet](img/2afa493ae6d611126670478720f241bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n2f_aW8c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/post/2018-11-19-tweeting-wikidata-info_files/Screenshot%2520from%25202018-11-19%252021-32-11.png)

同样的推文

# 4 把它全部扔进一个 docker 容器

我想每天都发这个帖子，但是如果能把 R 和代码很好地打包在一起，让它在云中运行会更好。这就是 docker 的用武之地，你可以定义你想要的软件包，然后一个迷你操作系统就被创建出来了，它可以在任何一台电脑上运行(如果他们有 docker 的话)。完整的示例脚本和 docker 文件可以在 github 上的[处找到。](https://github.com/RMHogervorst/tweetwikidatadeaths)

仅此而已。如果你有关于如何在云中便宜地每天运行它的建议，请通过 [twitter](https://twitter.com/RoelMHogervorst) 或者在 [github](https://github.com/RMHogervorst/tweetwikidatadeaths/issues/new) 上开一期来告诉我。

## 可以做得更好的事情:

*   我可以运行容器，但我不知道如何让它在云中运行
*   我要求 10 个死亡，随机挑一个，不知道 sparql 有没有随机函数
*   我将(twitter)键放入脚本中，最好使用环境变量
*   rtweet 和 WikidataQueryServiceR 有很多依赖关系，这使得 docker 容器很难构建(大部分时间都很耗时)
*   我想我可以构建查询并发布到 wikidata，但是使用 WikidataQueryServiceR 要快得多
*   我希望我知道如何使用 rocker:tidyverse 容器来运行脚本，但是我还没有想出来

### 机器的状态

在创建的时候(当我编织这个文档的时候)这是我的机器的状态:**点击这里展开**

```
sessioninfo::session_info()
```

```
## ─ Session info ──────────────────────────────────────────────────────────
## setting value                       
## version R version 3.5.1 (2018-07-02)
## os Ubuntu 16.04.5 LTS          
## system x86_64, linux-gnu           
## ui X11                         
## language en_US                       
## collate en_US.UTF-8                 
## tz Europe/Amsterdam            
## date 2018-11-19                  
## 
## ─ Packages ──────────────────────────────────────────────────────────────
## package * version date source         
## backports 1.1.2 2017-12-13 CRAN (R 3.5.0) 
## blogdown 0.8 2018-07-15 CRAN (R 3.5.1) 
## bookdown 0.7 2018-02-18 CRAN (R 3.5.0) 
## clisymbols 1.2.0 2017-05-21 CRAN (R 3.5.0) 
## crayon 1.3.4 2017-09-16 CRAN (R 3.5.0) 
## curl 3.2 2018-03-28 CRAN (R 3.5.0) 
## digest 0.6.15 2018-01-28 CRAN (R 3.5.0) 
## evaluate 0.11 2018-07-17 CRAN (R 3.5.1) 
## glue * 1.3.0 2018-07-17 CRAN (R 3.5.1) 
## htmltools 0.3.6 2017-04-28 CRAN (R 3.5.0) 
## httr 1.3.1 2017-08-20 CRAN (R 3.5.0) 
## knitr 1.20 2018-02-20 CRAN (R 3.5.0) 
## magrittr 1.5 2014-11-22 CRAN (R 3.5.0) 
## R6 2.2.2 2017-06-17 CRAN (R 3.5.0) 
## Rcpp 0.12.18 2018-07-23 cran (@0.12.18)
## rmarkdown 1.10 2018-06-11 CRAN (R 3.5.0) 
## rprojroot 1.3-2 2018-01-03 CRAN (R 3.5.0) 
## sessioninfo 1.0.0 2017-06-21 CRAN (R 3.5.1) 
## stringi 1.2.4 2018-07-20 cran (@1.2.4)  
## stringr 1.3.1 2018-05-10 CRAN (R 3.5.0) 
## WikidataQueryServiceR * 0.1.1 2017-04-28 CRAN (R 3.5.1) 
## withr 2.1.2 2018-03-15 CRAN (R 3.5.0) 
## xfun 0.3 2018-07-06 CRAN (R 3.5.1) 
## yaml 2.2.0 2018-07-25 CRAN (R 3.5.1)
```