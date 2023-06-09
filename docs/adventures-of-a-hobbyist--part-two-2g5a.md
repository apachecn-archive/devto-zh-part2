# 一个业余爱好者的冒险~第二部分

> 原文：<https://dev.to/link2twenty/adventures-of-a-hobbyist--part-two-2g5a>

# 第一周的学习

[![Hello World](img/7af6fef36d1eeda1bbe5ffde3dd213ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F3F_zr0v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kq7hye8tmmlrxuve0t12.png)

## 这是什么？

这是跟随我的学习之旅的每周(ish)系列的第二部分。我希望编写一些软件来使我的工作生活更轻松，我想我应该记录我的时间。如果你对阅读第一部分感兴趣，这里有:[序言](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-one-2e0n)。

## 这一周你都在做什么？

这是我的第一周，所以我在我的电脑上安装了 NPM 和 Node，我按照这个[指南](http://blog.teamtreehouse.com/install-node-js-npm-windows)操作，尽管它是如此简单，我不需要费心。

我运行了一个常规的“Hello World”脚本。这是一个令人耳目一新的简单方法，似乎不值得分享。

```
console.log("Hello World"); 
```

Enter fullscreen mode Exit fullscreen mode

## 主项目做了什么？

我决定做一些测试，看看为了管理它们而连接到不同的服务器是否是一个可行的选择，我过去就是这样做的。

我仍然认为这是计划阶段的一部分，我是寻找失败的忠实信徒，你越早失败，你就能越早开始寻找解决方案。

我发现了一个名为 [SSH2](https://www.npmjs.com/package/ssh2) 的模块，它似乎很受欢迎，所以我想我应该看看它。文档很清晰，我决定尝试一下。

```
// Import ssh2
const SSH_Client = require('ssh2').Client;
const ssh = new SSH_Client();

// Declare private key
const privKey = `
-----BEGIN RSA PRIVATE KEY-----

-----END RSA PRIVATE KEY-----`;

// Event listener for ready state
ssh.on('ready', () => {
  ssh_connected();
});

// Called when connection established and runs command
let ssh_connected = () => {
  ssh.exec('whoami', (err, stream) => {
    if (err) throw err;
    ssh_stream(stream)
  });
}

// Handle data stream returned from server
let ssh_stream = stream => {
  stream.on('close', (code, signal) => {
    ssh.end();
  });
  stream.on('data', data => {
    console.log(data.toString('utf8'));
  });
  stream.stderr.on('data', data => {
    console.log(data.toString('utf8'));
  });
}

// Initiate connection
ssh.connect({
  host: 'server',
  username: 'andrew',
  privateKey: privKey,
}); 
```

Enter fullscreen mode Exit fullscreen mode

我运行了这个命令，令我高兴的是，我的命令提示符返回了`andrew`，成功！如果你对这个方法有什么要说的，就我所知，它可能是非常错误的，在 [github](https://github.com/ignis-pwa/ideas/issues/8) 上有一个开放的问题，请留下你的想法，谢谢。

## 接下来是什么？

接下来，我打算看看 [greenlock](https://www.npmjs.com/package/greenlock) 来感受一下用 Node 制作 web 服务器，再次请告诉我，如果你认为我的想法是错误的( [github 问题](https://github.com/ignis-pwa/ideas/issues/9))。在接下来的一周里，我想看看 MySQL 和 Node，看看能不能解决这个问题。

## 这不是一个星期...

我知道，我知道这是一个新项目，对我来说很令人兴奋，所以我花了很多时间。我想保持这些帖子相对较短，这意味着当我有足够的时间来填充一个帖子时，我可能会尝试一下，我毫不怀疑当我进入项目的更困难阶段时，这种速度会慢下来。

## 注销

谢谢你读这篇文章，有人想读我的漫谈真的让我很惊讶。如果你对这个项目有什么想法，或者你想纠正我没有写的东西，请尽管写。我在 github 上有一个开放的[资源库](https://github.com/ignis-pwa/ideas)

再次感谢🙂安德鲁