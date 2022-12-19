# PhpUnitGen，简化你的单元测试的编写！

> 原文：<https://dev.to/paulthebaud/phpunitgen-simplify-the-writing-of-your-unit-tests--4nec>

## 这篇文章过时了。看看新的:

[![paulthebaud image](../Images/ab5d89f5296fb0ed5cda506c0a99e36a.png)](/paulthebaud) [## PhpUnitGen，测试，新方法！

### 保罗·泰博

#showdev #php #testing #opensource](/paulthebaud/phpunitgen-testing-the-new-way-3jf)

* * *

你是非常排外的 PHP 开发人员俱乐部的一员，你不断地复制/粘贴来满足 T2 的 PHPUnit？
单元测试甚至在你的噩梦中也萦绕着你？

PhpUnitGen 旨在让您的睡衣和应用程序得到更好的测试！

[![](../Images/b964594a8a87c2f627409d3f3505d6ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I2t-hF80--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.github.com/paul-thebaud/phpunit-generator-assets/master/logos/logo.svg%3Fsanitize%3Dtrue)

**PhpUnitGen** 是一个**在线**或**终端**开发工具，用于为 *PHP* 代码生成你的单元测试框架。

## 为什么是 PhpUnitGen？

和其他人一样，我必须为了吃饭而工作，为了工作而吃饭(即使发展是一种激情，哼)。我已经尝到了循环的滋味，我的工作是测试一个 PHP 应用程序。有什么比为无数个 PHP 文件重新创建另一个测试框架更无聊和无趣的呢？可能是一级方程式比赛...

从这个假设出发，我发现自己想要“祝贺”这个天才，它会自动生成我所有的单元测试框架，从而节省我很多时间。

我谦虚地创造了这个工具。
**PhpUnitGen** 诞生了。

然后我告诉自己，与其在网飞面前浪费时间，不如好好利用这段时间，于是我在**开源**上发布了这个工具。

## PhpUnitGen 是什么？

**PhpUnitGen** 是一个开发工具，用 *PHP7.1* 编程，允许从 *PHP* 源代码生成单元测试框架。

生成的测试用 *PHP7.1* 编写，并与 *PHPUnit 6/7* 一起工作。

下面是 PhpUnitGen 的一些特性:

*   它自动检测*类*、*特征*或其他格式，并适当地实例化它们。
*   它允许**为 *getter* / *setter* 方法自动生成**测试。
*   它提出了许多 *PHP* 注释，允许创建简单的测试、*模仿*或*类的实例化*。

## 怎么用？

使用 **PhpUnitGen** 有两种方法。

通过 web 应用(**开源** ): [phpunitgen.io](https://phpunitgen.io) 。

通过 *Composer* 包与**终端**(也是开源的):[paultebaud/phpunit-generator](https://github.com/paul-thebaud/phpunit-generator)。

## 如何帮助提升 PhpUnitGen？

你可以通过 [Github issues](https://github.com/paul-thebaud/phpunit-generator/issues) 发送你所有的评论/错误来帮助改进 PhpUnitGen。

也可以分享这篇文章的链接，包或者 web 应用！

**感谢您的阅读，再见！**