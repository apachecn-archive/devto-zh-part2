# 使用虚拟盒 laravel 的家园盒

> 原文：<https://dev.to/aurelkurtula/using-virtual-box-laravels-homestead-box-44f6>

在尝试[分离虚拟盒子、流浪者和苏格兰盒子](https://dev.to/aurelkurtula/setting-up-virtual-box-vagrant-and-scotch-box--4jd8)时，我发现苏格兰盒子对我来说太重了。它提供了比我需要的更多的东西，而且似乎鼓励每个项目使用一个盒子——尽管还有其他方法。

所以我一直在尝试一个不同的盒子，名为 [homestead](https://laravel.com/docs/5.6/homestead) ，由 Laravel 创建。它提供了使用 PHP 的所有要求——这是我真正想要的。

## 开始使用

首先我们需要安装盒子

```
vagrant box add laravel/homestead 
```

Enter fullscreen mode Exit fullscreen mode

我们只会做一次，不管你想要启动多少家园箱

现在转到任何你想安装初始家园文件夹和克隆家园的目录

```
git clone https://github.com/laravel/homestead.git Homestead
cd Homestead
bash init.sh 
```

Enter fullscreen mode Exit fullscreen mode

最后一行将在`Homestead`文件夹中创建一个`Homestead.yaml`配置文件。这是您设置所有项目的地方。在`Homestead.yaml`内部，我们有这样的东西

```
--------
ip: "192.168.10.10"
memory: 2048
cpus: 1
provider: virtualbox

authorize: ~/.ssh/id_rsa.pub

keys:
    - ~/.ssh/id_rsa

folders:
    - map: ~/code
      to: /home/vagrant/code

sites:
    - map: homestead.test
      to: /home/vagrant/code/public

databases:
    - homestead 
```

Enter fullscreen mode Exit fullscreen mode

要快速启动并运行，您唯一需要编辑的是`folders`和`sites`属性。上面的设置是默认的，但是它们不太可能适合你。

`folders`属性需要指向您想在 homestead 框中运行的 php 项目文件夹。然后，`sites`属性是您为项目指定域名的地方！

**项目文件夹完全独立于家园配置文件夹**。(与苏格兰盒子相比，这是我喜欢的东西([之前已经介绍过](https://dev.to/aurelkurtula/setting-up-virtual-box-vagrant-and-scotch-box--4jd8)))

出于演示的目的，让我们假设我们的项目在`~/MyWORK/test-projects`中。属性将反映出

```
folders:
    - map: ~/MyWORK/test-projects
      to: /home/vagrant/code 
```

Enter fullscreen mode Exit fullscreen mode

`Map`指向本机的目录，`to`指向虚拟机的目录。通过映射这两者，我们确保了它们保持同步。所以最终当我们从虚拟机上运行一个网站时，它的文件反映了我们在`~/MyWORK/test-projects`的文件

然后，`sites`属性将虚拟机中的一个目录映射到一个自定义域

```
sites:
    - map: site1.test
      to: /home/vagrant/code/site1 
```

Enter fullscreen mode Exit fullscreen mode

同样,`/home/vagrant/code/`已经从我们的机器映射到一个目录，因此`~/MyWORK/test-projects`中的所有东西都存在于`/home/vagrant/code/`中，这意味着`/site1`存在于`~/MyWORK/test-projects/sites`中——我们实际编写代码的地方。

## 编辑主机文件

正如在`Homestead.yaml`中提到的，这个虚拟机器的 IP 地址是`192.168.10.10`，因此，在我们的主机文件(MAC 上的`/etc/hosts`和 windows 上的`C:\Windows\System32\drivers\etc\hosts`)中，我们需要添加这一行:

```
192.168.10.10  site1.test 
```

Enter fullscreen mode Exit fullscreen mode

因此，当我们在浏览器中访问`site1.test`时，它将触发 homestead，反过来`Homestead.yaml`将服务于位于`/home/vagrant/code/site1`的 web 项目

## 奔跑的流浪者

现在一切都设置好了，从终端，用`Homestead`目录我们需要开始流浪

```
vagrant up 
```

Enter fullscreen mode Exit fullscreen mode

像往常一样，第一次运行它将需要一些时间，因为它需要安装盒子。但每隔一段时间，它会很快开始和停止

## 使用数据库

homestead box 附带了已经设置好的 MySQL 和 PostgreSQL，它们在 homestead 流浪者 box 启动时运行。

你可以从`127.0.0.1`和端口`33060` (MySQL)或者`54320` (PostgreSQL)连接到那些。两个数据库的用户名和密码都是`homestead` / `secret`。

## 结论

这个盒子比苏格兰盒子简单多了。我喜欢它鼓励我们对所有的 PHP 项目使用一个盒子——这正是我想要的。

我想知道的是，映射在空间方面是如何工作的，这些项目是克隆的还是被引用的？