# 如果 TDD 是 Zen，那么添加无服务器带来了涅槃——第 2 部分

> 原文：<https://dev.to/ledfusion/if-tdd-is-zen-adding-serverless-brings-nirvana---part-2-f0>

# 如果 TDD 是 Zen，那么添加无服务器带来了涅槃(第 2 部分)

如果你是我关于 [TDD](https://en.wikipedia.org/wiki/Test-driven_development) + [无服务器](https://serverless.com/)架构的文章的快乐读者之一，请继续阅读，因为今天我们将使用 mongoose、连接池和监控来改进我们的无服务器工具包。

如果您还没有，我强烈建议您先阅读第 1 部分，一旦您满意了，就回来。

[![ledfusion image](../Images/5da4901e4d20c721fe1b55ef53f0c951.png)](/ledfusion) [## 如果 TDD 是 Zen，那么添加无服务器带来了涅槃

### Jordi Moraleda

#serverless#aws#lambda#tdd](/ledfusion/if-tdd-is-zen-adding-serverless-brings-nirvana-545c)

因此，如果第 1 部分对您来说还可以，但还需要更多，那么让我们看看如何让我们的 DB 执行得更快更快。

## 数据库模型

如果我们希望我们的代码易于维护，lambda 函数和 TDD 就是我们的朋友。但是在处理数据库时，我们显然可以更好地为我们的后端记录和执行数据库模式。

NPM 包是实现数据库调用的最轻的方式。然而，我们可能要考虑使用像[mongose](http://mongoosejs.com/)这样的 ODM，因为我们得到的好处是值得牺牲一点点性能的。我们将在几分钟后调整性能。

所以，从第 1 集的[代码开始，让我们安装 Mongoose 并开始:](https://github.com/ledfusion/serverless-tdd-starter/tree/part-1)

```
npm uninstall mongodb
npm install mongoose 
```

准备好了。现在我们可以定义我们的第一个模型了。

作为一个加入项目的团队成员，如果我第一次签出代码，我肯定会去 models 文件夹。因此，让我们创建文件夹，并在`models/user.js`中为用户添加一个模型。