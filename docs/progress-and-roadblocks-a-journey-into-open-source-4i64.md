# 进步与障碍:开源之旅

> 原文：<https://dev.to/hugo__df/progress-and-roadblocks-a-journey-into-open-source-4i64>

> 在 Sequelize CLI 中增强 ES6 的生成器

这是我发现带有序列的 ES6 类样式模型定义的故事(参见[使用 ES6 类用于序列 4 模型](https://dev.to/hugo__df/using-es6-classes-for-sequelize-4-models-17ga))。认识到当前的 Sequelize CLI 模型生成器不支持它(这是意料之中的，因为这不是最常见的方式)。最后，我自己实现它的动力。

换句话说，我开始使用 Sequelize CLI 不支持的 Sequelize 模型风格。在一个“特性请求”问题上我没有得到答案，所以我只是分叉它，实现它并发布它。

自由开源软件运动有它的起伏，它的宣传和被放弃的项目。它的一个很棒的方面是，你总是可以(取决于许可证)直接派生一些东西并发布它🙂。

一些链接:

*   [测序-cli fork repo](https://github.com/HugoDF/sequelize-cli)
*   [在 npm 上发布的分叉](https://www.npmjs.com/package/@hugodf/sequelize-cli)

实时推特风暴:

*   [第一天(挣扎)](https://twitter.com/hugo__df/status/1057294226755592192)
*   [第二天(修复)](https://twitter.com/hugo__df/status/1057671284920848385)
*   [第 3 天(首次发布)](https://twitter.com/hugo__df/status/1057986197165879298)
*   [第 4 天(收尾)](https://twitter.com/hugo__df/status/1058284191673188354)

目录:

*   [处理 GitHub 分叉库🍴](#dealing-with-a-github-fork-repository)
*   [遵循设置说明📐](#following-setup-instructions)
*   [我的目标🛒](#my-goals)
*   [通过测试](#getting-tests-to-pass)
*   [发布更新🚀](#releasing-the-updates)
    *   [尝试向上游返回](#trying-to-contribute-back-upstream)
    *   [将 fork 发布为 npm 范围的模块](#publishing-a-fork-as-an-npm-scoped-module)
*   [关于开源运动的教训📖](#lessons-about-the-open-source-movement)
    *   [有人拿工资会犯错，免费工作的人也会犯(类似的)错误](#people-are-paid-to-make-mistakes-people-who-work-for-free-will-also-make-similar-mistakes)
    *   [没有人欠你*任何东西*T3】](#no-one-owes-you-anything)
    *   [尽你所能](#do-what-you-can)

## 处理一个 GitHub 分叉库🍴

1.  使用 GitHub UI 派生
2.  克隆**你的叉子**T0】
    *   例如`git clone git@github.com:HugoDF/sequelize-cli.git`
3.  创建上游远程
    1.  `git remote add upstream git remote add upstream https://github.com/sequelize/cli`
4.  (为了与上游同步)获取上游远程并将其合并到本地分支(来自 [GitHub 帮助同步一个分叉](https://help.github.com/articles/syncing-a-fork/)
    1.  从上游获取最新信息`git fetch upstream`
    2.  合并变更`git merge upstream/master`
    3.  将上游更改发送到分支`git push`

## 遵循设置指令📐

建立一个项目可能会令人生畏，幸运的是 Sequelize CLI 在`CONTRIBUTING.md`中有一些步骤，它们是:

1.  `npm install`
    *   在 Node 10 上不工作，与 node-gyp 和 SQLite 有关
2.  `npm test`
    *   需要 10-15 分钟
    *   最近一次失败`master`

## 我的目标🛒

我想:

1.  更新`sequelize model:create`以支持第二个模板，它使用 ES6 类模型👍当传递了一个`--class`标志时，任务如下
    1.  在 [model_generate.js](https://github.com/HugoDF/sequelize-cli/blob/master/src/commands/model_generate.js#L10-L24) 的某个地方添加对`--class`标志的支持
    2.  检查 [model_helper.js](https://github.com/HugoDF/sequelize-cli/blob/master/src/helpers/model-helper.js#L45-L51) `generateFileContent`中的`args.class`，并据此切换模板
    3.  创建一个`class-model.js`文件，它是一个类似于[src/assets/models/model . js](https://github.com/HugoDF/sequelize-cli/blob/master/src/assets/models/model.js)的模板，但是使用了 Sequelize 模型的 ES6 `class`语法
    4.  当`args.class`置位时，使用`class-model.js`代替`model_helper#generateFileContent`中的`model.js`(即。`true`
2.  更新迁移生成器(这些完全是随意的选择，但是我发现在序列化项目中想要做的事情):
    1.  对函数使用对象速记符号，而不是箭头函数
    2.  *(最后没这么做，因为太琐碎太吹毛求疵)*使用`(sequelize, DataTypes) -> Promise`函数签名，即。将`queryInterface`和`Sequelize`分别重命名为`sequelize`和`DataTypes`
3.  ***(不行)*** 将`model/index.js`更新为一种更明确、更符合我在生产中使用的形式
    *   为什么？这与模板的当前行为不兼容，你会丢失`models`文件夹中所有模型的自动加载。

## 通过测试🚫

1.  本地失败…
2.  为 https://github.com/sequelize/cli 工作

为什么？

> 2.1/尝试侵入 Sequelize CLI 的第二天
> 
> 我得到了绿色测试…原来`master`是一个假的✅通过建设。
> 
> 它从 npm 加载最新的`sequelize-cli`，soooo 没有在 master 中测试代码。
> 
> 硬重置到最新发布的 npm 版本修复了测试👍
> 
> —雨果·迪·弗朗切斯科([@雨果 _ _ df](https://dev.to/hugo__df))[2018 年 10 月 31 日](https://twitter.com/hugo__df/status/1057671284920848385?ref_src=twsrc%5Etfw)

Sequelize CLI 持续集成没有按照您预期的方式设置:

*   您所期望的:签出提交的代码并测试它
*   它做什么:从 npm 获取`sequelize-cli`,并测试它……所以它测试的是最新发布的版本，而不是回购中的最新代码。

测试还需要一段时间⏳

*   它们大部分是集成测试:写文件，写数据库，并且不得不经常安装/拆卸
*   在我的机器上，一系列测试在 2-3 秒内运行，总时间约为 10-15 分钟

## 发布更新🚀

### 试图逆流而上

2018 年 8 月 1 日创建的一个 [GitHub 问题好像丢了(连个“这不是我们想做的事”都没有)。](https://github.com/sequelize/cli/issues/678)

这不是最好的体验，但对我来说完全没问题，毕竟 OSS 维护者不欠我们任何东西。

这可能是一个小众用例，大多数人似乎乐于以`module.exports = () => { /* do some stuff in the closure */ return };`的方式编写模型，尽管看看阅读[“为 Sequelize 4 模型使用 ES6 类”](https://dev.to/hugo__df/using-es6-classes-for-sequelize-4-models-17ga)的人数，这是一种使用 Sequelize 编写应用程序的有效方式:

![](img/32f7f7c95a410228070d035658e32e90.png)

我创建了一个公关，但我不希望它被合并:

*   ES6 类 PR on [sequelize/cli repo](https://github.com/sequelize/cli/pull/709)

### 将 fork 发布为 npm 范围的模块

我一直认为这更像是一个利基/变体类型的东西，所以把它作为一个分支发布是有意义的

更新`package.json`来拥有，`publishConfig`是非常重要的，如果你正在发布一个作用域模块

```
{
  "name": "@hugodf/sequelize-cli",
  "publishConfig": {
    "access": "public"
  }
} 
```

使用`np`🙂：

1.  `npx np`并浏览对话框
2.  由于测试需要 10-15 分钟来运行，我发现使用`--yolo`选项很有用

[![](img/bb15f76cd8c630d702c11ff5000711cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g-AYekXL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_3F14BC7EA71B60938252453DA3775006F6FE61D96309C1403D86743C7DA41F76_1541151247857_image.png)

## 关于开源运动的教训📖

### 有人拿工资就犯错误，免费工作的人也会犯(类似的)错误

就像在日常工作中一样，对代码中发生的“有趣的事情”感同身受是很重要的，例如，对代码以外的东西进行测试。

如果说维护者已经免费创建了这个项目，那么在他们的业余时间，期待代码库中有更多有趣的东西。

技术债务是开源项目已经达到某种成熟水平的标志。

记住你的情况如下:

1.  意识到项目的存在
2.  使用项目
3.  找到一个项目不太适合你的需求的用例
4.  这个用例可能不是这个项目想要解决的事情
5.  尝试为项目做出贡献

你处在一个非常特殊的情况下，项目已经达到了一定的临界质量/意识/规模。为了达到这一点，就像一家试图扩大规模的初创公司一样，维护人员不得不相对快速地行动，凭直觉行事，可能会在某些地方进行一些“黑客攻击”。

### 没有人欠你*任何东西*

正如我提到的，我打开了一个问题和一个公关，我不认为他们中的任何一个会得到回应。

这就是为什么我将 fork 作为一个模块发布(作用域模块对于这种用例来说是很棒的👍npm 团队)。

### 量力而行

点滴帮助:

*   提到一些在设置过程中难住你的事情(例如，大师是一个假的绿色✅版本)
*   文档更新
*   开放随机 PRs，具有超级固执己见的新功能🙄
*   写博客记录有趣的图书馆用法

每个人都想帮忙。

[恳求自己的塞尔曼](https://unsplash.com/@docrowdee?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)