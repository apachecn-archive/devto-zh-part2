# php -如何修复“找不到类”错误

> 原文：<https://dev.to/dechamp/php---how-to-fix-class--not-found-error-1gp9>

## 问题

运行我的代码时，我得到一个“找不到类”。我使用 composer 自动加载来加载我的类。

## 解(但愿😜)

因此，你可以做一些事情来解决 PHP 中的“找不到类”问题。

通常，在名称空间中，它只是一个错别字或一个应该小写的大写字母，反之亦然。

所以我是这样做的。

### 第 0 步(感谢 Kushal Niroula！见下面他的评论)

正如 Kushal Niroula 在下面提到的，你应该总是检查你是否在顶层脚本中导入了这个类，所以首先要这么做！

`use Your/Class/Here;`

### 第一步

检查 composer.json 文件，即自动加载路径。这是自动加载器将与之匹配的内容，因此请确认它所指向的基本文件夹是正确的。

例如，如果我们将 psr-4 设置为`"DeChamp\\MyApp\\": "src/"`，这意味着如果我在“src”文件夹中有一个名为“Service”的文件夹，那么名称空间将是“DeChamp\MyApp\Service”。

检查命名空间路径是否与文件路径匹配。

*   命名空间 DeChamp \ MyApp \ Service//在文件 dummy.php 内
*   src/Service/dummy.php

### 第二步

检查文件顶部的命名空间和文件夹目录是否匹配。此外，没有错别字和路径匹配大小写，都是大写和小写。

很多次我都遇到了漏写的问题。

**注意:**有些系统是不区分大小写的，所以这可能不适用，但仍然应该实践。我曾经有一段时间本地开发不匹配，所以它在我的机器上工作，但后来生产抱怨。

#### 错了

*   `namespace DeChamp\MyApp\Service; // within file dummy.php`
*   `src/service/dummy.php //service is lower when the first character should be capitalized`

#### 对

*   `namespace DeChamp\MyApp\Service; // within file dummy.php`
*   `src/Service/dummy.php` //文件夹名称与名称空间完全匹配

### 第三步

如果你已经确认一切都是正确的，而你却在用头撞墙，那么这可能就是问题所在。

如果您在上次运行 composer install 后更新了您的 composer 文件，它可能已经过时。Composer 使用缓存文件来提高速度和性能。

要强制自动加载文件重新生成，只需运行下面的命令。

`composer dumpautoload`

如果您确实知道所有其他项目都是正确的，这应该可以解决问题。

## 反馈

我说错什么了吗？有问题/建议吗？随时反馈或赞美😁

* * *

瓦里马德有限责任公司。

目前的项目有[https://charactergenerator4000.com](https://charactergenerator4000.com)和 [https://coder.exchange](https://coder.exchange) 。请检查它们，并让我们知道你的想法。