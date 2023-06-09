# 需要帮助不和谐的网页挂钩

> 原文：<https://dev.to/spyderexe/need-help-with-discord-webhooks-6b7>

你好，开发者社区！首先，如果这不是发表这篇文章的正确地方，我要道歉，乍一看，这似乎是一个写文章和发表文章的地方，而不是发表编码错误的地方，但在怀疑中，我真的很想得到答案，因为其他论坛(Stack)没有多大帮助

我在 Node.js 和 javascript 等方面不是很有经验，我想给我的 discord 服务器添加一个 webhook。我找到了这个我想实现的 github 项目:[https://github.com/FrankenMan/me_irl-webhook](https://github.com/FrankenMan/me_irl-webhook)

所以我遵循了 discord 文档，创建了一个 fork，添加了 webhook，一切似乎都正常(每次我提交时都会自动收到 discord 消息)，但是这个机器人什么也不做。

这是我的叉子:[https://github.com/Spyder-exe/me_irl-webhook/](https://github.com/Spyder-exe/me_irl-webhook/)

所以我做了一些研究，发现这个机器人需要一个 config.json 文件和一个 posts.json 文件。所以我重命名了 config.json.example 并添加了我的 webhook 的 id 和令牌，创建了一个空白的 posts.json 文件。

我还修改了 package.json 文件，因为这个项目已经存在一年了:

```
"dependencies": {
  "discord.js": "^11.0.0",
  "erlpack": "github:hammerandchisel/erlpack",
  "request": "^2.79.0",
  "uws": "^0.13.0"
} 
```

对此:

```
"dependencies": {
  "discord.js": ">=11.0.0",
  "erlpack": "github:discordapp/erlpack",
  "request": ">=2.79.0",
  "uws": ">=0.13.0"
} 
```

然而，该机器人似乎仍然没有做任何事情，这里是主要的 bot.js 代码，同样，我不是 Javascript 的经验，所以我不能告诉什么是错的

```
const Discord = require('discord.js');
const request = require('request');
const fs = require('fs');
const config = require('./config.json');
const posts = require('./posts.json');

const webhook = new Discord.WebhookClient(config.webhookid, config.webhooktoken);

const postDict = JSON.parse(fs.readFileSync('./posts.json', 'utf8'));
//function for logging ids and urls of posts to stop repeat posts.
function postLog(postId, postUrl) {
    postDict[postId] = {
    url: postUrl
  }
  fs.writeFile('./posts.json',JSON.stringify(postDict), (err) => {
    if (err) console.error(err)
  })
}

function fetchRedditPost() {
request(config.url, function(error,response,body) {
      var ok = JSON.parse(body)
      ok.data.children.forEach(function(ok){
        let NUT = "imgur.com"
        let ext = ".jpg"
        let otherExt = ".gif"
        let dril = ".gifv"
        let r34 = ".png"
        let alb = "/a/"
        //checking if it's an imgur link without .jpg, .gif, .gifv, .png
    if (ok.data.url.includes(NUT) && !ok.data.url.includes(ext && otherExt && dril && r34)) {
       const SHACK = ok.data.url + ext
       //skip imgur album links
     if (ok.data.url.includes(alb)) return;
     //check if this post has been logged. If false, post it on Discord and log it, if true, do not post it
      if (!postDict[ok.data.id]){
        webhook.sendMessage(`${ok.data.title}\n${SHACK}`);
       postLog(ok.data.id, SHACK)
      }
      else {
         return
      }
    }
    //urls containing i.reddituploads.com don't show up in Discord
    else if (ok.data.url.includes("i.reddituploads.com")){
     if (!postDict[ok.data.id]) {
     postLog(ok.data.id,ok.data.preview.images[0].source.url);
     webhook.sendMessage(`${ok.data.title}\n${ok.data.preview.images[0].source.url}`)
     }
     else {
        return;
     }
    }
    else{
      if (!postDict[ok.data.id]){
        postLog(ok.data.id, ok.data.url)
        webhook.sendMessage(`${ok.data.title}\n${ok.data.url}`);
      }
      else{
        return;
      }

    };
      })
})
};
function redditInterval() {
 setInterval(() => (fetchRedditPost()), 36000);
}
redditInterval(); 
```