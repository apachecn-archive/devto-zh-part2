# 格式化推文:看看扩展推文、转发和引用

> 原文：<https://dev.to/kehers/formatting-tweets-a-look-at-extended-tweets-retweets-and-quotes-n5j>

我在 [thefeed.press](https://thefeed.press) 上发现的一件事是，围绕共享链接的对话(推文)有时比链接更有趣。适当强调这些推文意味着在任何需要的地方展示它们；例如电子邮件摘要。显示它们意味着正确地格式化它们。

### 简介

要正确显示一条推文，它需要格式良好。这意味着识别和链接实体，如用户名、标签和网址。简单来说，就是将一个典型的 tweet 对象 <sup id="fnref1">[1](#fn1)</sup> 转换成这样:

```
{  "created_at":  "Mon Mar 05 21:16:46 +0000 2018",  "id":  970770116043595800,  "text":  "Wish I have some time to curate #WeAreNigerianCreatives. Someone please do.",  "entities":  {  "hashtags":  [{  "text":  "WeAreNigerianCreatives",  "indices":  [32,  55]  }  ],  "symbols":  [],  "user_mentions":  [],  "urls":  []  }  } 
```

对此:

> 希望我能有时间策划一些老年创意。有人请做。

请注意，tweet 对象的`text`是普通的无格式文本，但是还有一个额外的`entities`对象，其中包含了格式的必要细节。你可能不需要写一个库来匹配和替换文本中的实体。Twitter 提供了 [Twitter 文本](https://github.com/twitter/twitter-text)，这是一个做这件事的神奇库。

这是 Node.js 中的一个表示。

```
// twitter-text already installed with 
// `npm install twitter-text`
// ...
const twitter = require('twitter-text')
    , tweet = {
        "created_at": "Mon Mar 05 21:16:46 +0000 2018",
        "id": 970770116043595800,
        "text": "Wish I have some time to curate #WeAreNigerianCreatives. Someone please do.",
        "entities": {
          "hashtags": [{
              "text": "WeAreNigerianCreatives",
              "indices": [32, 55]
            }
          ],
          "symbols": [],
          "user_mentions": [],
          "urls": []
        }
      }
    ;

console.log(twitter.autoLinkWithJSON(tweet.text, tweet.entities); 
```

### 向扩展推文问好

对于超过 140 个字符的 tweet，默认情况下，tweet 对象只返回 140 个字符的文本。在这种兼容模式下，

1.  `text`被截断为 140 个字符
2.  对于超过 140 个字符的推文，`truncated`被设置为`true`
3.  仅包括可用的 140 个文本范围内的内容

下面是一个 tweet 对象的例子

```
{  "created_at":  "Sat Mar 10 18:12:17 +0000 2018",  "id":  972535628742078500,  "text":  "I kind of hate how with most web development/new frameworks etc., I start out with the intention “I’d like to spend… https://t.co/A10WmSzVeL",  "truncated":  true,  "entities":  {  "hashtags":  [],  "symbols":  [],  "user_mentions":  [],  "urls":  [{  "url":  "https://t.co/A10WmSzVeL",  "expanded_url":  "https://twitter.com/i/web/status/972535628742078469",  "display_url":  "twitter.com/i/web/status/9…",  "indices":  [  117,  140  ]  }  ]  }  } 
```

将给出以下内容的格式:

> 我有点讨厌大多数网络开发/新框架等等。，我开始时的意图是“我想花……[https://twitter.com/i/web/status/972535628742078469](https://twitter.com/i/web/status/972535628742078469)…”

与原始推文相比:

> 我有点讨厌大多数网络开发/新框架等等。，我开始时的意图是“今天我想花 20 分钟学习 X”，但我不得不再花 60 分钟来建立适当的环境。

### 模式:扩展

如何获取全文？简单。将参数`tweet_mode=extended`添加到您正在查询的任何端点。所以，让我们试试`https://api.twitter.com/1.1/statuses/show/972535628742078469.json?tweet_mode=extended`，而不是`https://api.twitter.com/1.1/statuses/show/972535628742078469.json`

```
{  "created_at":  "Sat Mar 10 18:12:17 +0000 2018",  "id":  972535628742078500,  "full_text":  "I kind of hate how with most web development/new frameworks etc., I start out with the intention “I’d like to spend 20 minutes learning X today,” and have to invest an additional 60 minutes just setting up the appropriate environment.",  "truncated":  false,  "display_text_range":  [0,  234],  "entities":  {  "hashtags":  [],  "symbols":  [],  "user_mentions":  [],  "urls":  []  }  } 
```

是啊，就这么简单。请注意:

1.  `full_text`取代`text`
2.  `truncated`是`false`
3.  `display_text_range`标识推文可显示内容的开始和结束。

然后，您可以继续使用`full_text`和`entities`进行格式化。

```
const twitter = require('twitter-text')
    , tweet = {
        "created_at": "Sat Mar 10 18:12:17 +0000 2018",
        "id": 972535628742078500,
        "full_text": "I kind of hate how with most web development/new frameworks etc., I start out with the intention “I’d like to spend 20 minutes learning X today,” and have to invest an additional 60 minutes just setting up the appropriate environment.",
        "truncated": false,
        "display_text_range": [0, 234],
        "entities": {
          "hashtags": [],
          "symbols": [],
          "user_mentions": [],
          "urls": []
        }
      }
    ;

console.log(twitter.autoLinkWithJSON(tweet.full_text, tweet.entities); 
```

### 嗯嗯……转发

这是在扩展模式下请求的转发。

```
{  "created_at":  "Sun Mar 11 12:00:27 +0000 2018",  "id":  972804442667003900,  "full_text":  "RT @jasongorman: As a physics grad, I understand how snooker works at a level I imagine a lot of pro snooker players don't. But I suck at s…",  "truncated":  false,  "display_text_range":  [  0,  140  ],  "entities":  {  "hashtags":  [],  "symbols":  [],  "user_mentions":  [  {  "screen_name":  "jasongorman",  "name":  "jasongorman",  "id":  18771008,  "id_str":  "18771008",  "indices":  [  3,  15  ]  }  ],  "urls":  []  },  "retweeted_status":  {...}  } 
```

注意`full_text`是如何被截断的，即使`truncated`表示`false`。会有什么问题呢？嗯，转发中的文本以`RT @username:`为前缀，如果结果文本超过 140 个字符，它将被截断。

怎么办？使用`retweeted_status`代替。`retweeted_status`对象包含您需要的全部文本和实体。

```
{  "created_at":  "Sun Mar 11 12:00:27 +0000 2018",  "id":  972804442667003900,  "full_text":  "RT @jasongorman: As a physics grad, I understand how snooker works at a level I imagine a lot of pro snooker players don't. But I suck at s…",  "truncated":  false,  "display_text_range":  [...],  "entities":  {...},  "retweeted_status":  {  "created_at":  "Sun Mar 11 08:10:46 +0000 2018",  "id":  972746641957642200,  "full_text":  "As a physics grad, I understand how snooker works at a level I imagine a lot of pro snooker players don't. But I suck at snooker. Understanding != ability.",  "truncated":  false,  "display_text_range":  [0,  155],  "entities":  {  "hashtags":  [],  "symbols":  [],  "user_mentions":  [],  "urls":  []  },  }  } 
```

只需检查`retweeted_status`是否存在，然后使用它。

```
// Get tweet
// ...
if (tweet.retweeted_status)
  tweet = tweet.retweeted_status;

formatted = twitter.autoLinkWithJSON(tweet.full_text, tweet.entities); 
```

### 行情:/

报价是在一个完全不同的世界里。你需要看到引用的推文是什么样子才能理解。

```
{  "created_at":  "Sat Dec 16 04:04:36 +0000 2017",  "id":  941881722685284400,  "full_text":  "Added tweets to the daily newsletter for better context. https://t.co/Q46O3husnz",  "truncated":  false,  "display_text_range":  [0,  56],  "entities":  {  "hashtags":  [],  "symbols":  [],  "user_mentions":  [],  "urls":  [{  "url":  "https://t.co/Q46O3husnz",  "expanded_url":  "https://twitter.com/thefeedpress/status/941880801087680512",  "display_url":  "twitter.com/thefeedpress/s…",  "indices":  [57,  80  ]  }]  },  "quoted_status":  {...}  } 
```

`full_text`并没有讲述完整的故事。它不包括被引用的推文。引用的推文隐藏在`quoted_status`的某个地方。与转发不同的是，你可以用转发状态替换推文，你需要原始和附加推文才能完全理解*引用*。这里是`quoted_status`的样子:

```
{  "created_at":  "Sat Dec 16 04:00:56 +0000 2017",  "id":  941880801087680500,  "full_text":  "New newsletter screenshot https://t.co/HQmJumZfhN",  "truncated":  false,  "display_text_range":  [0,  25],  "entities":  {...},  "extended_entities":  {...}  } 
```

那么在这种情况下我们该怎么办？我们需要达到的目标是这样的:

> 为每日时事通讯添加推文，以获得更好的背景
> 
> > [@ the feedpress](https://twitter.com/thefeedpress):
> > T3】新快报截图【pic.twitter.com/HQmJumZfhN】T4

似乎我们只需要将引用的推文和附加推文分别格式化，然后一起显示。

```
const twitter = require('twitter-text')
    ;

// Get tweet
// ..
let text = twitter.autoLinkWithJSON(tweet.full_text, tweet.entities);
if (tweet.quoted_status) {
  let qt = twitter.autoLinkWithJSON(tweet.quoted_status.full_text, 
            tweet.quoted_status.entities);
  text += `<blockquote><a href="https://twitter.com/${tweet.quoted_status.user.screen_name}">@${tweet.quoted_status.user.screen_name}</a>:<br> ${qt} </blockquote>`;
}

console.log(text); 
```

> 为每日时事通讯添加推文，以获得更好的背景。[https://twitter.com/thefeedpress/status/941880801087680512…](https://twitter.com/thefeedpress/status/941880801087680512)
> 
> > [@ the feedpress](https://twitter.com/thefeedpress):
> > T3】新快报截图【pic.twitter.com/HQmJumZfhN】T4

看起来很接近。但是附加的 tweet 有一个链接指向嵌入的引用。我们能去掉这个链接吗？让我们试试。

因为我们知道引用状态的链接总是会结束附加的 tweet 文本，我们可以用格式`https://twitter.com/[quoted_status_user_username]/status/[0-9]+`匹配链接的文本结尾并删除。不过，这有几个问题。如果我们匹配无格式文本，url 仍然是格式`http://t.co/\w+`(未展开)，而不是`https://twitter.com/[quoted_status_user_username]/status/[0-9]+`(展开)。如果我们在格式化后进行匹配，那么链接将会被扩展，但是包含的 HTML 标签将会破坏我们的正则表达式 <sup id="fnref2">[2](#fn2)</sup> 。

因为我们知道链接总是会结束文本，所以我们可以删除未格式化文本中的任何结束链接。在格式化文本之前，我们还可以从实体中删除索引。

```
if (tweet.retweeted_status)
  tweet = tweet.retweeted_status;

if (tweet.quoted_status) {
  if (tweet.entities && tweet.entities.urls) {
    let re = new RegExp('https://twitter.com/\\w+/status/'+tweet.quoted_status.id_str);
    tweet.entities.urls = tweet.entities.urls.filter(url => !re.test(url.expanded_url));
  }
  text = twitter.autoLinkWithJSON(tweet.full_text, tweet.entities);
  let qt = twitter.autoLinkWithJSON(tweet.quoted_status.full_text, tweet.quoted_status.entities);
  text = text.replace(/https:\/\/t.co\/[^\/]+$/, '');
  text += `<blockquote><a href="https://twitter.com/${tweet.quoted_status.user.screen_name}">@${tweet.quoted_status.user.screen_name}</a><br>${qt}</blockquote>`;
}
else
    text = twitter.autoLinkWithJSON(tweet.full_text, tweet.entities); 
```

### 结论

这是你可能需要的全部。但是还有更多的事情要做。如何在推文中显示媒体(图片、视频)？引号中的引号？线索回复？

如果你真的想做，格式化推文可能是一件复杂的事情。但是如果不是必要的话，真的可以不做。你可以用[嵌入推文](https://dev.twitter.com/web/embedded-tweets)来代替。

* * *

1.  出于简洁的目的，tweet 对象中的一些项目以及本文中使用的其他项目都被删除了。 [↩](#fnref1)

2.  下面是链接`https://twitter.com/thefeedpress/status/941880801087680512`的格式化 HTML 的样子`<a href="https://t.co/Q46O3husnz" title="https://twitter.com/thefeedpress/status/941880801087680512" rel="nofollow"><span class='tco-ellipsis'><span style='position:absolute;left:-9999px;'>&nbsp;</span></span><span style='position:absolute;left:-9999px;'>https://</span><span class='js-display-url'>twitter.com/thefeedpress/s</span><span style='position:absolute;left:-9999px;'>tatus/941880801087680512</span><span class='tco-ellipsis'><span style='position:absolute;left:-9999px;'>&nbsp;</span>…</span></a>` [↩](#fnref2)