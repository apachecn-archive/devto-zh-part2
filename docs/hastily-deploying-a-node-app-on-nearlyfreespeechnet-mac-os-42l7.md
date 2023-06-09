# 在 NearlyFreeSpeech.net(Mac OS)上匆忙部署节点应用

> 原文：<https://dev.to/jenc/hastily-deploying-a-node-app-on-nearlyfreespeechnet-mac-os-42l7>

这太吓人了，我只是在记录我做了什么，这样我就能记住。感谢 Conan Lai @letmelol 的现场协助和 2015 年的这篇 Mopsled 博客，你真的应该读一下，而不是我的帖子，它只详细介绍了我使用的终端命令。

# 入门

(更多信息[见 Mopsled 帖子中的“NFSN 配置”](http://web.archive.org/web/20161108020800/http://www.mopsled.com/2015/run-nodejs-on-nearlyfreespeechnet/))

1.  做一个近乎自由的演讲。
2.  创建密码
3.  创建一个 RSA 密钥如果你没有，运行`ssh-keygen`，它会生成一个公钥和私钥。你需要公共的
4.  转到配置文件>添加 SSH
5.  要复制您的公钥，请在终端中运行`pbcopy < ~/.ssh/id_rsa.pub`
6.  将您的密钥粘贴到输入框中，然后单击 Add SSH Key

# 准备我的应用程序

我的应用程序是一个带有 express server 的 Vue-cli:此时，我的前端在一个不同于后端的文件夹中。两个不同的项目有两个 package.jsons！

1.  在我的前端项目中，用`npm run build`将构建编译到/dist 或/build 文件夹中
2.  将文件夹复制并粘贴到我的服务器端项目文件夹(express app)
3.  在我的`app.js`中，添加`app.use(express.static ('dist/'))`以将后端指向我的公共目录

可选:在`package.json`中，我可以添加一个脚本来运行应用`run.sh`

# 正在部署

(参见[项目设置](http://web.archive.org/web/20161108020800/http://www.mopsled.com/2015/run-nodejs-on-nearlyfreespeechnet/))

1.  ssh 到我的服务器进入
    `ssh username_websiteNickName>@ssh.phx.nearlyfreespeech.net`

2.  要将所有文件“上传”到服务器，请运行以下命令将所有内容复制到`home/protected/`:

    `rsync -av ./ username_websitenickname@ssh.phx.nearlyfreespeech.net:/home/protected`

    `./`表示“此文件夹中的所有内容”为本地路径，`username_websitenickname@ssh.phx.nearlyfreespeech.net:/home/protected`为远程路径。

或者，我可以使用`scp -r ./ username_websiteNickname@ssh.phx. nearlyfreespeech.net:/home/protected`

请记住在本地路径和目标路径之间保留一个空格。

很明显 rsync 真的很酷，因为它只更新你的文件中已经改变的/新的内容。

# 让它活下去

1.  参见 Mopsled 帖子上的[“配置 nearlyfreespeechnet 守护程序”和“配置代理”]([http://www . Mopsled . com/2015/run-nodejs-on-nearlyfreespeechnet/](http://www.mopsled.com/2015/run-nodejs-on-nearlyfreespeechnet/))

## 帮助:获取 TLS 证书

作为一个我认为自己是聪明人的教程，我认为在阅读了这个[要点线程](https://gist.github.com/andrewlkho/a7920cd2d019f823332f)后，我可以通过在`/home/protected`中运行`tls.sh`来创建我自己的 TLS 证书。我错了。现在这个任务一直在运行，我每天都会收到一封邮件，告诉我它是如何失败的。有人知道我该怎么做才能让`https`正常工作吗？

[![email of my server error message](img/1f0e7ee93fa76711b4c6f6da827ef6ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FLWd1l47--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c40wmsgevsdzzsfn9f2n.png)

## 更新:

在整个 duhh 时刻，我发现预定的任务在 GUI 中列出。我去了网站，在左边我发现了一个侧边栏，上面列出了“计划任务”,其中列出了 tls.setup 任务，在右边有一个“删除”按钮。