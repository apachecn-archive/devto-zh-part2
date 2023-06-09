# 我的第一个 Azure 功能应用——Twitter 图像显示器

> 原文：<https://dev.to/raymondcamden/my-first-azure-function-app---twitter-image-displayer-4b1d>

所以我只花了[两个月](https://dev.to/raymondcamden/summer-plans---looking-at-azure-functions-2e5a)左右的时间，但我已经*最终*使用 Azure 函数作为后端构建了一个真正的(有点)应用程序。我警告过这需要一点时间，嗯-是的-我是对的。我尝试了更多的东西，最终我觉得我可以建造一些东西。在我可以构建我的应用程序之前，我还需要弄清楚一些事情。

### 添加 NPM 模块

如果你简单地打开控制台，在 Azure 门户添加 NPM 模块是非常简单的。这是在屏幕的底部:

[![Screen Shot](img/c9570744ef0667d5187ed6553960e2a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k225js-7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/09/az1.png)

这是一个和其他终端一样的控制台，我只需输入`npm i X`就可以安装我需要的任何软件包。NPM 抱怨丢失了一个 package.json，我想我可以先创建它，但是它运行得足够好，所以我不去管它。

我的假设是，如果你使用命令行的‘zip deploy’特性(正如我在这个[早先的帖子](https://dev.to/raymondcamden/testing-local-development-with-azure-functions-3blf-temp-slug-7504238)中所描述的)，那么你将需要在你的 zip 中包含`node_modules`或者在部署之后在命令中运行部署。

我仍在思考这个问题——但现在我知道如何让 NPM 模块进入，这才是最重要的。

### 处理机密

大多数无服务器平台(至少我合作过的平台)都有一个“秘密”的概念，或者在函数级定义一个你不想在代码中出现的值的方法。API 键就是一个很好的例子。secret 让您引用一个变量，而不是直接将某个特定的键粘贴到代码中。

在我看来，在 Azure Functions 中使用这一功能的最佳方式是通过应用程序设置。我之前提到过这个，但是 Azure Functions 做的一件事就是把你的函数分组到一个应用程序中。我越用这个，就越喜欢。这似乎是将无服务器功能组合在一起的一种很好的逻辑方式。Azure 的一个特点是能够指定不同的应用程序级别变量。您可以在 UI 中点击应用程序的根节点，然后简单地向下滚动到变量列表。你可以添加或编辑你认为合适的。

[![Application Settings](img/c732c0b0f3123a643df5384f8ceb289f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OfAlBo3R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/09/az2a.png)

在上面的截屏中，希望你能找出一些以`twitter_`开头的变量，这些是我为我的应用程序定制的值。添加后，您可以通过`process.env.X`引用它们，其中“X”是设置的名称。

### 建立 CORS

我必须敲定的最后一件事是安排 CORS。我已经创建了一个匿名端点(详见我博客文章[这里](https://dev.to/raymondcamden/http-stuff-with-azure-functions-and-more-pbn-temp-slug-7386639))，但是 CORS 是通过“平台设置”完成的:

[![Platform Settings](img/049cfd613bc2d442ee179507fa1dfdd4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wCEg1waE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/09/az3.jpg)

这将打开一个面板，您可以在其中配置允许哪些主机进行远程 HTTP 调用(无论如何通过浏览器)。在我的例子中，我将它设置为`*`以使它尽可能简单。

[![CORS Panel](img/f9f8171ab3080c5a3e3e0dc0597110b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zLO9VW8L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/09/az4.jpg)

好吧——最后——我到底造了什么？

### 申请

所以两年多前，我写了一篇博文([“从 Twitter 账户获取图片”](https://www.raymondcamden.com/2016/03/25/getting-images-from-a-twitter-account))，描述了一个 Node.js 应用程序，可以让你从 Twitter 账户查看图片。我关注(并让[建立了](https://twitter.com/randomcomicbook)Twitter 账户，这些账户会不时发布随机图片。我目前最喜欢的是分享电影剧照的[一个完美的镜头](https://twitter.com/oneperfectshot)。

我决定采用这个普通的 Node.js 函数，并将逻辑转换成 Azure 函数。代码相对简单:

```
const Twit = require('twit');
let T = null; 

module.exports = function (context, req) {
    context.log('Twitter test processed a request.');

    T = new Twit({
        consumer_key: process.env.twitter_consumer_key,
        consumer_secret: process.env.twitter_consumer_secret,
        access_token: process.env.twitter_access_token,
        access_token_secret: process.env.twitter_access_token_secret,
        timeout_ms: 60*1000, // optional HTTP request timeout to apply to all requests. 
    });

    if (req.query.account) {

        T.get('search/tweets', { q: 'from:'+req.query.account+'+filter:media', count: 100 }, function(err, data, response) {
            context.log('results '+data.statuses.length);

            let results = [];
            data.statuses.forEach((tweet) => {

                if(tweet.entities && tweet.entities.media && tweet.entities.media.length > 0) {
                    tweet.entities.media.forEach(function(m) {
                        results.push(m.media_url);  
                    });
                }

            });

            context.res = {
                status: 200, 
                headers: { 'Content-Type':'application/json' },
                body: results
            };
            context.done();

        });

    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
        context.done();
    }

}; 
```

Enter fullscreen mode Exit fullscreen mode

我使用 NPM 图书馆进行搜索。我在查询字符串中寻找一个`account`值，以确定从哪个 Twitter 帐户“抓取”图像。还要注意使用`filter:media`来请求带有图片的推文。

一旦得到结果，我就遍历它们，只添加图片 URL。它被填充到一个数组中然后我返回。

仅此而已。整个后端。又短又甜。

对于前端，我使用 [Vuetify](https://vuetifyjs.com/) 快速完成了一个 Vue.js 项目(坦白地说，这感觉有点过头了)。你可以在 GitHub 上看到前端([https://GitHub . com/cfjedimaster/web demos/tree/master/tweet images](https://github.com/cfjedimaster/webdemos/tree/master/tweetimages))但是我在这里分享一下应用逻辑:

```
const SERVICE = 'https://rcamden-azurefunctions.azurewebsites.net/api/tweets?account=';

const app = new Vue({
    el:'#app',
    data() {
        return {
            account:'',
            images:[],
            errorState:false,
            loading:false,
            noresults:false
        }
    },
    created() {
        console.log('ok, set stuff up');
        this.account = new URLSearchParams(document.location.search).get('account');
        if(!this.account) {
            this.errorState = true;
            return;
        }
        this.loading = true;
        console.log('account is '+this.account);
        fetch(SERVICE + encodeURIComponent(this.account))
        .then(res => res.json())
        .then(res => {
            this.loading = false;
            console.log('results', res);
            if(res.length === 0) {
                this.noresults = true;
            } 
            this.images = res;
        });
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我只是基于查询字符串值运行我的无服务器 API，并将结果添加到显示代码使用的数组中。我很快通过 Surge 把它发到网上，你可以在这里看到它:[http://black-and-white-frog.surge.sh/?account=oneperfectshot](http://black-and-white-frog.surge.sh/?account=oneperfectshot) 。如果您想尝试另一个帐户，只需更改最后的变量。很有可能我超出了我的 API 限制，这里有一个截图:

[![Demo results](img/c25fb0739239f3bafeeb3e9167e8890e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0cA-p3Bl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/09/az5.jpg)

### 总结起来

我受够了。嗯，不，差得远了，Azure 函数还有很多，但我终于可以构建一个简单的演示了，我对结果非常满意。当我听说 Visual Studio 代码扩展可能是使用 Azure 功能的“首选”方式时，我确实想更快地研究它，我也想尝试与其他 Azure 服务集成。我真的希望这是有帮助的，如果你有任何问题，请在下面给我留言！