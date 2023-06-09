# 使用 Azure 函数和漫威 API 来可视化角色历史

> 原文：<https://dev.to/raymondcamden/using-azure-functions-and-the-marvel-api-to-visualize-character-history-4m26>

我玩[漫威 API](https://developer.marvel.com/) 已经有一段时间了([“我所有的朋友都是超级英雄”](https://www.raymondcamden.com/2017/01/18/all-my-friends-are-superheroes)，[，“构建一个 Twitter 机器人来显示随机的漫画书封面”](https://www.raymondcamden.com/2016/02/22/building-a-twitter-bot-to-display-random-comic-book-covers)，[“将漫威 API 与 IBM Watson 一起使用”](https://www.raymondcamden.com/2015/05/26/using-the-marvel-api-with-ibm-watson)，[“以漫威 API 为例”](https://www.raymondcamden.com/2014/02/02/Examples-of-the-Marvel-API))，我发现自己会时不时地回头看看他们的数据库中有什么很酷的东西。不幸的是，看起来漫威最近并没有对他们的 API 做什么新的东西，但至少它仍然工作，我想这是值得的。几周前，我认为这将是一个有趣的实验，看看你是否可以自动可视化一个角色随着时间的变化。那么我是什么意思呢？

想想 1962 年蜘蛛侠的第一张照片…

[![Spider-Man comic cover from 1962](img/07e5f9b9ee66c432090097ca80291c83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VNfHnwhX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/mv1.jpg)

现在把它与 1988 年的这张精彩照片进行比较:

[![Spier-Man comic cover from 1988](img/e0f2288b50c974090669a60d950d8a16.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--guM2pNf1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/mv2.jpg)

最后是今年的封面:

[![Spider-Man comic cover from 2018](img/4b10dc5c2d21422dbc6bccc5cd160c65.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NjfZJbr8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/mv3.jpg)

我只是喜欢看到几十年来风格的发展，尤其是有这样一个标志性的人物。我决定尝试找出一种方法来自动完成这项工作，并将它显示给用户。现在，在我继续之前，让我声明一下，我不会“现场”运行这个演示。为什么？首先——我仍然不能 100%确定如何用 Azure 函数在免费层保持“安全”。上个月，我收到了一张 40 美元的账单，因为我在一个项目中做了一个错误的选择，虽然这是我的错，但我仍然感到有点心痛。其次，漫威本身对他们的 API 使用有限制。这当然是一个合理的限制，但也是我不想担心的事情。如果微软或漫威想要帮助我，就给我写信吧！我不会屏住呼吸。；)也就是说，我将要展示的所有代码都可以在我的 GitHub repo 中找到:[https://github.com/cfjedimaster/marvelcharacterovertime](https://github.com/cfjedimaster/marvelcharacterovertime)

## 后端

我的后端是用 Azure 函数构建的。这是我第一次使用 [Visual Studio 代码集成](https://code.visualstudio.com/tutorials/functions-extension/getting-started)并且他妈的做得很好。我想大概花了 20 分钟左右的设置时间，但是一旦完成，当我有更新的时候，这是一个快速部署到 Azure 的命令。在本地运行代码也很容易。从我目前有限的经验来看，这是使用 Azure 函数的最佳方式(显然，如果你是一个代码用户的话)，也是我未来打算使用的方式。

我的应用程序只需要两个特定的特性——搜索字符的能力和随时间推移查找相关封面的能力。先说字符搜索端点:

```
const rp = require('request-promise');
const API_PUB_KEY = process.env.API_PUB_KEY;
const API_PRI_KEY = process.env.API_PRI_KEY;

const crypto = require('crypto');

module.exports = async function (context, req) {

    if (req.query.name) {

        let name = req.query.name;
        let baseUrl = `https://gateway.marvel.com:443/v1/public/characters?nameStartsWith=${encodeURIComponent(name)}&apikey=${API_PUB_KEY}`;

        let ts = new Date().getTime();
        let hash = crypto.createHash('md5').update(ts + API_PRI_KEY + API_PUB_KEY).digest('hex');
        baseUrl += "&ts="+ts+"&hash="+hash;

        //console.log('baseUrl', baseUrl);

        return rp({
            url:baseUrl,
            json:true
        }).then(res => {
            //console.log(res.data);
            let results = [];

            if(res.data.total > 0) {
                results = res.data.results.map(r => {
                    return { id:r.id, name: r.name, thumbnail: r.thumbnail.path + '.' + r.thumbnail.extension };
                });
            } 

            context.res = {
                // status: 200, /* Defaults to 200 */
                body: results,
                headers: { 
                    'Content-Type':'application/json',
                    'Access-Control-Allow-Origin': '*'
                }
            };

        });
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string"
        };
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是最简单的，因为它需要做的就是使用带有参数`nameStartsWith`的[字符](https://developer.marvel.com/docs#!/public/getCreatorCollection_get_0)端点。这将让你输入一个值，如'蜘蛛'，并得到结果。时间和散列的东西只是漫威 API 安全的一部分，坦率地说，这感觉像是多余的，但它确实存在。我得到结果，然后把它向下映射一点，去掉很多我不需要的数据。这使得 Azure 函数和我的 web 应用程序之间的通信更加快捷，因为我不会返回不必要的数据。

酷！到目前为止一切顺利，我相信下一个端点也会一样简单，对吗？哈！

漫威没有一个 API 可以返回带有某些角色的封面，但是你可以在漫画中搜索一个角色，我想这应该够接近了。为了得到我的数据，我想我应该搜索一年的数据，包括一个字符的结果。不幸的是，当一个角色第一次出现时，角色 API 不会返回。所以为了估算一下，我搜索了从 1950 年到 2090 年的数据。请随时到 2090 年来找我投诉。

我按照销售日期对这些结果进行排序，然后使用第*个第*个结果来指示角色第一次出现的时间。我没有对此进行大量测试，但它似乎与蜘蛛侠很好地合作。

一旦你有了这些，你就可以从最初的一年到今年的每一年索要漫画。基本上就是这样。下面是代码:

```
const rp = require('request-promise');
const API_PUB_KEY = process.env.API_PUB_KEY;
const API_PRI_KEY = process.env.API_PRI_KEY;

const crypto = require('crypto');

module.exports = async function (context, req) {

    /*
    First idea:
    first we do a comic search with a date range of 1950-2090 in an attempt to find the first comic
    this gives us X. We then get 10 comics from X to THIS_YEAR

    Second idea:
    go from THIS_YEAR to THIS_YEAR-- until we get nothing back. 
    however, it's possible for a character to 'go away' for a few years. so maybe we would allow for '3 strikes'
    of no results and only stop when we've hit that limit
    */

    if (req.query.id) {
        let id = req.query.id;

        return new Promise((resolve, reject) => {

            // ok - try to get first issue
            let baseUrl = `https://gateway.marvel.com:443/v1/public/comics?dateRange=1950-01-01%2C2090-01-01&characters=${id}&orderBy=onsaleDate&apikey=${API_PUB_KEY}`;

            let ts = new Date().getTime();
            let hash = crypto.createHash('md5').update(ts + API_PRI_KEY + API_PUB_KEY).digest('hex');
            baseUrl += "&ts="+ts+"&hash="+hash;

            //console.log('baseUrl', baseUrl);

            rp({
                url:baseUrl,
                json:true
            }).then(res => {

                let firstDate = '';

                if(res.data && res.data.results && res.data.results.length > 0) {
                    let firstResult = res.data.results[0];
                    // from what I know the type is always onsaleDate
                    firstDate = new Date(firstResult.dates[0].date).getFullYear();
                }

                // no firstDate?
                if(firstDate === '') {
                    context.res = {
                        body: {result:[]},
                        headers: { 'Content-Type':'application/json' }
                    };
                    resolve();
                    //not sure I need this
                    return;
                }

                //temp hack:
                //firstDate = 2015;

                //get this year
                let thisYear = new Date().getFullYear();

                console.log('going to go from '+firstDate+ ' to '+thisYear);
                let coverCalls = [];
                for(let x = firstDate; x <= thisYear; x++) {
                    let dateStr = x + '-01-01%2C'+ x + '-12-31';
                    let thisUrl = `https://gateway.marvel.com:443/v1/public/comics?dateRange=${dateStr}&characters=${id}&orderBy=onsaleDate&limit=10&apikey=${API_PUB_KEY}`;

                    let ts = new Date().getTime();
                    let hash = crypto.createHash('md5').update(ts + API_PRI_KEY + API_PUB_KEY).digest('hex');
                    thisUrl += "&ts="+ts+"&hash="+hash;

                    console.log(thisUrl);

                    coverCalls.push(rp({
                        url:thisUrl,
                        json:true
                    }));

                }

                Promise.all(coverCalls).then((data) => {
                    console.log('in the all for calling covers');

                    let results = [];

                    //each index of data is year X, we will return the: year, [title, cover]
                    for(var x=0;x<data.length;x++) {
                        let item = {};
                        item.year = x + firstDate;
                        item.comics = [];
                        for(var y=0;y<data[x].data.results.length;y++) {
                            let comic = {};
                            comic.title = data[x].data.results[y].title;
                            comic.cover = data[x].data.results[y].thumbnail.path + '.' + data[x].data.results[y].thumbnail.extension;
                            item.comics.push(comic);
                        }
                        results.push(item);
                    }

                    context.res = {
                        body: {result:results},
                        headers: { 
                            'Content-Type':'application/json',
                            'Access-Control-Allow-Origin': '*'
                        }
                    };
                    resolve();

                }).catch(e => {
                    console.log('error', e);
                });

            });

        });

    }
    else {
        context.res = {
            status: 400,
            body: "Please pass an id (for the character) on the query string"
        };
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到我使用了一组承诺，这样我就可以一次快速发出一堆请求，然后等待它们全部完成。漫威没有“节流”限制，所以这对于其他 API 来说可能并不总是有效。最后，请注意，我再次将结果映射回来，以限制发送到前端的数据。

### 前端

前端是一件简单的事情-提示字符，显示结果，然后随着时间的推移呈现漫画封面。我使用 Vue.js 构建了它，并从我的好友 [Garth](https://garthdb.com/) 那里获得了相当多的设计帮助。我真的希望我能为 yall 运行这个，但是正如我上面说的，我不能在 API 的限制下免费运行。

让我们从字符搜索结果屏幕开始:

[![Example of app comic character result screen](img/fb6e5c17ed253fbde8381aee90f8a3b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TSZIoIsv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/mv4.jpg)

在你选择了一个角色后，我就点击了后端，坦白地说，这真的非常快，尤其是考虑到像蜘蛛侠这样的人拥有如此多的数据。以下是一长串结果中的四个截图:

[![Comic results](img/f28eb02ca205e0a8c12d6ccc126244e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tcvXMPt9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/mv5.jpg)

[![Comic results](img/ab8a73cf2a70b3d6b2271241fb068ef7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pooktgpE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/mv6.jpg)

[![Comic results](img/902d54ee3fdbb6555af2f9b94d84a9e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wO2Uai6g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/mv7.jpg)

[![Comic results](img/526467d94d6e08e05df62b7920cef1c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NkaNo3Ss--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/mv8.jpg)

代码非常简单。布局如下:

```
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  
  <meta name="description" content="">
  <meta name="viewport" content="width=device-width">
  <style> [v-cloak] {display: none};
        </style>
  <link href="https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400" rel="stylesheet">
  <link rel="stylesheet" href="app.css">
</head>

<body>
  <div id="app" v-cloak>
    <!-- block used to have you search for a char -->
    <div v-if="needCharacter">
      <form class="searchForm" @submit="search($event)">
        <fieldset class="searchForm__fieldset" :disabled="characterSearching">
          <label class="searchForm __label">Enter a character name: <input class="searchForm__ input" v-model="character" type="search"></label>
          <button class="searchForm __input searchForm__ submit" @click="search($event)">Search</button>
          <button class="searchForm __input searchForm__ reset" @click="reset()">Reset</button>
        </fieldset>
      </form>
      <p v-if="noCharacters">I'm sorry but I couldn't find any matches for that search.</p>
      <div v-if="characters">
        <ul class="charactersList">
          <li class="characterCard" v-for="character in characters" @click.prevent="loadChar(character)">
            <div class="characterCard__thumb" v-bind:style="{ backgroundImage: `url(${character.thumbnail})` }"></div>
            <div class="characterCard__name"></div>
          </li>
        </ul>
      </div>
    </div>
    <!-- you have a char, we're getting covers now -->
    <div v-if="loadingCharacter">
      <div class="loadingCovers" v-if="loadingCovers">
        <i>Loading covers...</i>
      </div>
      <div class="coversView" v-if="covers">
        <div class="breadcrumbs">
          <h1><a href="./">Search</a> &gt; </h1>
        </div>
        <div v-for="coverData in covers">
          <h2 class="covers__year"></h2>
          <ul class="coversList">
            <li class="coverCard" v-for="comic in coverData.comics">
              <img :src="comic.cover" class="coverCard__image" :title="comic.title">
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
  <script src="https://unpkg.com/vue"></script>
  <script src="app.js"></script>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

而这里对应的 Vue 代码:

```
//const searchAPI = 'http://localhost:7071/api/searchCharacters?name=';
//const coverAPI = 'http://localhost:7071/api/getCharacterCovers?id=';
const searchAPI = 'https://marvelcomicchar.azurewebsites.net/api/searchCharacters?name=';
const coverAPI = 'https://marvelcomicchar.azurewebsites.net/api/getCharacterCovers?id=';

const app = new Vue({
  el: '#app',
  data: {
    needCharacter: true,
    character: '',
    currentCharacter: '',
    characterSearching: false,
    noCharacters: false,
    characters: [],
    loadingCharacter: false,
    loadingCovers: true,
    covers: [],
    noCovers: false
  },
  methods: {
    search: function(event) {
      if (event) event.preventDefault()
      if (this.character === '') return;
      console.log('search for ' + this.character);
      this.noCharacters = false;
      this.characterSearching = true;
      fetch(searchAPI + encodeURIComponent(this.character))
        .then(res => res.json())
        .then(res => {
          this.characterSearching = false;
          if (res.length === 0) this.noCharacters = true;
          console.log(res);
          this.characters = res;
        });
    },
    loadChar: function(c) {
      console.log('load', c.id, c.name);
      this.currentCharacter = c;
      this.needCharacter = false;
      this.loadingCharacter = true;
      fetch(coverAPI + encodeURIComponent(c.id))
        .then(res => res.json())
        .then(res => {
          this.loadingCovers = false;
          if (res.length === 0) this.noCovers = true;
          //console.log(res);
          // todo, remove http://i.annihil.us/u/prod/marvel/i/mg/b/40/image_not_available.jpg
          this.covers = res.result;
        });
    },
    reset: function() {
      console.log('reset here')
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

除了一些 Ajax 调用之外，这里真的没有什么了。我肯定还能做更多的事情(正如评论本身所说)，但这已经完成了工作。

如果你想看完整的结果集，我可以说“打印成 pdf”的版本有 150 页。这部分是因为 PDF 格式中有一些奇怪的东西，使它占据了更多的垂直空间，但随着时间的推移，看看近 60 年的蜘蛛侠真的有点令人印象深刻。

所以我感觉很糟，就快速黑了一下。我用 devtools 复制了所有的图片 URL，然后我快速地用 CodePen 渲染了所有的图片——将近 500 个。您可以在此处查看:

[https://codepen.io/cfjedimaster/full/QJwyOB/](https://codepen.io/cfjedimaster/full/QJwyOB/)