# 在 Nodejs 应用程序中设置与 Travis CI 的持续集成

> 原文：<https://dev.to/lauragift21/setup-continuous-integration-with-travis-ci-in-your-nodejs-app-26i2>

[![travis logo](../Images/4f32055aba9d55d0d6ea1c6dd250cafe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o25mzxVZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/lauragift/image/upload/v1529656180/logo-travisci-wide1_lcjhdf.jpg)

这篇文章将详细解释如何在 nodejs 项目中设置 Travis CI 部署。

**什么是持续集成？**

[持续集成](https://en.wikipedia.org/wiki/Continuous_integration)是在小代码变更中频繁合并的持续实践——而不是在开发周期结束时在大变更中合并。目标是通过以较小的增量开发和测试来构建更健康的软件。这就是特拉维斯. CI 的用武之地。

### 项目设置

为了测试 Travis CI 的工作方式，我们需要设置一个带有测试的节点项目。
确保你已经安装了节点和 NPM`node -v`和`npm -v`来检查版本。

```
# start up your project

mkdir travis-ci
cd travis-ci
npm init

# install the dependencies required for this project
npm install express mocha chai 
```

Enter fullscreen mode Exit fullscreen mode

用下面的代码在根目录下创建一个 *index.js* 文件。

```
# index.js

const express = require('express');

const app = express();

app.get('/', (req, res) => {
  res.send('ci with travis');
});

const server = app.listen(3000, () => {
  console.log('App running on port 3000');
});

module.exports = server; 
```

Enter fullscreen mode Exit fullscreen mode

还要创建一个测试文件夹和一个 **index-spec.js** 文件来测试我们的节点应用程序。

```
# index.spec.js

const expect = require('chai').expect
const server = require('../index');

describe('test', () => {
  it('should return a string', () => {
    expect('ci with travis').to.equal('ci with travis');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

[![test](../Images/61f2227a61fc9209cb4a240b17a8bf13.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HpeEzWoU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/lauragift/image/upload/v1529662546/Screenshot_from_2018-06-22_11-11-40_nyow0d.png)

**NPM 脚本**

通过运行这些脚本来确保应用和测试正常工作。

```
 "scripts": {
  "start": "node index.js",
  "test": "mocha"
} 
```

Enter fullscreen mode Exit fullscreen mode

**特拉维斯设置**

在根目录下创建一个文件 **.travis.yml** 。

```
 language: node_js
node_js: 
  - "stable"
cache:
  directories:
    - "node_modules" 
```

Enter fullscreen mode Exit fullscreen mode

上面指出的. travis.yml 文件是指导 travis 构建什么的文件。语言选项可以是应用程序运行时使用的任何语言，而“node _ js”:“stable”表示 Travis 应该使用 node 的稳定版本。您还可以在 Travis 上缓存 node_modules 目录，以避免在每次触发构建时安装所有依赖项，而是更新具有较新版本的包。添加到该文件的更多选项可以在[这里](https://docs.travis-ci.com/user/getting-started/)找到。

### *将 Travis 与 GitHub 整合*

Travis 是一项 CI 服务，这意味着它是一个自动化的流程。Travis ad GitHub 的典型工作流是这样的:

**提交被推送到 GitHub*

** Travis build 被触发，它检查测试是通过还是失败。*

[![travis ui](../Images/310bcbef20bc7423a8ae799f498c3707.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QSXUO7Ja--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/lauragift/image/upload/v1529664742/Screenshot_from_2018-06-22_11-49-48_abjiek.png)

#### 特拉维斯构建设置

**创建一个 GitHub repo，将项目文件夹推送到 GitHub。*

**将回购添加至 [Travis 网站](https://travis-ci.org/)*

**进行更改并提交至回购。您应该会自动看到您的构建过程正在运行。*

**将 Travis 徽章添加到 GitHub repo 中的 README.md 文件中。*

**结论**
Travis CI 让开发软件变得高效。它确保您部署遵循良好实践的干净代码，并且还检测您的代码中是否存在由项目中的更改或重构导致的可能的错误或不足。

所有代码都可以在 [Github repo](https://github.com/lauragift21/travis-blog) 上获得。

另外，如果你有问题或想法，请随时发表评论。

最初发布在[我的博客](https://www.giftegwuenu.com/setup-continuous-integration-with-travis-ci-in-your-nodejs-app/)