# 用流浪汉和码头工人营造木偶环境

> 原文：<https://dev.to/ikeryo1182/constracting-puppeteer-environment-with-vagrant-and-docker-5ano>

# 简介

我构建了一个环境，在这个环境中可以用流浪者和码头工人来执行木偶师，因为我不想在我的本地开发环境中安装任何程序。

如果你安装了 vagger 和 VirtualBox，这个环境就很容易构建了。

# 什么是木偶师

[puppet er](https://github.com/GoogleChrome/puppeteer)是 Google 提供的库，对浏览器的无标题测试非常有用。

木偶师能做什么...

*   屏幕上显示程序运行的图片
*   创建 PDF
*   擦
*   自动化类型表单测试
*   用 Chrome 测试 Javascript 和浏览器的功能

像 React 这样的 JavaScript Web 应用程序存在问题，但是木偶师通过使用最新的 Chrome 浏览器解决了这个问题。如果你不必做跨浏览器测试，你应该使用木偶而不是硒。

# 准备工作

*   安装[变量盒](http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html)
*   正在安装[流浪者](https://www.vagrantup.com/)
*   检查我的准备库( [GitHub 库](https://github.com/ikeryo1182/puppeteer_tutorial)

# 程序

## 1。Git 克隆

```
user:~$  cd AnyFolder
user:AnyFolder$  git clone git@github.com:ikeryo1182/puppeteer_tutorial.git 
```

Enter fullscreen mode Exit fullscreen mode

## 2。向上流浪+嘘

```
user:AnyFolder$  cd puppeteer_tutorial/vagrant_puppeteer
user:vagrant_puppeteer$  vagrant up
user:vagrant_puppeteer$  vagrant ssh 
```

Enter fullscreen mode Exit fullscreen mode

在“向上漫游”中，docker 将通过配置进行安装。

## 3。更改目录+根目录

```
[vagrant:localhost ~]$  cd puppeteer
[vagrant:localhost puppeteer]$  sudo su 
```

Enter fullscreen mode Exit fullscreen mode

## 4。设置-selinux-启用

```
 - OPTIONS='--selinux-enabled --log-driver=journald --signature-verification=false'
+ OPTIONS='--selinux-enabled=false --log-driver=journald --signature-verification=false' 
```

Enter fullscreen mode Exit fullscreen mode

## 5。Docker 启用和启动

```
[root:localhost puppeteer]$ systemctl enable docker.service
[root:localhost puppeteer]$ systemctl start docker.service 
```

Enter fullscreen mode Exit fullscreen mode

## 6。检查码头状态

```
[root:localhost puppeteer]$  systemctl status -l docker 
```

Enter fullscreen mode Exit fullscreen mode

如果显示
消息(如下),这很好

```
-->  Active: active (running) since xxx xxxx-xx-xx xx:xx:xx xxx; xxmin ago 
```

Enter fullscreen mode Exit fullscreen mode

## 7。码头工人建造

```
[root:localhost puppeteer]$  docker build -t puppeteer 
```

Enter fullscreen mode Exit fullscreen mode

如果你想看更多的细节，你应该检查 Dockerfile。

## 8。码头运行

```
[root:localhost puppeteer]$  docker run --rm -it -v $(pwd):/opt/data-volume -w /opt/data-volume puppeteer 
```

Enter fullscreen mode Exit fullscreen mode

## 9。流浪同步回来

```
user:vagrant_puppeteer$  vagrant rsync-back 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 script.js 中看到截图创建的图像结果

[![google_top.png](img/f4dd5a00977da51663600d9b6fd1abe6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0XTxSTdj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/192145/7901f54b-dc67-1376-eea7-844d76203036.png)

这是日语页面。
如果不需要日文字体，应修改安装了日文字体的 Dockerfile。它在 Docker 构建中执行。

## 10。修改 script.js

```
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch({
    args: [
      '--no-sandbox',
      '--disable-setuid-sandbox'
    ]
  });
  const page = await browser.newPage();
  await page.goto('https://google.com');
  await page.screenshot({ path: 'google_top.png' });

  browser.close();
})(); 
```

Enter fullscreen mode Exit fullscreen mode

## 随心所欲修改 script.js 和刮！

```
user:vagrant_puppeteer$  vagrant rsync
[root:localhost puppeteer]$docker build ~~~
[root:localhost puppeteer]$docker run ~~~
user:vagrant_puppeteer$  vagrant rsync-back 
```

Enter fullscreen mode Exit fullscreen mode

# 感谢

感谢您的阅读

参考:
[操纵 Docker 容器上的傀儡师](https://qiita.com/QUANON/items/59c468adfff0278f20bb)
[用傀儡师操纵无头铬合金](https://qiita.com/sand/items/b01d7d8f3d9c1642298b)

你可以在下面的 URL (JP)
[库](https://github.com/ikeryo1182/puppeteer_tutorial)查看源码