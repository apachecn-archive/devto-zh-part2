# 在 Laravel 中创建资产管理系统

> 原文：<https://dev.to/kris/-create-asset-management-system-in-laravel-i5k>

### 在 Laravel 5.7 中创建资产管理系统

[![](../Images/bca5ab3ad7818df21e3b4b4a81ca94bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KF26IOBj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZBdnLlNLdFOJk-fgtCnXuQ.png)

#### **旅程赞助商**

[**使用 Laravel 和 Vue.js 进行 Fullstack Web 开发**](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1608944&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Flaravel-vuejs-fullstack-web-development%2F)

了解如何使用 Laravel 5、Laravel Mix、Vue js、Bootstrap 4 & Sass 构建 fullstack web 应用

[**带推送器的实时单页论坛 App&vuejs**](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1587178&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Freal-time-single-page-forum-app-with-pusher-laravel-vuejs%2F)

在单页 App 中用推送器实时做事情

在这个系列中，我们将学习如何创建 CRUD 和一些额外的功能，如报告、日志、许可、在 Laravel 中的本地化，以及使用一些业务逻辑，如资产管理，那么什么是资产管理系统？是用于跟踪资产的应用程序，如计算机、许可软件、物理硬件(如桌子、电视)或消耗品(如油、墨水)在这篇文章中，我解释了我们如何开始结束

这个灵感项目已经从 [**变成了**](https://snipeitapp.com/)

[Home - Snipe-IT 开源 IT 资产管理](https://snipeitapp.com/)

如果你对 laravel 有所了解，从 Github 上一些有很多明星和贡献者的项目中学习，使你的技能快速增长，想象一下你从许多专业开发人员那里免费学习

snipe-它基于 Laravel 5.4 和 bootstrap 3，但是在这个系列中我们使用 5.7 和 Bootstrap 4

因为目前的版本有很多的功能和复杂的结构已经让一个新手

不知所措，所以我决定使用过去两年发布的[版本 3](https://github.com/snipe/snipe-it/releases/tag/v3.0)

但是对于最初的版本，我们把它做成简单的母狗

请记住，我们不会复制粘贴我们需要了解有多少专家开发人员编写代码，并选择一些我们在新手角色中理解的东西

然后，我们使小的不同，从演示只是一个版本，但核心代码相同

说真的，我们要向现实生活中的程序员学习

他们不怎么看 Udemy 或 Tut 网站上的教程，而是一头扎进代码森林，试图理解如何使用调试器来拦截代码流，并观察当跳过函数时变量是如何变化的

**他们从 Github 上的一千名专业开发人员那里学习最佳实践**

### 在第 0 部分的开始，我们为开发准备了环境

我是 [cloud9](http://c9.io) 的超级粉丝，但他们在 2017 年被 AWS 收购，集成到 AWS 服务中，使之成为完整的云开发环境

[https://www.youtube.com/embed/fwFoU_Wb-fU](https://www.youtube.com/embed/fwFoU_Wb-fU)

我们将学习如何使用 Cloud9 和设置必要的工具

[![](../Images/ad60dc8efafbba2b47c200f653316ceb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_p6VLFjO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhcYSTYGnYoCKJTIRPcrJag.png)

**要求**

亚马逊账户和信用卡(如果不使用本地替代)

**你会学到**

*   基于 centos 基本 linux 命令
*   云元素 9
*   如何更改 php 版本

### 第 1 部分我们安装并设置 Laravel 5.7 和 Github 库，并部署到 Heroku

[![](../Images/1e722f667a4520a550d1ecbfcdd09c12.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wr2N5OHy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AFbWUm4N-wsj7sm1T.jpg)

因为在 EC2 上保持项目的实时性会收取一些免费的费用，所以我选择 Heroku 进行实时演示

**你会学到**

*   如何在基于 CentOS 的 AWS linux 上安装 Laravel 5.7 和 Mysql
*   安装 AdminLTE，让您的前端生活更轻松
*   基本 git 命令
*   简单的 Github 存储库
*   如何向 Heroku 部署任何承诺，使您的应用程序保持活跃
*   为您的应用奠定基础
*   还有更多..

### **第二部分我们将创建简单的 CRUD 系列**

#### 类别

[![](../Images/fdd8b24f8cad1a618f32880440377746.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LBfgHLot--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQjl-SZEYV7BMkHURWIyeZA.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何命名路线
*   如何从头开始创建简单的 CRUD

#### 用户

[![](../Images/e1293e3cdad3bf459c0a9bf2f5ff01f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TmCt70F---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Alzxx2hodUQrQ2gFTdmtQYQ.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   如何在 Laravel 中上传文件

#### 状态标签

[![](../Images/3f123765e224219fbee27600dbfbc769.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kC_PIbIw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APPFGnjVjMj33Ezu_mJzf_g.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何创建不涉及数据库表关系
*   如何从头开始创建简单的 CRUD

#### 资产模型

[![](../Images/fe6f87775c2c441ba286f9e3b13797fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eMxFU2ju--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AShKoGP1yX18PUJV_VF1WVw.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jQuery 数据表创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   使用范围创建特定查询
*   如何上传图片
*   如何在 Laravel 上使用关系进行查询

#### 制造商

[![](../Images/8b12de59a49e2fb2886bb04e784c4cd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p_HtjboD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8Hp7iFKx4n6Rxai4x_QIPQ.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   使用范围创建特定查询
*   如何上传图片
*   如何在 Laravel 上使用关系进行查询

#### 供应商

[![](../Images/015b9e28b265d2445b157bbb60b72164.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--frHiEyId--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoCS5JH6WKQnSp9bjiDIpHw.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   使用范围创建特定查询
*   如何上传图片
*   如何在 Laravel 上使用关系进行查询

#### 部门

[![](../Images/033df3906492a3c65316e4141e32618d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bZsntNj5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgBbwmW8TgcPE5sgL3y7euQ.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   使用范围创建特定查询
*   如何上传图片
*   如何在 Laravel 上使用关系进行查询
*   如何使用原始查询

#### 位置

[![](../Images/2d5c4f8a0423fc2c8589628638d9e99f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q4Y41vrw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzCYAQIibnD5T7qZWbF12VQ.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   使用范围创建特定查询
*   如何上传图片
*   如何在 Laravel 上使用关系进行查询
*   如何使用原始查询

#### 公司

[![](../Images/559a37a221dafb8990eb3bb26b51cff8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uL_jZFLI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AD1N-0GqMtbiIhMKXoQZDfg.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   如何上传图片

#### 折旧

[![](../Images/419a8f06976d8502b68999d82fb5ff3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SzemfzaO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AfnYZgUl7SU6SefuYs2En3Q.png)

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD

#### 资产

[![](../Images/15ab1eaa6e17311f3f249fbb4da78fb5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vl3AGLgV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMcQiJOfvtXF4xfK94dljDw.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   使用范围创建特定查询
*   如何上传图片
*   如何在 Laravel 上使用关系进行查询
*   如何签入和签出资产
*   如何向用户发送通知

#### 执照

[![](../Images/1f147abb2f11c5678c6a0b11d6280e3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t2ML5V1G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A4ywvRzwrjCPn88dTYnVmqg.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   使用范围创建特定查询
*   如何上传图片
*   如何在 Laravel 上使用关系进行查询
*   如何登记和注销许可证
*   如何向用户发送通知

#### 配件

[![](../Images/76b81f30546da6dbb46a25e51e0b8d92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_y-Im39v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AlkvYnR-BIbG9sG5oGOwZqg.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   使用范围创建特定查询
*   如何上传图片
*   如何在 Laravel 上使用关系进行查询
*   如何入住和退房
*   如何向用户发送通知

#### 消耗品

[![](../Images/9c17cd626c3ffa5f2a5a3aa223549b40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LftbT6JG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjHaD5f8C0GHdL5uh1Z3SzA.png)

在本节中，您将学习

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   使用范围创建特定查询
*   如何上传图片
*   如何在 Laravel 上使用关系进行查询
*   如何入住和退房
*   如何向用户发送通知

#### 组件

[![](../Images/09001331a319c5790656a86b11d325ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c7eDUUTB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsSKfbKBeCyXOiRIzihZ7WA.png)

#### 在本节中，您将了解到

*   如何用终端创建模型控制器和迁移
*   如何用 jquery 数据库创建富表
*   如何在不接触数据库的情况下创建表关系
*   如何从头开始创建简单的 CRUD
*   使用范围创建特定查询
*   如何上传图片
*   如何在 Laravel 上使用关系进行查询
*   如何入住和退房
*   如何向用户发送通知

### 额外功能

#### 简单报告

[![](../Images/f9c99e86f701c1e6e1004a830084bc51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1CJGynrR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADkGFif4PF4GcI8bkvU4SPQ.png)

这么简单是因为它是你想要的表格中许多数据的汇总

#### 权限管理

如此困难的部分，但拉拉夫处理这个与大门类

[![](../Images/dbc05a39c0864540a4d867e45d801980.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5DremXJW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASFuOQd_gPmwuebSbPTwk3w.png)

我们将通过用户可以执行的一组操作来创建权限管理。

[![](../Images/d120c599ee7d2ff5eff5dc3de1015dfb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BWDcojey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/609/1%2AjzzLLUFju6Pb8g5QL_HAYg.png)

#### 在本节中，您将了解到

*   如何对复杂数据执行 CRUD
*   如何使用 Laravel Gate 类

#### 动作日志

[![](../Images/7d86cf9ca5b39839fbb1eb6823981a8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7hLhW6oT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmUglze6J7ORxJ-tg2zFVEg.png)

在本节中，您将向现有应用程序添加日志

*   他们如何处理日志
*   他们如何使用日志进行审计

#### 本地化

[![](../Images/63d4ec5299da3a61f4df0ae48d34752e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wfT2KX-9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9KdaMPaeuRMJl1ZlG_sl8g.png)

另一个有趣的部分是，我们将使用 Laravel 本地化类将所有现有应用程序的静态消息更新为动态消息

#### 在本节中，您将了解到

*   了解应用程序翻译如何工作？
*   用 Lang 类将静态消息变成动态消息
*   创建设置语言页面

#### 关闭赞助商

#### [终极进阶 Laravel Pro 课程(含 Vuejs)](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1382640&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Fthe-ultimate-advanced-laravel-pro-course-incl-vuejs-2%2F)

[![](../Images/4d4606bae253ac06a555ce761f6b381f.png)T2】](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1382640&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Fthe-ultimate-advanced-laravel-pro-course-incl-vuejs-2%2F)

#### [用 Laravel 建立一个实时在线游戏](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1099490&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Fonline-game-with-laravel%2F)

[![](../Images/9cf71789042257774450a7bd79ca64fb.png)T2】](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1099490&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Fonline-game-with-laravel%2F)

[**了解更多**](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1099490&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Fonline-game-with-laravel%2F')

#### [Laravel 5.7 电子商务商店](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1510962&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Flaravel-ecommerce-shop%2F)

[![](../Images/e4b6ebaeb1da751575b7621997a31723.png)T2】](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1510962&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Flaravel-ecommerce-shop%2F)

[**了解更多。**T3】](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1510962&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Flaravel-ecommerce-shop%2F)

#### [TDD Laravel —让我们建立个人预算网站](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1338132&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Ftdd-laravel-lets-build-personal-budget-website%2F)

[![](../Images/88dddd6e6d5323569ac049d73f958980.png)T2】](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1338132&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Ftdd-laravel-lets-build-personal-budget-website%2F)

[**了解更多**](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1338132&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Ftdd-laravel-lets-build-personal-budget-website%2F) 。

#### 期末备注:

如果你需要和我一起探索海洋，普通海盗可以登上黑珍珠号

[![](../Images/91a527543848d2dda0f17eb8c415ef4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AG1WtlGV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2An6UJsq531VPAJvdf)

也许我们可以找到一些代码宝藏

#### 如果你需要为这次旅行筹集资金

[![](../Images/ec2e70869f8bdec64465cc119705302a.png)T2】](https://www.patreon.com/krissanawat)