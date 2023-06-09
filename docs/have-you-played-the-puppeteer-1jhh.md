# 你玩过木偶戏吗？

> 原文：<https://dev.to/taitung/have-you-played-the-puppeteer-1jhh>

我最近得到了一个机会，在 Google puppeteer 上做一个 UI 测试自动化项目。这是一个高度可定制的工具，供开发者控制谷歌 Chromium 浏览器的行为。木偶戏的细节可以在这里找到[。](https://github.com/GoogleChrome/puppeteer)

我们有一个谷歌 Chrome 网络扩展项目，每次发布前的测试将由实习生进行一系列测试。我们试图通过执行项目的单元测试来保持 100%的覆盖率。然而，集成测试，如点击 API 和监视 web 扩展 UI 如何根据 web 扩展所在的网页或网站进行操作，仍然是手动完成的。事实上，手工测试仍然很重要。但是，我们有时会发现，由于各种原因，实习生通过手工测试收集的数据并不准确。我们已经调查了 UI 测试自动化解决方案，如 Selenium with WebDrivers 和其他解决方案，但我们最终选择了 Google puppeteer，因为我们现阶段只支持 Google chrome。

谷歌木偶师的 API 非常简单。从创建一个浏览器开始非常简单，就像下面的代码

```
const browser = await puppeteer.launch({
    headless, // if true then no UI will be shown
    ignoreDefaultArgs: true,
    ignoreHTTPSErrors: true,
    slowMo, // delay executing each actions performed on web broswer
    args, // other args
  })
return browser; 
```

Enter fullscreen mode Exit fullscreen mode

由于测试目标是浏览器扩展，我需要指定浏览器扩展的位置，以便 Google Chromium 浏览器可以定位资产，然后开始安装。 **extPath** 是保存浏览器扩展资源的文件夹的绝对路径。

```
 const args = new Array();
  args.push(`--disable-extensions-except=${extPath}`);
  args.push(`--load-extension=${extPath}`);
  const b = await puppeteer.launch({
    headless,
    ignoreDefaultArgs: true,
    ignoreHTTPSErrors: true,
    slowMo,
    args,
  });
  return b; 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将向您展示右上角带有 web 扩展图标的 Google Chromium 浏览器，并且可能会打开另一个选项卡，该选项卡会登录到 web 扩展在 [menifest.json](https://developer.chrome.com/extensions/manifest) 中指定的网站。

组成谷歌网络扩展的关键组件很少。其中，通常称为 popup.html**的主 UI 是项目中的测试目标之一。据我所知，目前没有任何 [API](https://developer.chrome.com/extensions/api_index) 能够触发右上角的扩展图标或按钮的点击事件来显示主 UI，即 popup.html。但是，有一种方法可以绕过这个问题，那就是直接访问 popup.html 页面。因此，您需要创建一个[页面](https://github.com/GoogleChrome/puppeteer/blob/v1.4.0/docs/api.md)，并指定您想要访问的 URL。在下面的代码中，有一个名为 extId 的变量。如果您没有这个 id，您可能无法加载 popup.html。** 

```
 const page = await browser.newPage(); // create a new tab
  const chromeExtPath = `chrome-extension://${extId}/popup.html`; // where to find extId?
  await page.goto(chromeExtPath, { waitUntil: 'domcontentloaded', });
  if (bringToFront) await page.bringToFront();
  await page.reload();
  return page; 
```

Enter fullscreen mode Exit fullscreen mode

对于正常安装，如果你在 Macbook 上，你应该在这个目录下找到 web 扩展 id**~/Library/Application Support/Google/Chrome/Default/Extensions**。然而，您不会在这个目录中找到任何与[解包的](https://developer.chrome.com/extensions/hosting)扩展相关的 id。

```
➜  ls -l ~/Library/Application\ Support/Google/Chrome/Default/Extensions
total 0
drwx------@ 3 guoguang  staff  96 May 21 10:33 bmnlcjabgnpnenekpadlanbbkooimhnj
drwx------@ 3 guoguang  staff  96 May 31 12:57 chhjbpecpncaggjpdakmflnfcopglcmi
drwx------@ 3 guoguang  staff  96 May 26 13:37 djjjmdgomejlopjnccoejdhgjmiappap
drwx------@ 3 guoguang  staff  96 May 30 13:59 jifpbeccnghkjeaalbbjmodiffmgedin
drwx------@ 3 guoguang  staff  96 May 31 12:55 kbfnbcaeplbcioakkpcpgfkobkghlhen
drwx------@ 3 guoguang  staff  96 May 18 14:01 niloccemoadcdkdjlinkgdfekeahmflj
drwx------@ 3 guoguang  staff  96 May 16 10:31 nmmhkkegccagdldgiimedpiccmgmieda
drwx------@ 3 guoguang  staff  96 May 16 10:31 pkedcjkdefgpdelpbcmbmeomcjbeemfm 
```

Enter fullscreen mode Exit fullscreen mode

有两种方法可以解决缺少扩展 id 的问题。首先，[在 Google Chrome 浏览器中加载解压后的扩展](https://developer.chrome.com/extensions/getstarted),并打开开发者模式来显示扩展 id。然而，这必须通过手动过程，如果你想有一个完全自动化的测试过程，这是有点问题的。第二种方法是动态生成扩展[键](https://developer.chrome.com/apps/manifest/key)和 id。按照 StackOverflow 上的这个[线程](https://stackoverflow.com/questions/37317779/making-a-unique-extension-id-and-key-for-chrome-extension?rq=1)关于如何创建密钥和 id，我有一个 shell 脚本生成密钥和 id，如下所示。您可能需要在创建 pem.key 时将密钥大小扩大到 4096，以便使 web 扩展 id 和密钥适用于这些扩展。顺带一提，我用的谷歌 Chromium 版本是 68.0.3419.0 开发者 Build 64 位。

```
 # genereate the web extension key.
  ret=$(openssl rsa -in ./extensions/key.pem -pubout -outform DER | openssl base64 -A)
  # based the key above to generate the **extension id**
  ret=$(openssl rsa -in ./extensions/key.pem -pubout -outform DER | shasum -a 256 | head -c32 | tr 0-9a-f a-p) 
```

Enter fullscreen mode Exit fullscreen mode

不仅是 popup.html，扩展如何与网页交互也是测试目标。如果用户访问我们合作伙伴的网站，我们的 web 扩展的一个重要功能是改变其图标。一开始，我尝试通过传递[消息来同步 web 扩展和网页之间的测试数据。我发现我需要在扩展中修改很多代码，特别是对于](https://developer.chrome.com/extensions/messaging) [onMessage](https://developer.chrome.com/apps/runtime#event-onMessage) 和[on externalmessage](https://developer.chrome.com/apps/runtime#event-onMessageExternal)部分。为了最小化更改，我使用 Firebase 作为同步测试数据的媒介。这可能不是最好的选择，但目前对我们来说确实有效。

```
 firebase.initializeApp(JSON.parse(data));
  firebaseDB = firebase.database();
...
  firebaseDB.ref('urlPatternTest/').update({
    uuid: testKey,
    host: '',
    icon: '',
    ts: (new Date()).getTime(),
    key: '',
  }, err => {
    if (err) reject(err);
    else resolve();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

其余的是关于代码，我们如何做用户界面自动化测试，如切换标签，登录/注销等。

```
async function selectTab(page, tabName) {
  const selector = 'div#buttonBox :first-child'; // querySelector
  let handle = await page.waitForSelector(selector); // blokc until the expected dom element loaded
  const text = await page.evaluate(sel => document.querySelector(sel).innerText, selector, handle); // evalute the selector and return the data
  if (text === tabName) {
    handle = await page.$(selector);
    await handle.click();
  } else {
    handle = await page.$('div#buttonBox :last-child');
    await handle.click();
  }
}
...
....
  let selector = 'input[type="email"]';
  await sgPage.waitForSelector(selector);
  const emailHandle = await sgPage.$(selector);
  await emailHandle.type(USER_EMAIL); // key in user email

  selector = 'input[type="password"]';
  const pwdHandle = await sgPage.$(selector);
  if (_.isNil(pwdHandle)) throw new TypeError('found no password input element!');
  await pwdHandle.type(USER_PWD); // key in use pwd
... 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我真的很喜欢使用 Puppeteer，因为它可以简单地用成熟的 API 和截取数据(HTML、图像、URL、cookies 等)的方式完成 UI 测试。现在，我们仍在通过添加更复杂的测试场景来完成这个项目。不确定你是否会在不久的将来使用木偶师。欢迎任何评论！