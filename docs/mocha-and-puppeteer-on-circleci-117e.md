# 在 circleCI 上使用 mocha 和 puppeteer 进行自动化测试

> 原文：<https://dev.to/entrptaher/mocha-and-puppeteer-on-circleci-117e>

[![](../Images/6d0eeec9909e12c80b41ca7599733170.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FvNFdvDn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/jDIthmt.png)

# 预取

我总是很难理解为什么代码质量很重要，我会拒绝在这上面花时间。但是最近我发现自己因为质量问题一次又一次地陷入困境。如果质量很好，那么我就不必花费百万秒来思考问题和结构。于是我学会了摩卡、柴、布袋戏之类的浏览器自动化库。但是很快我在 CI 和代码质量检查工具上遇到了问题。

我费了很大的劲才想出如何将所有这些新学到的知识一次整合到项目中。

这就像不写第一行就试图构建一个完整的应用程序。

当我在其他 repos 上看到漂亮美味的徽章时，我会感到空虚，一些 repos 有多个绿色的徽章，而我的 repos 几乎是空的，代码没有记录在案，等等新手的东西。

然后我突然想到，我可以从我想要的一切开始一步一步地创建一个样本回购，并将我的知识应用于非常基础的层面，这样我就可以在其他项目中重复使用相同的过程，并随着时间的推移缓解我的压力。这个回购就是这样诞生的。

结果很棒，我也像其他人一样添加了一些很酷的徽章，

[![](../Images/95020d3022c51dd3f850c60c728e482d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8otoqLye--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/Av2riR1.png)

啊！当有人告诉你质量很好时的喜悦(只是为了内心的平静)。

# 这是什么

一个样例 repo 展示了如何使用 mocha、puppeteer 和 ci 来自动化各种测试。也许已经创建了一个自动化脚本，但是根据项目的大小，即使是这个脚本也应该被测试，以确保当有一个提交被推送时，一切都按预期工作。

我们可以使用 BDD、TDD 或任何其他方法来构建它，但是无论使用什么方法，步骤总是包括:

*   创建实际/自动化脚本
*   创建测试/mocha 脚本
*   创建/准备 CI 环境

现在大多数 CI 和服务器总是准备 Xvfb 和其他需要的库，所以除非明确需要，否则我们不必过多地处理它们。

注意:这篇自述/帖子将避免深入讨论，只讨论顶部的问题，假设你已经了解 nodejs、单元测试、mocha/chai、puppeteer 和 CI。你不必知道所有的事情，只要知道基本的就够了。

PS: [回购到了](https://github.com/entrptaher/mocha-ci-test)。

# 为什么要考试？

是的，为什么我们只是制作简单的自动化脚本？现在我们只测试一个简单的功能。好像不多。然而，想象一下 100 个脚本或 1000 个脚本。你有可能运行并测试每一个脚本以确保它能正常工作吗？那就不太可能了，一旦出现那种情况就会变成非常愚蠢的头疼事。所以，即使你不需要这些测试，一旦你不得不一遍又一遍地做同样的事情，它们也会长期帮助你。

如果你正在构建一个 scraper，并且必须浏览 100 个网站或填写 100 个表格，当你知道脚本工作并且可以到达目标网站/表格时，它会有所帮助。当事情变糟时，它也会提醒你。

# 基本示例脚本

我们将创建一个样本脚本，将浏览示例网站，提取标题和 outerHTML 并关闭自己。我们将把这个脚本视为一个独立的脚本，所以我们不会在测试环境中处理关闭/打开浏览器的问题，相反，它只是在那里展示它是如何工作的。

## 创建实际脚本

我们将使用具有异步等待支持的 node 8，并为此使用木偶师。

基本上我们要做的是，

*   启动浏览器。
*   导航到网站
*   为测试目的收集标题和外部 HTML
*   关闭浏览器并清理进程
*   返回数据

```
// app/grab-data.js

const puppeteer = require('puppeteer');

const grabData = async () => {
  const browser = await puppeteer.launch({
    // will greatly affect the results
    headless: true,
    // important for running on various server where root user is present
    args: ['--no-sandbox', '--disable-setuid-sandbox'],
  });

  // actual navigation happens here
  const page = await browser.newPage();
  await page.goto('https://example.com');

  // sample data collection
  const title = await page.title();
  const outerHTML = await page.evaluate(() => document.querySelector('body').outerHTML);
  // cleanup once done
  await browser.close();
  return { title, outerHTML };
};

module.exports = grabData; 
```

Enter fullscreen mode Exit fullscreen mode

## 创建测试脚本

当使用单元测试时，使用超时很重要。确保退出测试，并使用适当的超时。最近的 mocha 不会退出测试，在这种情况下，或者在你有无限超时的情况下，你可能会以测试停滞的巨大代价而告终。默认情况下，Mocha 将一个接一个地运行测试。如果你想尝试异步测试，你可以使用艾娃。两者都各有千秋，广受欢迎，拥有巨大的支持。

您不需要测试自动化脚本中的每一件事情。只要确保脚本在每个可能的场景中都能在最基础的层面上工作。如果您推送 100 次提交，并且自动化脚本具有登录等功能，它将在很短的时间内完成 100 次登录，并触发各种警报，除非您测试的是您的网站。

我们将根据实际的用户流来创建测试脚本。检查`test/basic.js`中的测试代码。即使没有注释，代码也是可读的，(另外，我在代码中添加了太多内容，只是为了消除所有的 eslint 错误。)

```
// test/basic.js

const { expect } = require('chai');
const grabData = require('../app/grab-data');

describe('tests example website', function testExample() {
  this.timeout(30000);

  it('browses the website', async function testBrowser() {
    const { title, outerHTML } = await grabData();
    this.title = title;
    this.outerHTML = outerHTML;
  });

  it('title has text: example', async function testTitle() {
    expect(this.title.toLowerCase()).to.have.string('example');
  });

  it('outerHTML has text: example', async function testOuterHTML() {
    expect(this.outerHTML.toLowerCase()).to.have.string('example');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

[![Mocha test](../Images/ccd596deb1e4691eed2cdc957fcd6b74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MT82rvKT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/entrptaher/mocha-ci-test/master/screenshots/mocha-test.png)

## 代码质量

### 林挺

有时，您希望确保工作质量良好，并且不会将垃圾不一致的代码推送到您的 repo 中。如果回购有多个贡献者，你甚至可以确保他们的代码通过所有林挺测试(不包括所有 eslint-disable hacks)，并安全返回。

因此，我们只需使用`eslint --init`，按照向导安装 airbnb 规则，然后使用`eslint .`运行，

[![](../Images/f8130dc7f6f9b14209a42a66dfefd4c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tL4pRRZP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/19NZaVZ.png)

...

opps！然后`eslint --fix . --ext .js`自动修复基本问题。

我们也可以将这些包含在`package.json`内的脚本中，稍后简单地调用它。

由于我们有 mocha 和 node 作为目标，我们必须手动编辑一些东西，并添加`node`和`mocha`作为目标环境。否则会抛出类似`'describe' is not defined`的错误。

由于我们在 puppeteer 中使用浏览器代码，它会抛出诸如 how `document is not defined`之类的错误。我们也需要将`browser`作为目标，或者应用变通方法。

我们都很好，一旦我们把它脱绒。它可能会抛出一些关于最佳实践的小警告，如果我们愿意，我们可以修复它们，或者继续前进。

结果:
[![](../Images/6068e637d93f3ac8c244220dda0170d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gqGe1dV6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/entrptaher/mocha-ci-test/master/screenshots/linting.png)

### 其他质量检查

如果不够，我们可以使用其他的代码质量检查工具比如`codacy`。我们所要做的就是添加项目，它会自己做所有其他的事情。它将克隆项目，检测编程语言，运行各种代码模式并计算项目度量。最后，它会给我们一个项目认证，A 到 F 级，A 是最好的，F 是最差的。样本回购可能在开始时得到 A，或 f。但如果我们不够小心，它会像我们正在使用的其他工具一样，在以后对我们大喊大叫。

## 创建/准备 CI 环境

无头是浏览和测试的一个重要因素。有时 headless 会留下很大的痕迹，并作为 bot 被捕获，否则它非常适合测试我们自己的网站，我们可以明确地将其排除在测试之外。

在 CI 环境中启用 headless 有时需要 6 秒多的时间才能启动，而禁用 headless 会将时间减少到 1 秒。它可以被优化和破解。虽然这看起来没什么大不了的，但如果你预算紧张，CI 成本非常高，这确实是一件大事。大多数 CI 为您提供了自由运行的时间，但是在资源几乎有限的情况下，它会很快增加。

除非明确要求，否则确保启用 headless。

CircleCI 有`node:8.12.0-browsers`和其他类似的标签，包括`headless`和`xvfb`显示所需的库。仅仅使用他们的向导就足以让你前进。

我们将需要包括林挺，然后我们将运行实际的测试，确保它是高质量的代码，工作良好，而不是在我们自己的机器上运行它。如果我们足够勇敢，我们甚至可以应用修复。

查看`.circleci/config.yml`中的代码。结果看起来很美，除非所有的测试都以红色结束。

[![CircleCI example](../Images/940ab580e0ff48f8bbc20aabe29fe511.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MTpr5Yty--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/entrptaher/mocha-ci-test/master/screenshots/circleci-results.png)

## 最终步骤

到目前为止，我们几乎复制粘贴一切或遵循向导，一切都完成了。我们只需要在 circleCI 上设置实际项目并链接回购。然后按下回购将触发构建。

这并不难，是吗？

# 每次提交时触发

在每次提交/推送时触发成百上千的测试是非常糟糕的。有时你只是更新自述文件，并不想触发的事情。有时你会想在做出重大改变后才触发。为了防止这种更糟糕的事情发生，请确保推送到一个单独的分支，并且只有在您对当前的代码更改感到满意时才进行拉请求。合并到主分支将触发构建，这也可以设置为各种 webhooks 和中断度量。

到目前为止，这个回购不需要这么大的自述文件，但我最终还是写了它，因为我非常懒于写文档，每当我需要一些旧回购的脚本来使用时，我都会感到困惑。

以后我将能够用一些很棒的东西来扩展这个回购，

*   流程管理，
*   队列管理，
*   dockersbuilds，
*   代码覆盖率
*   服务器部署。

这么大的潜力！