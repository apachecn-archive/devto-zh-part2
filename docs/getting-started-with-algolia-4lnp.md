# 开始使用 Algolia

> 原文：<https://dev.to/algolia/getting-started-with-algolia-4lnp>

哦，嘿，你好👋！你有过从零开始做搜索的痛苦吗？或者当你的项目经理说“你知道什么最棒吗?”时的那种挫败感？如果我们能在网站上有一个搜索栏就好了！”你的第一个想法是...

[![awwcmon](img/928a0465e941d11484dbd563b0cf7bf1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xSm3NWP4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://www.reactiongifs.com/r/hmwrk.gif)

是的，我以前已经感受过太多次了。老实说，我像躲避瘟疫一样快速跑了几圈，因为即使我把搜索排序好了，我也觉得它不是很好，最重要的是，我会在文档中思考 *wtf，那个模块应该放在哪里？？*真的，不是超级好玩。

然而现在，我们有一些很棒的工具和服务，让这变得更容易。RIP 建筑搜索从零开始。啊，我喜欢科技。让我的生活一天比一天轻松。

这是我开始玩并最终加入阿尔戈利亚队的原因之一。我不希望这篇文章成为你读到“哦，上帝，她在向我推销”的那种文章。不，我真的很乐意与你分享我在 Algolia 入门时学到的东西，以及你如何站起来开始编码。因此，让我们深入了解一些让搜索做好准备的步骤。

# 获取您的 API 密钥

[![algolia keys marked up](img/4a0db95d386d4111cc80a08efa826382.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RDmWaa8U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.glitch.com/45e6d35c-2e10-4020-8ad3-d5f1b9d3aae6%252FalgoliaAPIkeysMarkedUp.png%3F1514567735627)

首先在[阿尔戈利亚](https://www.algolia.com/cc/devto)创建一个账户。然后从您的[仪表盘](https://www.algolia.com/licensing)中获取您的凭证。你需要复制你的`App Id`、`Search Only API Key`和`Admin API Key`。

完成后，将它们添加到您正在使用的`.env file`中，这样您的应用程序就知道如何连接到您的 Algolia 应用程序和索引。嘣！那是困难的部分。

# 连接您的数据源

如果您已经准备好了数据，我们可以从创建一个函数来调用该 url 并将其推入索引开始。让我们在这里使用 JavaScript 作为我们的语言。

```
const data_url = "https://raw.githubusercontent.com/algolia/datasets/master/movies/actors.json"

function indexData(data_url){
  return axios.get(data_url,{})
  .then(function(response){
    console.log(response.data[0]);
    return;
  })
  .catch(function(error) {
      console.warn(error)
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将在这个函数的基础上构建，但是现在，我们传递它，记录数据中的第一条记录，然后从循环中返回，这只是一个循环。我们在这里使用 axios 进行 api 调用。Axios 是一个 Javascript 库，用于从 node.js 发出 http 请求或从浏览器发出 XMLHttpRequests，它支持 JS ES6 自带的 Promise API。与其他方法相比，使用这种方法的优势在于它可以自动转换 JSON 数据。

# 为 Algolia 准备数据

现在我们正在拨打电话，让我们开始使用我们之前设置的 Algolia 帐户，并将数据上传到我们的索引中！我们将分两步完成，首先，我们将迭代从 axios.get 调用返回的数据，并创建一个对象数组。这将让我们只使用我们想要在索引中突出显示的数据。然后，一旦完成，我们可以发送到 Algolia 的索引..

*步骤 1:* 让我们创建一个函数，通过传递 axios.get 调用的响应来处理上传，而不只是返回一个成功响应。

```
function indexData(data_url){
  return axios.get(data_url,{})
  .then((response) => {
      return dataToAlgoliaObject(response.data)
    })
  .then(function(response){
    return;
  })
  .catch(function(error) {
      console.warn(error)
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

现在在这个函数中，我们想要遍历我们的数据点并创建我们的 algolia 对象，这应该是一个非常简单的循环。

```
function dataToAlgoliaObject(data_points){
  var algoliaObjects = [];
  for (var i = 0; i < data_points.length; i++) {
    var data_point = data_points[i];
    var algoliaObject = {
        objectID: data_point.objectID,
        name: data_point.name,
        rating: data_point.rating,
        image_path: data_point.image_path,
        alternative_name: data_point.alternative_name
      };
    algoliaObjects.push(algoliaObject);
  }

  return algoliaObjects;
} 
```

Enter fullscreen mode Exit fullscreen mode

*第二步:*现在我们已经构建好了我们的对象，它们已经准备好被送往阿尔戈利亚了！ [![kermit party](img/dfec92c63612a9f50f664df6edc04675.png)](https://i.giphy.com/media/D1iAIpluG6ygE/giphy.gif) 
让我们用 indexData 函数来改变一些事情。由于 axios promise 结构的原因，我们可以在调用中添加一个`.then`，并使用`async`和`await`来确保在我们上传数据之前不会出现任何问题。

```
function indexData(data_url){
  return axios.get(data_url,{})
  .then((response) => {
      return dataToAlgoliaObject(response.data)
    })
  .then(async (response) => {
     await sendDataToAlgolia(response);
     return;
  })
  .then(function(response){
    return;
  })
  .catch(function(error) {
      console.warn(error)
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

# 将数据发送到 Algolia

现在，让我们编写`sendDataToAlgolia`函数。这里是我们将利用之前放在我们的`.env`文件中的键的地方。我们还需要确保我们有初始化索引的东西，并传递给它我们想要用来存储数据的索引的名称。由于我们使用的数据集是关于电影演员的，这似乎是一个足够好的名字。

```
const algoliaClient = algoliasearch(process.env.ALGOLIA_APP_ID, process.env.ALGOLIA_ADMIN_API_KEY);
const algoliaIndex = algoliaClient.initIndex("movie-actors");  

function sendDataToAlgolia(algoliaObjects){
  return new Promise((resolve, reject) => {
    algoliaIndex.addObjects(algoliaObjects, (err, content) => {
      if(err) reject(err);
      resolve();
    })
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

# 设置您的设置

我们有数据了！但是，现在我们想告诉 Algolia 我们希望如何使用这些数据。我们可以通过仪表板或代码做到这一点。我个人喜欢通过代码来做，所以让我们在这里回顾一下。我们有很多选择，但是我们可以做一些基本的事情:

*searchableAttributes* :在此列出您希望从您创建的算法对象中搜索的内容
*attributes 高亮显示*:高亮显示您正在搜索的文本
*customRanking* :选择您的数据将如何显示，desc()或 ASC()
*attributesToRetrieve*:返回这些属性以便在搜索结果中显示

```
async function configureAlgoliaIndex(){
  algoliaIndex.setSettings({
    searchableAttributes: [
      'name'
    ],
    attributesToHighlight: [
      'name'
    ],
    customRanking: [
      'desc(rating)'
    ],
    attributesToRetrieve: [
      'name', 
      'rating',
      'image_path'
    ]
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们成功地将数据上传到索引中，就让我们添加这个函数。

```
function indexData(data_url){
  return axios.get(data_url,{})
  .then((response) => {
      return dataToAlgoliaObject(response.data)
    })
  .then(async (response) => {
     await sendDataToAlgolia(response);
     return;
  })
  .then(async () => {
     await configureAlgoliaIndex();
     return;
  })
  .then(function(response){
    return;
  })
  .catch(function(error) {
      console.warn(error)
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

哇，现在我们的索引中有了我们想要的数据。所以，我们已经完成了后端的工作，现在到了人们可以*看到*我们的甜甜数据并搜索它的部分。

# 连接前端

Algolia 有一种叫做 widgets 的东西，它允许我们快速地在 HTML 页面中添加部分，而无需编写大量新代码。只需几行 JavaScript 就可以轻松添加搜索栏和 Algolia 对象在页面上的位置等元素。我们去看看我们的客户档案。

我们希望首先创建一个可以在我们的应用程序中使用的即时搜索实例。您可以使用 cookies 将这些数据从服务器传递到前端，也可以将密钥放在前端。出于篇幅原因，我们将在这里显示密钥。

```
$(document).ready(function() {
  var instantsearch = window.instantsearch;

  // create an instantsearch instance with our app id and api key
    var search = instantsearch({
      appId: Cookies.get('app_id'),
      apiKey: Cookies.get('search_api_key'),
      indexName: Cookies.get('index_name'),
      urlSync: true,
      searchParameters: {
        hitsPerPage: 3
      }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们将一个搜索输入连接到您的 html，以便用户有一个搜索的地方。

```
search.addWidget(
  instantsearch.widgets.searchBox({
    container: '#search-box',
    placeholder: 'Search your favorite actors'
  })
); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们要添加我们的数据的结果，并且在返回语句中你可以改变你想要显示的内容。

```
 search.addWidget(
    instantsearch.widgets.hits({
      container: '#hits',
      hitsPerPage: 12,
      templates: {
        empty: `<div class="col-md-12" style="text-align: center;"> We didn't find any results for the search <em>\"{{query}}\"</em></div`,
        item: function(hit) {
          try {
            return `
              <div class="col-md-4" style="text-align: center;">
                <p> 
                  <h3 class="hit-text">${hit._highlightResult.name.value}</h3>
                  <img src="https://image.tmdb.org/t/p/w45/${hit.image_path}" height="50" width="50">
                </p>
                <p>
                  Rating: ⭐️ ${hit.rating} </p>

              </div>
            `;
          } catch (e) {
            console.warn("Couldn't render hit", hit, e);
            return "";
          }
        }
      }
    })
  ); 
```

Enter fullscreen mode Exit fullscreen mode

一个好的搜索体验不应该让用户被结果淹没，所以，让我们给返回的结果添加分页。

```
 search.addWidget(
    instantsearch.widgets.pagination({
      container: '#pagination'
    })
  ); 
```

Enter fullscreen mode Exit fullscreen mode

最后但同样重要的是…让我们开始搜索吧！这将实例化页面上的所有内容。

```
 search.start(); 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果你想跳过所有这些手动工作，你可以在 Glitch 上查看我们的 [quickstart 应用程序，重新混合它，你会用大约 5 分钟的努力获得所有这些代码和一个基本的工作应用程序。😉祝阅读愉快，希望这有所帮助！](https://glitch.com/~algolia-quickstart)

[![newgirl](img/9c936efd74a41ee9e3f94c74d68fd256.png)T2】](https://i.giphy.com/media/2uhnLivbj0S6A/giphy.gif)