# 在几分钟内建立您的知识库

> 原文：<https://dev.to/fivetwentysix/spinning-up-your-knowledge-base-in-minutes-o1c>

从这个教程开始，你需要一个 Debian/Ubuntu 服务器，你可以从 [Digital Ocean](https://m.do.co/c/a03123d75450) 用我的链接免费获得 10 美元。

一旦创建了服务器，并且您已经成功地将必要的依赖项 SSH 安装到服务器中，然后下载并解压缩 Raneto。

```
sudo apt-get install -y nodejs wget
wget https://github.com/gilbitron/Raneto/archive/0.16.0.tar.gz raneto.tar.gz
tar -zxf raneto.tar.gz
cd raneto
npm install
PORT=8080 npm start 
```

知识库应用程序应该在端口 8080 上运行。要在端口 80 上提供服务，您可以使用`iptables`简单地转发端口:

`sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080`

现在，您的应用程序也在端口 80 上运行，这是 HTTP 的标准端口。

您现在有了一个可用的知识库。如果你被卡住了，请随意评论，我会回复你的。