# 为你的域名创建定制的谷歌搜索引擎，并以 JSON 格式获取搜索结果

> 原文：<https://dev.to/jochemstoel/create-custom-google-search-engine-for-your-domains-and-fetch-results-as-json-69o>

我决定在睡觉前写下这个简单的 5 分钟指南。

## 为你的网站域名创建一个定制的谷歌搜索引擎，并且几乎不用任何代码就能以编程方式访问它。

导航到[https://cse.google.com/cse/all](https://cse.google.com/cse/all)，在那里你可以创建一个定制的搜索引擎。点击“添加”按钮，并提供一个或多个网站进行搜索。在这种情况下，只需将 dev.to/*的 T2 添加到列表中。
如果你*启用*名为“搜索整个网页”的设置，如果没有找到(或没有找到足够的内容)，你的搜索结果将会增加。这意味着*禁用*以确保它只会显示来自您的网络域名的结果。

[![s1](img/f6eb29188304b4402c6c1e39efb8b40f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6Itm9mMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vb01s68vf05j86jf6kat.png)

如果你想通过简单的复制/粘贴在网页上嵌入你的自定义搜索引擎，那么你可以在 7 种不同的搜索引擎布局中进行选择。我个人比较喜欢紧凑型的。这些布局显示广告。

[![s2](img/42c7de77cfbd6d8b5eb47e44a8c5da6e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NI0lANoe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jn5b89yp1ei8k9gz945j.png)

编程访问有两个选项。选项*自定义搜索 JSON API* 是免费的，每天最多查询 10，000 次。这是你在这种情况下想要的。

> 如果您的自定义搜索引擎仅限于搜索特定的站点(10 个或更少)，您可以使用自定义搜索站点受限 JSON API。这个 API 类似于 JSON 定制搜索 API，只是这个版本没有每日查询限制。自定义搜索站点受限的 JSON API 请求每 1000 次查询花费 5 美元，并且没有每日查询限制。您可以在 API 控制台中注册计费。

### 你需要什么

API *key* (此处获取:[https://developers . Google . com/custom-Search/JSON-API/v1/introduction](https://developers.google.com/custom-search/json-api/v1/introduction))
搜索引擎标识符 *cx* (可以在你的搜索引擎公开网址中找到:[https://cse.google.com/cse?CX = 009833334622897458665:rtvizlbvdpk](https://cse.google.com/cse?cx=009833334622897458665:rtvizlbvdpk)

要在搜索引擎中查询“开源”，只需向[https://www.googleapis.com/customsearch/v1?key=YOUR_API_KEY&CX = SEARCH _ ENGINE _ ID&q = open+source](https://www.googleapis.com/customsearch/v1?key=YOUR_API_KEY&cx=SEARCH_ENGINE_ID&q=open+source)发出 GET 请求

假设你掌控了一切。向 querystring 添加一个 *sort=date* 参数，以便按日期对结果进行排序(最新的优先)。正如你在这张截图中看到的，谷歌很快发现了变化。

[![s3](img/a32769fbcb64bbd49e6d0d76102d74c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sg5LELXU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dzkq180ifcuav7tkmhtj.png)T3】

```
/* since this is a public API, it permits cross origin XMLHttpRequests from the browser */
fetch('https://www.googleapis.com/customsearch/v1?key=YOUR_API_KEY&cx=SEARCH_ENGINE_ID&q=open+source&sort=date').then(response => response.json()).then(json => {
   // json.items has the results 
}).catch(console.error) 
```

Enter fullscreen mode Exit fullscreen mode

ZzzzZZ..