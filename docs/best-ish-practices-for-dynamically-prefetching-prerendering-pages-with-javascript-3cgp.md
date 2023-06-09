# 用 JavaScript 动态预取和预呈现页面的最佳实践

> 原文：<https://dev.to/alexmacarthur/best-ish-practices-for-dynamically-prefetching-prerendering-pages-with-javascript-3cgp>

[资源提示](https://www.w3.org/TR/resource-hints/)(预加载、预取、预呈现等。)为前端性能游戏注入了一些新鲜的活力，尤其是浏览器越来越支持它们。对于传统的服务器呈现的应用程序来说，这些提示在知道何时实现它们方面非常简单。例如，当您知道页面稍后会需要关键资产时，您应该*预加载*关键资产，当您知道您将从同一个域加载一批资产时，您应该执行 *dns 预取*，比如那些来自 CDN 的资产。

然而，其他的暗示则更具挑战性。当*可能*用户在*未来*需要资产时，`prefetch`和`prerender`都关心加载资产。但是预测未来真的很难。因此，与其在 HTML 中硬编码这些提示，不如用 JavaScript 动态创建它们来响应用户的行为。

这真的不是什么新鲜事。事实上，有很多关于使用 JavaScript 做一些事情的讨论和方法，比如在悬停时[预呈现页面](https://css-tricks.com/prerender-on-hover/)，[在有意点击时预取页面](https://www.mskog.com/posts/instant-page-loads-with-turbolinks-and-prefetch/)，等等。这个演讲的大部分内容都集中在*如何做，*而不是探索你为什么要开始做这件事的任何基本原理。也就是说，回答几个问题有很大的澄清空间:

*用 JavaScript 做的时候……*

*   *…我如何知道我应该预取还是预读一页？*
*   在任何给定时间，我应该预取和/或预读多少页？
*   *…我怎么知道什么时候应该触发任何一个* `prefetch` *或者* `prerender` *？*

所有这些问题都围绕着一个微妙的平衡。如果我们预取和/或预渲染太多，我们就有增加用户网络带宽的风险，并可能最终损害的性能。如果太少(或不明智)，我们所有的工作都可能是徒劳的，因为没有人真正从中受益。对于您遇到的几乎每个应用程序，这种平衡的支点都有不同的定位，并且 JavaScript 在利用这些工具时提供的灵活性只会使它变得复杂。

根据我在实验中的发现，这里有一些杂七杂八的、*非常固执己见的*个人最佳实践，用于使用 JavaScript 动态且智能地生成`prerender`和`prefetch`提示。当我们浏览这些建议时，我们将编写一些代码来更好地实现所有这些。我们走吧！

## 1。*在*可能的*点击上*使用 JavaScript 预取或预呈现。

除非你的网站内容很少，只有几个链接，否则在页面加载后立即对页面上的每个链接进行全面预取或预呈现通常不是一个好主意。为什么？一次下载了大量数据，其中很多甚至永远不会被使用。相反，最好把这些任务留给你有理由相信用户将要点击某个东西的时候。

一个简单的方法是在悬停时触发一个动作:

```
document.querySelectorAll('a').forEach(link => {
    link.addEventListener('mouseover', (e) => {
        //-- Prefetch or prerender!
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

对于很多情况来说，我认为这很好。但是，如果你在一个有大量链接的页面上工作，或者如果你希望做一个资源密集型的`prerender`，它就没有足够的智能来给我们充分的信心，一个点击实际上即将发生。据我们所知，用户可能只是将鼠标从页面的一部分导航到另一部分。为这些悬停中的任何一个触发预取或预呈现可能有点太慷慨了。

所以，让我们通过用*意图*触发悬停上的某个东西来开始我们的预测游戏。以我们的复杂程度，我们可能会感到非常害怕，但为了简单起见，让我们假设一个人停留的时间越长，就越有可能点击某个东西。

为了帮助解决这个问题，我编写了 [ProbaClick](https://github.com/alexmacarthur/probaclick) ，这是一个小工具，当用户在某个东西上停留一段时间时，它会触发一个动作。默认情况下，该时间段是 500 毫秒。如果任何数量的悬停加起来达到该时间，将触发回调。让我们把它接到这里。一个小小的附带好处是，它的 API 将使我们的实现更加简洁。

```
//- Fire some action when any <a /> element is hovered over for some time.
ProbaClick('a', {
    callback: function (element) {
        //-- Prefetch or prerender!
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

言归正传，让我们开始为用户可能会点击的链接生成一些提示:

```
const makeHint = (href, type) => {
    let link = document.createElement("link");
    link.setAttribute("rel", type);
    link.setAttribute("href", href);
    document.head.appendChild(link);
    return href;
}

let hasBeenPrerendered = [];
let hasBeenPrefetched = [];

ProbaClick('a', {
    callback: function (link) {
        let href = link.getAttribute('href');

        //-- Only generate hint if we haven't done so already for this URL.

        if(!hasBeenPrerendered.includes(href)) {
            hasBeenPrerendered.push(makeHint(href, 'prerender'));
        }

        if(!hasBeenPrefetched.includes(href)) {
            hasBeenPrefetched.push(makeHint(href, 'prefetch'));
        }
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码摘要:当用户在一个链接上停留总共 500 毫秒时，我们将`prerender`和`prefetch`提示添加到文档的`<head>`中，但前提是这些提示在过去还没有生成。

这是朝着正确方向迈出的一步，但我们需要对此进行调整，特别是处理我们如何预呈现页面。

## 2。*不要*使用 JS 来*预渲染*每次加载超过一页。

就像我们的代码目前是如何设置的一样，很容易为每个可能被点击的链接生成一个`prender`提示。但是不要浪费时间，因为从技术上来说，每个浏览器实例只能运行一个 prerender 进程，这意味着我们做更多的事情只是在浪费时间。虽然我还没有找到任何官方文件证明所有的 T2 浏览器都是如此，但这似乎在很大程度上是一致的。[这篇不错的博文](http://ipullrank.com/how-i-sped-up-my-site-68-percent-with-one-line-of-code/)，[这个 StackOverflow 问题](https://stackoverflow.com/questions/26387612/html-performance-prerendering-of-multiple-pages)，还有我的亲身经历都印证了这一点。

在调整我们的代码来解决这个问题之前，让我们稍微深入一下`prerender`提示的行为。

**预渲染对硬编码 HTML 的影响**

在接触任何 JavaScript 之前，我尝试了一些硬编码的`prerender`提示，只是为了了解浏览器如何开箱即用。为了验证什么是真正的预渲染，我使用了 Chrome 的 Net Internals 工具:[Chrome://Net-Internals/# prerender](https://dev.tochrome://net-internals/#prerender)

以下是我的 HTML 文件头中的内容:

```
<link rel="prerender" href="https://www.typeitjs.com"/>
<link rel="prerender" href="https://www.typeitjs.com/docs"/> 
```

Enter fullscreen mode Exit fullscreen mode

当我加载页面时，只考虑了第一个*提示，第二个提示什么也没做。它似乎被忽视了。*

[!["Active Prerender"](img/0d714718456a663f169d2241b07c77b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yoPmcjBD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_86E490487AB28BDE8348427E1A325AD17E133E23ED436D4676C93BA3733A0D42_1540836925727_load-1.png)

有趣的是，在我刷新了同一个页面之后，*第二个* URL 被主动预呈现，第一个提示被指定为“重复”显然，浏览器会保留先前预呈现的页面，至少会保留一段时间。

[!["Active Prerender"](img/7721f118c203a6ed4dad08a6307863f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UC3JxXjf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_86E490487AB28BDE8348427E1A325AD17E133E23ED436D4676C93BA3733A0D42_1540836932646_load-2.png)

当我再次刷新时，两者都没有被预渲染。

[!["Active Prerender"](img/c3c04f91adc51a8810544fb5ace272f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_Vr4ZtHU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_86E490487AB28BDE8348427E1A325AD17E133E23ED436D4676C93BA3733A0D42_1540836936908_load-3.png)

这里学到的经验是，浏览器会预渲染它发现的第一个尚未缓存的提示(如果我们可以这样称呼它的话)。一旦 prerender 进程开始，以下任何`prerender`提示都是无用的。

**预渲染在 JavaScript 中的表现方式**

当我第一次尝试使用 JavaScript 来预呈现提示时，所有这些观察都清楚地解释了为什么它会变得非常奇怪。

为了进行实验，我设置了一个包含三个链接的页面，它们都有不同的`href`属性。页面上不存在资源提示。

```
<a href="https://www.typeitjs.com">Home</a>
<a href="https://www.typeitjs.com/docs">Docs</a>
<a href="https://github.com/alexmacarthur/typeit">GitHub</a> 
```

Enter fullscreen mode Exit fullscreen mode

然后，用 JavaScript，我等待每个链接被悬停。

```
document.querySelectorAll('a').forEach(link => {    
    link.addEventListener('mouseover', (e) => {
        let href = link.getAttribute('href');
        let prerenderLink = document.createElement("link");
        prerenderLink.setAttribute("rel", "prerender");
        prerenderLink.setAttribute("href", href);

        //-- Spit out the prerender hint.
        document.head.appendChild(prerenderLink);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

起初，我有一个我认为非常简单的假设:当一个链接被悬停时，相应的 URL 会被主动预呈现。这是正确的…某种程度上。

当我悬停在第*个*链接上时，一切正常。我用 Net Internals 工具和[谷歌的任务管理器](https://www.lifewire.com/google-chrome-task-manager-4103619)面板验证了这个 URL 是预加载的:

[![](img/ee27e103afe92d27a2f67b3d443f7eb6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--McP-9-0C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_86E490487AB28BDE8348427E1A325AD17E133E23ED436D4676C93BA3733A0D42_1540932164500_image.png)

但是在随后的盘旋中，令我沮丧的是，什么都没有改变。相同的初始链接保持“活动”，并且我没有通过悬停在任何其他链接上获得任何东西。事后看来，这非常有意义，因为我们通过硬编码提示发现了什么。当浏览器启动一个 prerender 进程时，您不能启动另一个进程。这使得有效地利用 JavaScript 来预呈现多个页面变得相当困难，因为一旦完成，它就完成了。

让我们将这一课应用到我们的代码中。为了对我们的提示生成更加负责，我们需要在完成一次之后防止任何后续的预呈现。

```
const makeHint = (href, type) => {
    let link = document.createElement("link");
    link.setAttribute("rel", type);
    link.setAttribute("href", href);
    document.head.appendChild(link);
    return href;
}

let hasBeenPrerendered = false;
let hasBeenPrefetched = [];

ProbaClick('a', {
    callback: function (link) {
        let href = link.getAttribute('href');

        //-- Only generate hint if we haven't done so already for this URL.

        //-- Only prerender if _nothing_ has been prerenderd before.
        if(!hasBeenPrerendered) {
            hasBeenPrerendered = makeHint(href, 'prerender');
        }

        if(!hasBeenPrefetched.includes(href)) {
            hasBeenPrefetched.push(makeHint(href, 'prefetch'));
        }
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是很有帮助的，但是因为我们只有一次机会预渲染一页，使用与预取完全相同的标准感觉有点，呃，不对。让我们做点什么。

## 3。*在你决定什么时候应该预读一页的时候，*会不会是*故意的？*

当选择动态预呈现页面时，会有相当大的风险。我们只有一次机会，如果我们错了，我们就白白浪费了用户的大量带宽。为了帮助我们更有意识地选择如何预渲染，让我们将它从当前的设置中完全抽出来，并增强我们用来扣动扳机的逻辑。

对于这个独立的逻辑集，ProbaClick 还允许您设置一个最大悬停计数来实现这一点，而不仅仅是增加触发动作之前的延迟。如果用户在元素上悬停一定次数，或者达到总悬停持续时间，则触发该事件。

```
const makeHint = (href, type) => {
    let link = document.createElement("link");
    link.setAttribute("rel", type);
    link.setAttribute("href", href);
    document.head.appendChild(link);
    return href;
}

let hasBeenPrefetched = [];
ProbaClick('a', {
    callback: function (link) {
        let href = link.getAttribute('href');

        if(!hasBeenPrefetched.includes(href)) {
            hasBeenPrefetched.push(makeHint(href, 'prefetch'));
        }
    }
});

let hasBeenPrerendered = false;
ProbaClick('a', {
    delay: 1000,
    count: 3,
    callback: function (link) {
        let href = link.getAttribute('href');

        if(!hasBeenPrerendered) {
            hasBeenPrerendered = makeHint(href, 'prerender');
        }
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

随着这一变化，我们将根据以下标准生成提示:

**预取**当用户集体悬停在一条链路上 500 毫秒时。 **Prerender** 当用户集体悬停在一个链接上 1000 毫秒，或者当他们已经悬停 3 次。

考虑到`prefetch`和`prerender`提示以不同的方式做不同的事情，这感觉更合适。

## 4。*不要*在别人的网站上预取或预读页面。

这主要是因为你没有建立另一个网站，所以你可能不知道它是如何构建的细节——包括它的页面权重、代码质量，甚至可能是它可能采用的一些不安全的做法。通过预取或预渲染别人的网站，你必然要承担相当大的风险。这种风险的程度显然因地点而异，但总的来说，对我来说，对实践说“啊”就足够了。

这在预呈现页面时尤其重要。如前所述，预呈现是资源密集型的，涉及的不仅仅是“获取”页面文档供以后呈现。一个`prerender`提示*预先呈现*页面，包括获取和执行页面的子资源。这是一个壮举。

假设你发表了一篇博客文章，鼓励人们向 GoFundMe 事业捐款，并且你的文章包含了一个指向捐款页面的链接。当有人点击那个链接时，为了加快后续的渲染，你可以*在后台*预渲染。但是不要，因为这些原因:

*   您可能没有意识到它的主 JavaScript 文件是一个 500MB 的庞然大物。这只是一个文件，在它被解析和执行之前。通过预渲染该页面，你只是最大限度地利用了用户当月的移动数据计划，而他们可能根本就没有访问过该页面。
*   您不知道该站点可能会使用哪种第三方脚本，更不知道它可能会对您自己的站点的性能和安全性产生什么影响。

因此，让我们修改一下*而不是*预取或预呈现任何属于不同域的链接。

```
const makeHint = (href, type) => {
    let link = document.createElement("link");
    link.setAttribute("rel", type);
    link.setAttribute("href", href);
    document.head.appendChild(link);
    return href;
}

const isExternalLink = (href) => {
    return !href.match(/^\//) && !href.includes(window.location.host)
};

let hasBeenPrefetched = [];
ProbaClick('a', {
    callback: function (link) {
        let href = link.getAttribute('href');

        if(isExternalLink(href)) return;

        if (!hasBeenPrefetched.includes(href)) {
            hasBeenPrefetched.push(makeHint(href, 'prefetch'));
        }
    }
});

let hasBeenPrerendered = false;
ProbaClick('a', {
    delay: 1000,
    count: 3,
    callback: function (link) {
        let href = link.getAttribute('href');

        if (isExternalLink(href)) return;

        if (!hasBeenPrerendered) {
            hasBeenPrerendered = makeHint(href, 'prerender');
        }
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 清理时间

不错！我们已经写好了代码的主要部分，但是让我们稍微整理一下:

```
let hasBeen = {
    prefetch: [],
    prerender: []
}

const makeHint = (href, type) => {
    let link = document.createElement("link");
    link.setAttribute("rel", type);
    link.setAttribute("href", href);
    document.head.appendChild(link);
    return href;
}

const isExternalLink = (href) => {
    return !href.match(/^\//) && !href.includes(window.location.host)
};

const maybeMakeHint = ({link, type, max = null} = {}) => {
    let href = link.getAttribute('href');

    if(isExternalLink(href)) return;
    if(hasBeen[type].includes(href)) return;
    if(max !== null && hasBeen[type].length >= max) return;

    hasBeen[type].push(makeHint(href, type));
}

ProbaClick('a', {
    callback: function (link) {
        maybeMakeHint({
            link, 
            type: 'prefetch'
        });
    }
});

ProbaClick('a', {
    delay: 1000,
    count: 3,
    callback: function (link) {
        maybeMakeHint({
            link,
            type: 'prerender',
            max: 1
        });
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了:几行未经测试的代码，遵循一些与使用 JavaScript 生成`prerender`和`prefetch`资源提示相关的自以为是的最佳实践。如果你有更好的方法来实现这里的任何东西，有任何补充，或者如果你对我说的话深感不快，请说出来！至少，我希望你能从这些想法中走出来:

*   “在我自己的应用程序中使用这些策略时，我确实需要注意很多事情。”
*   “我只是觉得我对这些提示的工作原理和区别有了更清晰的认识。”
*   “当我阅读这篇文章时，我感受到的普遍兴趣和兴奋程度足以让我在推特上发布它。”

感谢阅读！