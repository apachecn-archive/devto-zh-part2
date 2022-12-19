# 消除 UI 测试的恐惧😱

> 原文：<https://dev.to/uilicious/taking-the-horror-out-of-ui-testing--1fi6>

> 问题不在于那些失败的测试。我们只是有非常破的 UI 测试工具。

UI 测试烂透了。确实如此。

如果你还不熟悉自动化端到端测试，有一些著名的免费开源框架，按照 Github 明星的顺序排列如下: [NightmareJS (16K)](https://github.com/segmentio/nightmare) ， [Selenium (12K)](https://github.com/SeleniumHQ/selenium) ， [WebDriverIO (4K)](https://github.com/webdriverio/webdriverio) ， [CodeceptJS (1K)](https://github.com/Codeception/CodeceptJS) 。

测试通常看起来或多或少像这样——花一分钟来弄清楚这个来自 NightmareJS 的“Hello World”示例是做什么的🤔:

```
const Nightmare = require('nightmare')
const nightmare = Nightmare({ show: true })

nightmare
  .goto('https://duckduckgo.com')
  .type('#search_form_input_homepage', 'github nightmare')
  .click('#search_button_homepage')
  .wait('#r1-0 a.result__a')
  .evaluate(() => document.querySelector('#r1-0 a.result__a').href)
  .end()
  .then(console.log)
  .catch(error => {
    console.error('Search failed:', error)
  }) 
```

你想通了吗？

这样做的是去 DuckDuckGo，在搜索框中输入“github nightmare”，按下搜索按钮，等待第一个结果出现，并打印第一个结果的链接地址。

拜托各位，我以为我们已经知道硬编码和使用魔法等待是不允许的。测试代码仍然是代码，而且这些代码很难闻💩。这使得内容难以阅读，并且**更难维护**。如果产品改变了设计或者前端决定只做一点春季大扫除怎么办？该死，测试坏了。没人有时间去修理那些该死的 CSS 选择器！

另外，我们到底想测试什么？
**用户旅程**，还是*HTML*？

这样写测试怎么样？

```
I.goTo("https://duckduckgo.com")
I.fill("Search", "Github nightmare")
I.pressEnter()
I.see("Github - segmentio/nightmare")
I.click("Github - segmentio/nightmare") 
```

简洁。可读。**可维护**。
和前端不可知论者。VueJS，ReactJS，有角...有关系吗？

自从 jQuery 时代到来之前，我和@picocreator 就一直致力于构建 web 应用程序，我们都积累了自己凌晨 2 点的恐怖故事，试图确保东西得到测试并准时发货**，或者通过在生产中测试让事情在我们面前爆炸💣💣💣😱😱😱。我们每年万圣节晚上都给小开发者讲这些恐怖故事。好吧，不管怎样，有点跑题了...**

 **我们在软件架构上有很多不同意见，并且经常争论可维护的代码是什么样子，但是有一点我们是一致的，那就是问题不在于被破坏的测试。**我们只是有很破的 UI 测试工具**。有人需要修理它。这就是我们过去两年致力于的工作:

[![Testing DuckDuckGo](../Images/2b280ddd7c17f4bb0f33fbfe558f099b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r63YN5E3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ggw3s02vif0pk2z7qg30.gif)

小菜一碟。

但是这个测试太简单了。你可能会想，是的，这很好，但是如果事情变得更复杂，比如有 50 个“添加到购物车”按钮，或者图标按钮怎么办？

让我们找点乐子，好吗？😎

哦，等等，在我们开始之前，你要知道，这绝对不是一个由 AI 驱动的黑盒算法，稍后会有更多的介绍。

# 测试开发。到

让我们从基础开始，确保最重要的功能之一——搜索——能够正常工作。

```
I.goTo("https://dev.to/")
I.fill("Search", "dev.to")
I.pressEnter()
I.click("thepracticaldev")
I.see("The hardworking team behind dev.to ") // mmhm, very hardworking indeed. 
```

测试从 UI 的实现中分离出来的好处是，我们可以很容易地重用同一个测试来测试响应式设计。让我们看看搜索在桌面和移动视图上是否如预期的那样工作。

[![Testing search on dev.to](../Images/b8c6c334d977a99f76d6c7c9e25a2802.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bcli3NTo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/95u22qzfzduklrn99zsb.gif)

现在，让我们试着点击 DEV.to 的 logo 返回主页。UI-licious 扫描使用`title`设置的可访问性属性和工具提示，以及各种流行框架使用的其他类似属性。我们家的 logo 有什么可以用的吗？

```
<a href="/" class="logo-link" id="logo-link" aria-label="DEV Home"></a> 
```

哦，看，这是 DEV.to 的标志在引擎盖下的样子。还有一个`aria-label`、**神奇的**！让我们点击“开发人员主页”。

```
I.click("DEV Home") // We love aria-labels
I.amAt("https://dev.to/") 
```

我们开始吧:

[![Clicking on logo using ARIA-labels](../Images/9d880a2369e7cc6e85b382c35c8f180c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_ZStAJgi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i37xe7c6ljv7h62g5j2x.png)

好吧，让我们发挥创意，在开发商店买点东西。我要拿一百个贴纸包和开发包。

```
I.click("DEV Shop")
I.amAt("https://shop.dev.to/")

let shopping_list = [
  "Dev tote",
  "Sticker Pack"
]
shopping_list.forEach((item) => {   
  I.click("The DEV shop")
  I.click(item)
  I.fill("Quantity", 100) // lets' get a hundred of each
  I.click("Add to cart")
}) 
```

[![Shopping on DEV.to](../Images/ccd863810d1811898fb2b4bbd7010723.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RDYFos8N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fun5vv0au3k19zaccgol.gif)

好的...快好了。不，等等，我们再拿几包。嗯（表示踌躇等）...购物车中有几行商品，我们需要选择正确的数量框来更新。没关系，我只需要在更新数量之前，具体告诉 UI-licious 什么。

```
I.amAt("/cart")
I.see("Dev tote") 
I.fill("Quantity", 120) // UI-licious will pick the quantity box for "Dev Tote" to fill
I.pressEnter() 
```

[![Resolving similarities](../Images/714a1f934326ff6e5bf4d7ef8cf9e89e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2Q0z2jOK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/51khr52csal5cpkojp1m.gif)

最重要的是，让我们做一些测试，只是为了确保 UI-licious 本身的工作。

[![Eating our own dogfood](../Images/5076dac8670523ce8119abe9d7ce5cc2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0HN6q09f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c6f4abcajsoojek7tnbo.png)

是的宝贝。小菜一碟。😎

# 引擎盖下

不，它不是由人工智能驱动的。至少不是现代意义上的。

警告，前方有意见！测试应该是确定性的，也就是说，在给定相同输入的情况下，它应该总是产生相同的结果。随机的不可预测的行为在测试中并不理想，在人工智能驱动的测试引擎中修复缺陷包括...向它投掷更多“正确的”样本数据以使它更准确。

UI-licious 的工作原理是在方法上逆向工程意图(你所说的从你的 HTML 中`I.click("Sign in")`是什么意思)和前面的步骤是什么。它在可访问的网站上效果最好。你的代码*不一定要完美*才能可测试，但是使用**语义 HTML 和 ARIA 属性**肯定会有帮助。

(顺便说一下，UI-licious IDE 完全是使用 VueJS 构建的。\o/)

# 让测试变得伟大...对于修复 bug 的人来说。

我认为得到 bug 报告最烦人的部分是当它们不完整的时候，我需要追着报告者去寻找复制 bug 的步骤。与此同时，老实说，我也懒得报告错误。这就是为什么我们试图让 bug 复制报告变得完整和可操作(并且漂亮！)尽可能。👇

[![Awesome bug replication reports](../Images/00dc355ec3535ac70af5eb4db7d8f77f.png)T2】](https://snippet.uilicious.com/test/public/1f9qgLPBa5EC4ryZbnnAoB)

# 什么时候应该自动化 UI 测试？

一个很好的指导方针是:当你第 n 次测试*的*那个*登录流和*的*用户角色时。*

此外👇

[![klamping image](../Images/32840fcf944cf815a25ee8ab11b1608f.png)](/klamping) [## 停下来。不要测试那个！

### 凯文夹紧

#testing #javascript](/klamping/stop-dont-test-that-21hh)

你应该首先自动化单元测试、集成测试还是端到端测试？没关系，从某个地方开始。我通常建议从需要复杂条件和数学的单元测试开始，从关键用户流的端到端测试开始，因为这些也有助于捕捉下游的错误。

# 利弊？

利:起价 0 美元。devops 少了一件担心的事。反对意见:它还不是开源的。（...直到钱从天上掉下来)

# 蛋糕是真的吗？

是的。这不是谎言，我们无论去哪里都有蛋糕。

> ![unknown tweet media content](../Images/242693ad43a5105e6c5eb281da798732.png)![UI-licious profile image](../Images/dc9f89ffa9c507d813a0bdfcc7f3843a.png)UI-licious@ UI _ licious![twitter logo](../Images/65e26e35707d96169ec8af6b3cbf2003.png)休息时间 [@fossasia](https://twitter.com/fossasia) ！吃点吧🍰和☕在展览馆。2018 年 3 月 24 日上午 04:01[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=977394981714325504)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=977394981714325504)0[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=977394981714325504)2

万圣节快乐！
👻👻👻

# 学分

我们有一个很小但很敬业的高级和初级开发团队-> [@picocreator](https://dev.to/picocreator) 、[@ jmtion](https://dev.to/jmtiong)、 [@sopnopriyo](https://dev.to/sopnopriyo) 、Wesley Chang 和我自己。

由 NeONBRAND 在 Unsplash 上拍摄的封面照片**