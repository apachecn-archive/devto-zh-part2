# 用谷歌地图和木偶师追踪停车场

> 原文：<https://dev.to/jlengrand/playing-around-with-google-maps-and-puppeteer-to-track-parking-lots-fcn>

[![Playing around with Google Maps and Puppeteer to track parking lots](img/b22d533d13ab7bb634375e85890048e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xO-3MOht--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1446776811953-b23d57bd21aa%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

在过去的几周里，我读了很多关于 **[地理营销](https://en.wikipedia.org/wiki/Geomarketing)** 的文章，并一直在寻找有趣的体验。

我的一个想法是调查法国最大的[家乐福](https://www.carrefour.fr/)在法国有多少个开放停车场。

这是开始摆弄[木偶师](https://github.com/GoogleChrome/puppeteer)的完美用例，这是一个**无头 Chrome 节点 API** ，我是在去年哥本哈根的聚合物峰会上第一次听说的。

首先要做的是在数据库中收集所有家乐福的位置。幸运的是，主网站本身[已经覆盖了我们](http://www.carrefour.fr/magasin/liste-carrefour)。

主要想法是检查列表，提取商店名称及其位置(来自谷歌地图链接)。

## 第一步

我首先简单地用 [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) 和 [Selenium](https://dev.toSelenium%20-%20Web%20Browser%20Automation) 进行了尝试，但网站返回了错误。防刮擦保护！

我决定转行做木偶戏演员。

我的第一次尝试没有成功。与其他方法的错误相同。

代码非常简洁:

```
const puppeteer = require('puppeteer');

async function run() {
  const STORE_LINK_SELECTOR = ".k4-storelist-sublist-link"
  const CARREFOUR_BASE = "http://www.carrefour.fr/magasin/liste-carrefour";
  const browser = await puppeteer.launch({});
  const page = await browser.newPage();
  await page.goto(CARREFOUR_BASE);
  const linksArray = await page.evaluate(
    () => [...document.querySelectorAll('.k4-storelist-sublist-link')].map(elem => elem.href)
  );

  browser.close();
}

run(); 
```

Enter fullscreen mode Exit fullscreen mode

木偶师最酷的一点是，你实际上是在操纵 Chrome，所以你本身不是一个机器人(而且你对目标的网站很温柔)。

这里的解决方案是移除**无头**默认值。我喜欢这个，因为这个想法不是把网站吸干，而是提取一些你也可以用手得到的信息。

```
const browser = await puppeteer.launch({
    headless: false
}); 
```

Enter fullscreen mode Exit fullscreen mode

答对了。我现在可以收集所有商店网页的列表！

下一步自然是提取每个商店的纬度/经度，以及每个家乐福的“唯一 id”(名称)。我们遍历上面找到的链接列表，代码的核心可以包含在一个函数
中

```
async function getLocation(link, browser){
    const page = await browser.newPage();
    await page.goto(link);

    const title = await page.evaluate(() => document.querySelector(".k5-pagehead_storename").textContent);
    const location = await page.evaluate(() => document.querySelector("div.k5-pagehead_bottom > a:nth-child(1)").href);
    const url = queryString.parseUrl(location);
    const latlon = stringToLatLon(url.query.daddr);

    page.close();
} 
```

Enter fullscreen mode Exit fullscreen mode

本质上，我们在这里做的是找到每个商店网页上可用的 **Google Maps** 链接，并使用 [querystring](https://www.npmjs.com/package/query-string) 提取纬度/经度值。

好极了，我们有法国所有的家乐福超市，以及它们的具体位置。下一步是什么？鸟瞰这个地方！

这是第一个版本。同样，为了更温和，我们运行时没有使用无头模式。

```
async function makeScreenshot(location, browser){
    const title = location.title;
    const link = location.location;

    const page = await browser.newPage();
    await page.goto(link);

    await page.screenshot({ path: `screenshots/${title}.png` });
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的位置是一个简单的对象，包含一个标题(商店的名称)，以及一个格式为[https://maps.google.fr/?daddr=50.637,2.412](https://maps.google.fr/?daddr=50.637,2.412)的谷歌地图 URL。

## 解决问题

虽然这是一个好的开始，但目前的版本还有一些小问题。

### 截图及网络

首先，截图可能会在谷歌地图磁贴完全加载之前拍摄。但像往常一样，木偶师已经覆盖了我们，我们可以告诉他等到没有更多的网络活动！

```
await page.goto(link, {"waitUntil" : "networkidle0"}); 
```

Enter fullscreen mode Exit fullscreen mode

### 匿名 Chrome 用户

其次，因为我们运行的是未登录的 Chrome，谷歌地图的截图包含了 cookie 免责声明以及一些**‘你想选择加入吗……’**弹出窗口。

[![Here is an example](img/6f10a1fbbe34ba6565257bb85e5db941.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bIOXVoMQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7B%20%img/2018-04-05-puppeteer-google-maps-carrefour-google-popups.png%22%20%7C%20absolute_url%20%7D%7D)

我们通过在 DOM 中查找并点击这些弹出窗口来解决这个问题。你可能也可以在 Chrome 会话中使用现有的 cookies，但我想走捷径:)。

```
//Removing popups
...
await page.click(".widget-consent-button-later");
await page.click(".section-homepage-promo-text-button");
... 
```

Enter fullscreen mode Exit fullscreen mode

另一个问题是，在第一页之后，隐私弹出窗口可能不再显示。为了解决这个问题，我只需在点击它之前检查元素是否存在:

```
async function clickIfExists(thePage, selector){
    if (!!(await thePage.$(selector))){
        await thePage.click(selector);
    }
}
...
clickIfExists(page, ".widget-consent-button-later");
clickIfExists(page, ".section-homepage-promo-text-button");
... 
```

Enter fullscreen mode Exit fullscreen mode

### 调整谷歌地图视图

最后一个问题是谷歌地图的默认视图是“地图”视图。我想看卫星风景！我还想控制视图的缩放级别，这样我就可以看到停车场上的汽车。为了做到这一点，我瞎折腾了一阵，找到了相应的谷歌地图选项。

使用查询中的“z=level”控制缩放级别，使用“t=type”选项控制地图类型，sattelite 为“k”(go figure)。这给了我们以下简便的方法

```
function transformUrl(link){
    // https://maps.google.fr/?daddr=50.637,2.412 to https://maps.google.fr/?ll=50.637,2.412&z=16&t=k

    return link.replace("daddr=", "ll=") + "&z=16&t=k";
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦这些都完成了，我们的工具就准备好了，我们可以运行它了。看到它找到所有位置是美丽的！

**[在这里查看一下脚本运行的短视频](https://photos.app.goo.gl/Bq5DUnWOH6HfFSoa2)**

## 添加相关的哨兵图像

谷歌地图很棒，但它只给了我们一个快照，我不知道什么时候会出现新的图像。为了能够研究变化，我想研究公开可用的卫星数据。

我名单上最新的是 **[哨兵 2](https://en.wikipedia.org/wiki/Sentinel-2)** ，10 米分辨率，7 天逾越节时间。

为了找到给定位置的相关图像，我需要将纬度/经度坐标转换到 **[军事网格参考系统](https://en.wikipedia.org/wiki/Military_Grid_Reference_System)** 。

这可以使用一个名为 [usng.js](https://github.com/codice/usng.js/) 的库相对容易地完成。

对于给定的位置，我们可以使用下面的代码片段来检索 MGRS 坐标

```
const usng = require('usng/usng.js');
const precision = 1;
const converter = new usng.Converter();
const mgrsCoord = converter.LLtoMGRS(lat, lon, precision);
console.log(mgrsCoord); //30TXT 
```

Enter fullscreen mode Exit fullscreen mode

最后，为了获得相关的图像，我们可以将这些新的坐标与 AWS 上的 [open Sentinel datafeed 结合起来，后者方便地采用 REST 格式。

到阿格的网址，我们可以做一些类似
的事情](https://aws.amazon.com/public-datasets/sentinel-2/)

```
const sentinelFeed = `http://sentinel-s2-l1c.s3-website.eu-central-1.amazonaws.com/#tiles/${mgrsCoord.substring(0, 2)}/${mgrsCoord.substring(2, 3)}/${mgrsCoord.substring(3)}/`; 
```

Enter fullscreen mode Exit fullscreen mode

## 下一步

最后，我已经将所有这些结果耦合到一个数据库中。只有 324 个结果，所以像 [lowdb](https://github.com/typicode/lowdb) 这样的简单工具将会创造奇迹。为了方便浏览，我添加了一个非常粗糙的 index.html 文件，显示所有收集到的数据。这将有助于进一步找到我想首先查看的哨兵数据和位置。

你可以在这里看到文件(不过要小心，这个文件有 300Mb 大，里面有很多截图！).

## 结束

你可以在这里查看完整代码[。它不是有史以来最漂亮的，因为它只能跑一次。但是它完成了任务！](https://gitlab.com/jlengrand/carrefour)

希望你在我的最后一个周五晚上享受了这个偷峰:)。