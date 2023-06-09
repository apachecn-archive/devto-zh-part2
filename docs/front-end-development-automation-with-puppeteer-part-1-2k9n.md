# 使用木偶师实现前端开发自动化。第一部分

> 原文：<https://dev.to/papaponmx/front-end-development-automation-with-puppeteer-part-1-2k9n>

## [T1】简介](#intro)

如果你来这里只是为了`code`，这里是 [**项目回购**](https://github.com/papaponmx/puppeteer-in-the-wild) 。

Puppeteer 是一个工具，允许你编写一个 Chrome 的无头实例，并自动完成一些重复的任务。这有点像 Selenium，但是更酷，更容易使用(这是基于我的经验的观点，而不是事实)。如果你想让我写一个比较，告诉我。

我是一名前端开发人员，对我来说，my 是一个表达、联系有趣的人和解决有趣问题的平台。玫瑰有刺，当然，我的工作中有些部分我根本不喜欢做。我强烈的愿望是永远不要做这些事情，这激励我自动完成这些任务，现在与你分享。请注意，所描述的场景是真实的，但是信息因为我在当前工作中签署的 [NDA](https://en.wikipedia.org/wiki/Non-disclosure_agreement) 而改变了。

## 初始设置

对于我们的环境，由于我们是前端开发人员，我们将尝试使用[节点](https://nodejs.org/en/)来处理所有事情。我将用[纱](https://yarnpkg.com/en/)代替`npm`。

以下是基本依赖项的命令:

*   木偶师。无头 Chrome。
*   [冷却器`console.log`的信号](https://github.com/klauscfhq/signale)。

```
yarn add puppeteer signale -D 
```

Enter fullscreen mode Exit fullscreen mode

## 场景 1:你的改动有没有破坏什么东西？

#### 问题

你刚刚给一个基本的 CRUD 应用程序添加了一些很酷的特性，你不应该破坏任何东西，但是你仍然想确保一切正常。

所有的单元测试都在工作，但是仍然需要一些手动测试。在这个特定的应用程序中，当我注册某个用户时，会出现一个成功通知。

#### 解

由于这个应用程序有一些 html 表单，我的脚本需要做 3 件事:

*   用**当前日期**写一些输入，
*   截屏。
*   成功通知到达时通知我。

在每种情况下，都有一些变量，所以这是文件夹结构。

```
/test-folder
 |-index.js  // Here is where the magic happens
 |-config.js // Test variables   
 |-locators.js // A JSON with all CSS locators needed for this test. 
```

Enter fullscreen mode Exit fullscreen mode

这个测试的`config.js`看起来是这样的:

```
const config =  {
  url: 'http://localhost:3000/',
  phoneNumber: '123-456-7890',
  email: 'test@example.com',
  password: 'test1234',
}

module.exports.config = config; 
```

Enter fullscreen mode Exit fullscreen mode

在`index.js`日，我们将编写自动化测试的代码。首先我们需要导入:

```
const puppeteer = require('puppeteer'); // High level API to interact with headless Chrome
const signale = require('signale');
// import LOCATORS from './locators'; // A JSON with all the CSS locators we need.
const config = require('./config'); 
```

Enter fullscreen mode Exit fullscreen mode

我们将以字符串的形式添加日期，这样我们就知道测试是何时运行的。

```
 const d = new Date();
  const dateString = `${d.getDate()}_${d.getHours()}h${d.getMinutes()}`; 
```

Enter fullscreen mode Exit fullscreen mode

对于这个测试，我们将创建一个函数并在最后运行它。这个函数将做许多事情:

#### 1。打开一个浏览器实例，导航到一个给定的 url，它来自`config.js`；

。

```
 const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto(LOCAL_HOST_URL); 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。一旦页面加载，我们需要填写所有的输入表单。我们可以使用 CSS 选择器，并将它们作为字符串传递。为了简单起见，我们将它存储在一个单独的文件中，名为`locators.js`。

```
 module.exports.locators =  {
  userNameSelect: 'select[name="userName"]',
  birthdayInput: 'input[name="birthDay"]',
  submitButton: 'button[type="submit"]'
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用 ID，但是我在编写 HTML 时尽量避免使用它们，在这个例子中，我使用属性和名称有两个原因:

*   我的团队中没有经验的开发人员倾向于过度使用它们，或者偏爱 ID 而不是类名约定或特殊性。
*   可以有许多选择或输入。再读一遍原因 1。

下面是如何将值传递给输入。请注意，第一个输入是选择器，第二个是我们要键入的值。我们可以

```
await page.type(locators.userNameSelect, 'Jaime');
await page.type(locators.birthdayInput, '02/04');

await page.click('button[type="submit"]'); 
```

Enter fullscreen mode Exit fullscreen mode

对于 select，我们假设 html 看起来像这样。请注意值属性:

```
 <select>
    <option value="Jaime">Jaime</option>
    <option value="James">James</option>
    <option value="Bond">James</option>
  </select> 
```

Enter fullscreen mode Exit fullscreen mode

### 3。等待通知。

最终，随着您的进展，您可能会发现一些操作是异步的场景；HTML 表单可以显示从后端返回的值。例如，您可能希望向用户显示晚餐预订的日期。

对于这个例子

解决这个问题的方法是使用`page.waitForSelector`。它返回一个承诺，因此我们可以相应地采取行动。

```
await page.waitForSelector('.notification-message')
  .then( async () => {
    signale.success('Form was submitted successfully'); // This is a fancy console.log()
    await page.screenshot({path: `automated_test_success_`$dateString`.png`});
    browser.close();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

下面是单个文件中的代码， [**和项目回购**](https://github.com/papaponmx/puppeteer-in-the-wild) 。

```
 const puppeteer = require('puppeteer'); // High level API to interact with headless Chrome
const signale = require('signale');
// import LOCATORS from './locators'; // A JSON with all the CSS locators we need.
const config = require('./config');

const runTest = async (params) => {
  signale.debug('Opening browser...');
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  const d = new Date();
  const dateString = `${d.getDate()}_${d.getHours()}h${d.getMinutes()}`;
  const userName = `USER_FROM_TESTING_SCRIPT_${dateString}`;

  // Go to the website;
  await signale.watch('Navigating to the site');
  await page.goto(config.LOCAL_HOST_URL);

  // Fill the form
  await signale.watch('Filling up the form');
  await page.type(locators.userNameSelect, 'Jaime');
  await page.type(locators.birthdayInput, '02/04');
  await page.click('button[type="submit"]');

  await page.waitForSelector('.notification-message')
      .then( async () => {
        signale.success('Form was submitted successfully'); // This is a fancy console.log()
        await page.screenshot({path: `automated_test_success_`$dateString`.png`});
        browser.close();
  })
    .catch(() => signale.fatal(new Error('Submit form failed')));
};

runTest(); 
```

Enter fullscreen mode Exit fullscreen mode

我希望这有助于你看到潜力，你足够关心分享。感谢您的阅读。

我将在下面的部分中介绍另外两个场景:

*   场景 2:有东西停止工作了，你能看看吗？
*   场景 3:比较本地快照和测试快照