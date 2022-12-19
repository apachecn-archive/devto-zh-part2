# 测试和质量保证

> 原文：<https://dev.to/jamesrweb/accessibility-101---testing-and-qa-595o>

## 简介

在这篇文章中，我们将讨论如何测试我们的应用程序和网站的可访问性标准。

> “然而，自动化测试不能涵盖一切；只有 20%到 50%的可访问性问题可以被自动检测出来。例如，我们还不能自动比较 alt 属性和图像内容，还有一些屏幕阅读器测试也需要手动进行。”赛伦·戴维斯

正如您所看到的，只有 20% - 50%的可访问性标准可以用自动化的方式进行测试。因此，自动化测试本身永远不应该完全取代在用户测试中使用开发或测试中的最佳实践，以及在设计和开发阶段通过可用性测试查看真实数据和用户交互。

在这篇文章中，我将向你展示如何使用 aXe 可访问性引擎来测试测试管道中关键区域的可访问性。

让我们做一个非常先进的单页网站:

*index.html*T2】

```
<!DOCTYPE html>
<html lang="en">
<head>
    Greatest website in the world
    <link rel="stylesheet" href="/styles/main.css">
</head>
<body>
    <h1>My awesome product</h1>
    <small>Why didn't anyone make this sooner?</small>
    <img src="https://fillmurray.com/500/500" />
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

*styles/main.css*

```
h1 {
    font-size: 3rem;
}

