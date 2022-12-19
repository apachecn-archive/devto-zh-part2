# 带 NojeJS 的 HTTPS 服务器

> 原文：<https://dev.to/ab0nilla/https-server-with-nojejs-315i>

嗨，:D

这个星期我为信使(脸书聊天)写了一个小机器人。我已经有几年没有 fb 了。

我重新发现了 facebook 社交网络并加入了 developer.facebook.com，这个网站的文档很混乱。

良好的...

你需要在 HTTPS 服务器上运行你的网络钩子，脸书检查你的证书；如果在本地 https 上运行 nodejs 对于自己生成的证书，脸书说:“…”我不记得 xD，但是脸书不接受你的“不安全”的连接和你用 openssl 自己生成的证书。

我尝试用 openssl 安装我自己生成证书，但没有找到:(

但是！..在我的岗位上

[![ab0nilla image](../Images/f8acdfbb4ce360ac2e0873832defd126.png)](/ab0nilla) [## 在 Centos 7 中使用 Certbot 安装 SSL 证书

### 亚历杭德罗博尼拉 7 月 30 日 181 分钟阅读

#certbot #centos7 #apache #ssl](/ab0nilla/install-ssl-certificate-with-certbot-in-centos-7-47b2)I use "certbot" and in this project I used an online service: zerossl.com they also use let's Encrypt.

去 Zerossl.com 并获得您的证书，在网站上搜索“服务常见问题”和“操作视频”,并获得您的 CA、证书和密钥档案。

将下载文件复制到您的工作区文件夹中。

好了，你有了在 zerossl.com 生成的文件，现在编写简单 NodeJS 的日常代码；并添加此要求。

```
var https = require("https");] 
```

Enter fullscreen mode Exit fullscreen mode

添加您的下载文件(zerossl.com 证书)

```
const options = {
  key: fs.readFileSync("/dir/key.key"),
  cert: fs.readFileSync("/dir/crt.crt"),
  ca: fs.readFileSync("/dir/ca.ca"),
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后替换你的

```
app.listen(3000); 
```

Enter fullscreen mode Exit fullscreen mode

为此:

```
https.createServer(options, app).listen(port,console.log("webhookk listen")). 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:

```
'use strict';

THE REQUIRES AND process.env.PORT AND BLAH BLAH cons and other magic trick

var https = require("https");
var  fs = require("fs");

const options = {
  key: fs.readFileSync("YOURDIR/key.key"),
  cert: fs.readFileSync("YOURDIR/crt.crt"),
  ca: fs.readFileSync("YOURDIR/ca.ca"),
};

app.post('/webhook', (req, res) => {  
 yourcode
});

app.get('/webhook', (req, res) => {
   yourcode
  });

function handleMessage(sender_psid, received_message) {
   yourcode
}

function handlePostback(sender_psid, received_postback) {
  yourcode
  callSendAPI(sender_psid, response);
}

function callSendAPI(sender_psid, response) {
  yourcode
}
https.createServer(options, app).listen(port,console.log("webhookk listen")); 
```

Enter fullscreen mode Exit fullscreen mode

现在，脸书接受你的安全连接:D

请记住，证书会过期，您必须续订。

*记住 2: Https 使用端口 443，配置你的防火墙，移动你的其他服务器(apache，nginx)。*