# NodeJS 中的 Web UI 测试

> 原文：<https://dev.to/ykyuen/web-ui-testing-in-nodejs--kda>

*最初发布在[水手长博客](https://blog.boatswain.io/post/web-ui-testing-in-nodejs/)上。*

* * *

[![](img/620dc5d5c09d98d08f3e86deb4cd7ef5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kW-dxv3V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xxoygn37j7vvt3aqijp7.png)

在这篇文章中，我将展示一些用于测试 web UI 的工具。它们是:

*   [摩卡](https://mochajs.org/)
*   [柴](http://chaijs.com/)
*   [卡斯珀吉斯](http://casperjs.org/)
*   [幻象](http://phantomjs.org/)

#### 摩卡

[Mocha](https://mochajs.org/) 是一个用于测试的 Javascript 框架。它有一个 *BDD 风格的*语法 **(BDD: [行为驱动开发](https://en.wikipedia.org/wiki/Behavior-driven_development) )** ，并允许异步调用测试。它还支持不同的断言库，在我们的例子中，我们将使用 [chai](http://chaijs.com/) 进行断言。

#### 柴

[Chai](http://chaijs.com/) 是一个用于 [NodeJS](http://nodejs.org/) 和浏览器的 [BDD](https://en.wikipedia.org/wiki/Behavior-driven_development) / [TDD](https://en.wikipedia.org/wiki/Test-driven_development) 断言库，可以愉快地与任何 Javascript 测试框架配对。

#### CasperJS

[CasperJS](http://casperjs.org/) 是一个构建导航场景的助手库。它经常与 [PhantomJS](http://phantomjs.org/) 一起使用，但实际上它也支持[slimmerjs](https://slimerjs.org/)，这是另一个带有 [Firefox](https://www.mozilla.org/) 渲染引擎的无头浏览器，名为 [Gecko](http://geckoisgecko.org/) 。

#### 幻像

[PhantomJS](http://phantomjs.org/) 是一个带有 [WebKit](https://webkit.org/) 渲染引擎的无头浏览器。它允许在一个无头系统中运行基于浏览器的测试。

## 检查谷歌搜索是否正常

让我们从一个简单的例子开始。假设我们想要测试以下内容:

*   谷歌搜索页面可以访问吗？
*   搜索功能能够返回结果列表吗？

对于第一个问题，我们可以简单地向 Google URL 发出 HTTP 请求，看看它是否返回 HTTP 200 响应代码，这可以通过 [CasperJS](http://casperjs.org/) 轻松完成。

第二个问题有点复杂。它可以分解成以下几个步骤。

1.  *等待搜索表单*
2.  *填写表格并提交*
3.  *检查结果集是否包含搜索文本*

让我们看一下代码。

### 初始化新的 NodeJS 项目

```
npm init 
```

Enter fullscreen mode Exit fullscreen mode

### 安装以下节点模块

```
npm install casperjs chai mocha phantomjs --save-dev
# In addition to the above libraries, we also need the following extensions.
npm install casper-chai mocha-casperjs --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

### 设置测试

创建*测试/google-search.js*

```
describe('Google Search', function() {
  // Before script for each test
  before(function() {
    casper.start('https://www.google.com.hk/');
  });

  // *** Test 1 ***
  // Is the Google search page accessible?
  it('should have return HTTP 200', function() {
    expect(casper.currentHTTPStatus).to.equal(200);
  });

  // *** Test 2 ***
  // Is the search function able to return a list of result?
  it('should be able to search', function() {
    // Wait for the search form
    casper.waitForSelector('form[action="/search"]', function() {
      'form[action="/search"]'.should.be.inDOM;
    });

    // Fill in the form and submit
    casper.then(function() {
      this.fill('form[action="/search"]', { q: 'Boatswain' }, true);
    });

    // Check if the result set contains text "Boatswain"
    casper.waitForSelector('h3.r a', function() {
      'h3.r a'.should.be.inDOM;
      expect('h3.r a').to.contain.text(/Boatswain/);
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 添加运行测试的 npm 脚本

编辑 *package.json* 如下。

```
{  "name":  "ui-test",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "mocha-casperjs test/google-search.js"  },  "author":  "ykyuen",  "license":  "ISC",  "devDependencies":  {  "casper-chai":  "^0.3.0",  "casperjs":  "^1.1.4",  "chai":  "^4.1.2",  "mocha":  "^4.1.0",  "mocha-casperjs":  "^0.6.0",  "phantomjs":  "^2.1.7"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 运行测试

```
npm test 
```

Enter fullscreen mode Exit fullscreen mode

[![Test passed!](img/a1eaf14b2d5817934ee9aa393e6e723a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UaUKk6Zf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f776ftbakdfvj44pvdfp.jpg)

<center>Test passed! ✅</center>

测试运行结束后，将显示一份简短的测试报告。

### 让我们试着不要通过测试

```
// Check if the result set contains text "Boatswain"
casper.waitForSelector('h3.r a', function() {
  'h3.r a'.should.be.inDOM;
  expect('h3.r a').to.contain.text(/nosuchtext/);
}); 
```

Enter fullscreen mode Exit fullscreen mode

[![Test failed!](img/1e54c9815420761746a5477b5a7d5efb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LLs7zkpF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h82xqkcjhsk2touxgrna.jpg)

<center>Test failed! ❌</center>

## 总结

这个例子展示了如何在 [NodeJS](http://nodejs.org/) 中创建一个简单的 web UI 测试，并在命令行提示符下执行测试。它可以用于舞台环境的[烟雾测试](https://en.wikipedia.org/wiki/Smoke_testing_(software))。还请注意， [CasperJS](http://casperjs.org/) 是**不是**用于单元测试，而是 web UI 测试。另外，像 [Karma](https://karma-runner.github.io) 这样的测试运行器不支持 [CasperJS](http://casperjs.org/) 。如果你正在寻找单元测试解决方案，你可能应该排除 [CasperJS](http://casperjs.org/) 。

完整示例见[gitlab.com](https://gitlab.com/ykyuen/glide-example)。