small {
    color: lightgrey;
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们在浏览器中打开 index.html 时，我们看到的是有史以来最好的网站，对吗？好吧，至少，现在它符合我们的目的。

## 测试

在我们开始编写测试之前，确保您已经有效安装了 [node.js](https://nodejs.org/en/) 。

假设你这样做了，让我们在一个终端上运行`npm init -y`，这个终端目前已经被 cd 到我们的项目中。

现在运行以下命令:

```
npm install selenium-webdriver axe-webdriverjs chromedriver -D 
```

Enter fullscreen mode Exit fullscreen mode

```
npm install express -S 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要为我们想要测试的浏览器安装二进制文件，这些可以在 [Selenium webdriver 文档](https://www.npmjs.com/package/selenium-webdriver)中找到。

在您选择的二进制文件成功安装后，打开项目根目录中的`package.json`文件。

应该是这样的:

```
{  "name":  "testing",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1",  },  "keywords":  [],  "author":  "",  "license":  "ISC",  "devDependencies":  {  "axe-webdriverjs":  "^2.0.1",  "selenium-webdriver":  "^4.0.0-alpha.1",  "chromedriver":  "^2.41.0"  },  "dependencies":  {  "express":  "^4.16.3"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，在`scripts`部分，让我们添加下面几行:

```
"scripts":  {  "test":  "npm run test:accessibility",  "test:accessibility":  "TEST_URL=localhost:3000 node ./tests/accessibility",  "start:dev":  "PORT=3000 node .",  "start:prod":  "node ."  } 
```

Enter fullscreen mode Exit fullscreen mode

而现在我们的`package.json`应该是这样的:

```
{  "name":  "testing",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "npm run test:accessibility",  "test:accessibility":  "TEST_URL=localhost:3000 node ./tests/accessibility",  "start:dev":  "PORT=3000 node .",  "start:prod":  "node ."  },  "keywords":  [],  "author":  "",  "license":  "ISC",  "devDependencies":  {  "axe-webdriverjs":  "^2.0.1",  "selenium-webdriver":  "^4.0.0-alpha.1",  "chromedriver":  "^2.41.0"  },  "dependencies":  {  "express":  "^4.16.3"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将创建我们的服务器:

*index.js*

```
const express = require("express");
const app = express();
const port = process.env.PORT || 8080;

app.use("/styles", express.static("styles"));

app.get("/", (request, response, nextRoute) => {
    response.sendFile(`${__dirname}/index.html`);
 });

app.listen(port, () => {
    console.log(`app listening on port: ${port}`)
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以将`npm run start:dev`写入我们的终端，如果我们导航到`localhost:3000`，我们应该会看到我们可爱的网站启动并运行了！

好了，现在我们已经完成了设置，让我们进入有趣的部分，编写一个简单的测试来显示我们的简单页面包含的所有违规。

*tests/accessibility/index . js*T2】

```
const AxeBuilder = require('axe-webdriverjs');
const { Builder } = require('selenium-webdriver');

async function TestPageByUrl(url) {
  const driver = await new Builder()
  .forBrowser('chrome')
  .build();

  try {
      await driver.get(url);

      const { violations } = await AxeBuilder(driver).analyze();

      if(violations.length > 0) {
        console.log(
          JSON.stringify(violations, null, 4)
        );
        process.exitCode = 1;
      }
    } catch(error) {
      throw new Error(error);
    } finally {
      await driver.close();
      await driver.quit();
    }
}

TestPageByUrl(process.env.TEST_URL)
  .catch(console.error); 
```

Enter fullscreen mode Exit fullscreen mode

使用这种方法，我们确保如果测试运行良好，没有任何错误，但是如果有违规，我们可以在脚本正确关闭了`driver`实例之后干净地退出流程。这意味着在我们的管道中，如果在给定的 url 中有任何违规，测试显然会失败。

使用这种方法，您还可以按照一个包含一组路径的`tests/accessibility/routes.js`文件来做一些事情，并依次测试每一条路径。或者，您可以更进一步，通过使用 selenium 和 axe 驱动程序 API 来测试整个核心用户流，从而添加集成和验收测试，但这与本文的内容有些脱节。

正如你所看到的，获得结果真的很容易，你可以根据返回的数据做很多事情，比如使用 JIRA API 为每一个问题生成文档或开罚单。

除此之外，如果我们停止我们的服务器实例并在我们的终端中重新运行`npm run start:dev`,然后在一个新的窗口中使用`npm run test:accessibility`进行测试，我们将会看到一些问题出现:让我们修复这些问题！

## 修正结果

### 图像必须有替换文字

让我们改变图像并添加一个 alt 标签

```
 <img 
    src="https://fillmurray.com/500/500"
    alt="Bill Murray portrait" /> 
```

Enter fullscreen mode Exit fullscreen mode

### 确保前景色和背景色之间的对比度符合 WCAG 2 AA 对比度阈值

让我们改变小标签
的颜色

```
small {
  color: #666;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 确保所有内容都包含在地标区域内

让我们将内容包装在一个`main`标签中:

```
<main>
  <h1>My awesome product</h1>
  <small>Why didn't anyone make this sooner?</small>
  <img 
    src="https://fillmurray.com/500/500"
    alt="Bill Murray portrait" />
</main> 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们运行`npm run test:accessibility`时，我们将看到没有错误显示，干得好！

## Gitlab CI 管道(持续集成管道)

当需要时，我经常使用 Gitlab 或 Travis 进行管道测试，根据我的经验，我认为 Gitlab 是最容易设置的，下面的 yaml 文件将基本上运行我们在上面详细描述的所有测试，只要将它们放入 Gitlab repo 中。

```
image: node:9.4.0

cache:
  paths:
    - node_modules/

stages:
  - testAccessibility

TestAccessibility:
  stage: testAccessibility
  tags:
    - node
  before_script: 
    - npm install
  script:
    - npm run test:accessibility 
```

Enter fullscreen mode Exit fullscreen mode

一旦推送，gitlab 将运行我们所有的测试，如果我们的管道通过，我们刚刚实施了一整套可访问性测试，这将帮助我们通过自动化一致地推送尽可能可访问的内容，非常好，对吗？

## 结论

正如您所看到的，在初始设置过程之后，可访问性的自动化测试非常简单，非常值得实现。使用 aXe 真的很直观，我个人认为它提供了目前市场上最好的解决方案，所以在你的网站或应用程序上试一试，看看你的整体表现如何，迭代改进，老实说，你最终会收获回报，因为用户会更高兴，他们会更多地被包括进来，整体情况会因为你的工作而变得更好。

## 资源

*   [aXe 辅助功能引擎](https://www.deque.com/axe/)
*   [轴芯](https://axe-core.org/)
*   [Axe webdriver](https://www.npmjs.com/package/axe-webdriverjs)
*   [Selenium webdriver](https://www.npmjs.com/package/selenium-webdriver)
*   [GitLab 持续集成&部署](https://about.gitlab.com/features/gitlab-ci-cd/)