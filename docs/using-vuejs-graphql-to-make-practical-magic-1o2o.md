# 用 VueJS + GraphQL 制作超异能快感

> 原文：<https://dev.to/gaijinity/using-vuejs-graphql-to-make-practical-magic-1o2o>

# 使用 VueJS + GraphQL 制作超异能快感

### 用 Vue JS + GraphQL 制作一些实用的 mag ic 来解决日常问题，如自动完成搜索，用无头手工 CMS 服务器保存表单子信息

安德鲁·韦尔奇

[![Vuejs Graphql Craft Cms Magic](../Images/54b481574276a473dcf9be0611abb9b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3flNMQlT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/vuejs-graphql-craft-cms-magic.jpg)

新技术的进步可能令人畏惧。我们听说过像 [Vue JS](http://vuejs.org/) 和 [GraphQL](http://graphql.org/) 这样闪亮的新事物，但是一天中学习每一件新事物的时间是有限的。

所以我认为更实际的方法是弄清楚这些技术如何帮助我们解决我们每天面临的现实世界的问题。

<aside>This arti­cle presents two prac­ti­cal exam­ples using Vue­JS + GraphQL that will feel like magic</aside>

下面是我们要解决的两个问题:

*   **自动完整搜索** —在用户输入时动态显示结果列表
*   **接触表格子信息保存** —获取表格子信息数据并将其保存到后端数据库的能力

所以，让我们认真对待这个问题，谈谈我们完成这项工作所需的工具。

## 工具 ing

每个项目都至少需要一点工具；我试着把它保持在一分钟之内，这样我们就可以专注于考试。但是我们仍然需要一些。

[![Craft Cms Vue Js Graphql Tooling](../Images/184e5682409fa8adee486d6969228b90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cOc0ypof--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-cms-vue-js-graphql-tooling.jpg)

以下是我们将在前端工具中使用的内容:

*   **[Vue JS](http://vuejs.org/)**——一个可逼近、可变、高性能的前端 JavaScript 框架
*   **[Axios](http://github.com/axios/axios)** —一个处理`http`请求的 JavaScript 库
*   **[开机自检 4](http://getbootstrap.com/)**——一个流行的 u lar CSS 框架，只是为了让我们的考试样片看起来不难看

为了简单起见，所有这些前端资源都将从 CDN 中获取。我使用 Boot strap 是因为我非常喜欢 [Tail wind CSS](http://tailwindcss.com/) ，我不想被示例中的 util ty-first CSS 所困扰。

如果你不熟悉 Vue JS，没关系。你可以用 jQuery、vanil la JS 或者尤尔喜欢的任何东西做同样的事情。这只是更多的工作；我们只是在这里使用 Vue JS 来使图表检查变得更容易。

对 Vue JS 的全面解释超出了本文的范围，但是如果您想了解更多，可以查看以下资源:

*   [学习 Vue 2:循序渐进](http://laracasts.com/series/learn-vue-2-step-by-step)
*   [探索来自趋势 JavaScript 框架的 Vue JS](http://devmode.fm/episodes/spelunking-into-the-vuejs-frontend-javascript-framework)
*   在 Craft CMS 上使用 Vue JS 2.0

    <aside>嘿，GraphQL 在哪里？</aside>

你可能正在看这个列表，并对自己说:“嘿，GraphQL 在哪里？”这里没有列出来是有原因的；GraphQL 是一个规范，而不是一个实现。所以根本不需要包含 JavaScript！

以下是我们将用于后端工具的内容:

*   **[Craft CMS](http://craftcms.com/)** —一款提供丰富内容创作体验的优秀 CMS
*   —Mark Huot 的 excel lend plu in simply 在 Craft CMS 之上提供了一个 GraphQL 层

示例将使用 [Craft CMS](http://craftcms.com/) 作为后端，但是 [JAM stack](http://jamstack.org/) 像 Vue JS + GraphQL 这样的技术的优点是后端并不真正匹配。你可以替换掉尤尔想要艾德在后端使用的东西！我们使用 Craft CMS 作为“无头”CMS，只是为了通过 API 提供我们的内容数据。

即使您没有使用 Craft CMS，本文中的几乎所有内容都适用。所以继续读下去吧！

## 自动完成搜索

很常见的情况是，我们可能希望为人们提供在搜索字段中键入内容的能力，并让它动态列出一系列结果。

对于这个例子，我们在 Craft CMS 中有一个`blog`部分，其中有一些样本数据。我们希望让人们在字段中键入内容，以找到与他们键入的内容相匹配的博客条目。

最终结果在前端看起来是这样的:

[![Auto Complete Search](../Images/f84baec680e865de1f7dfb544303d6d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nC6yiK0E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_2166x1244_crop_center-center_line/auto-complete-search.png)

在顶部我们有一个搜索区域，在它的下面，我们展示了一个动态的匹配列表，当他们输入博客条目的时候。下面是一些调试信息，可能有助于您了解正在发生的事情。

在这篇解释中，我将跳过一点，但是完整的源代码将在文章的末尾。

## 用于自动完成搜索的 Vue 实例

那么……我们该如何解决这个问题呢？让我们从定义制作这个 hap pen 所需的数据开始，并围绕它创建我们的 Vue 实例。

<aside>With Vue­JS, the DOM is a side-effect of your data, not the oth­er way around</aside>

这就是我喜欢 Vue JS 的地方。您将数据定义为应用程序的真实来源，而 HTML 结果只是它的副产品。

我们来看看:

```
 // Instantiate our Vue instance
    new Vue({
        el: '#demo',
        data: {
            searchApi: axios.create(configureApi(apiUrl, apiToken)),
            searchQuery: '',
            searchResults: {}
        },
        methods: {
            // Perform a search
            performSearch() {
                // If they haven't entered anything to search for, return nothing
                if (this.searchQuery === '') {
                    this.searchResults = {};
                    return;
                }
                // Set the variables we will pass in to our query
                const variables = {
                    sections: searchSections,
                    needle: searchPrefix + this.searchQuery,
                    limit: 5
                };
                // Execute the query
                executeQuery(this.searchApi, searchQuery, variables, (data) => {
                    this.searchResults = data.data.entries;
                });
            }
        }
    }) 
```

我们的数据非常简单，只包含:

*   `searchApi` —我们将使用的 Axios 实例通过`http``发送&接收 GraphQL`
*   `searchQuery` —用户正在寻找的搜索字符串
*   `searchResults` —反对他们的搜索结果(如果有的话)

`configureApi()`函数看起来像这样:

```
 // Configure the api endpoint
    const configureApi = (url, token) => {
        return {
            baseURL: url,
            headers: {
                'Authorization': `Bearer ${token}`,
                'X-Requested-With': 'XMLHttpRequest'
            }
        };
    }; 
```

它返回一个配置对象，我们可以传递给`axios.create()`，这样我们所有的`http`请求都有相同的基本设置。我们只是在创建我们自己的 Axios 实例，它预先配置了我们想要的设置。

以下是我们传入的设置:

```
 // Information needed for connecting to our CraftQL endpoint
    const apiToken = 'wwYfgLejf27AxoSmR0K3wUzFoj9Y96QSNTICvpPslO2l2JcNsjfRY9y5eIec5KhN';
    const apiUrl = '/api'; 
```

虽然这看起来有点复杂，但是如果我们有多个 API URLs 会怎么样呢？或者，如果我们对每种类型的 API 调用都有不同的要求，会怎么样呢？这使得以可重用的方式设置我们的 API 端点变得更加容易。

`apiUrl`被设置为默认的`/api` URL，CraftQL lis tens 会将其用于 GraphQL 请求。`apiToken`是一个[承载令牌](http://swagger.io/docs/specification/authentication/bearer-authentication/)，CraftQL 用它来授予权限在 Craft CMS 中读写数据。

在工艺管理中，您可以创建这些轴承令牌:

[![Craftql Bearer Rokens](../Images/1aca284c538c6e83a7a1f0c137d7047e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BYqz73pw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1428x604_crop_center-center_line/craftql-bearer-rokens.png)

并定义他们有哪些缺点:

[![Craftql Token Scopes](../Images/74b3b32e5ed175ed4d66a95b7ed15225.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nsqwEyjJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_2036x1290_crop_center-center_line/craftql-token-scopes.png)

这些都不是 Craft CMS 或 CraftQL 独有的；无论尤尔最终在后端使用什么，都会有一个访问 API 的 URL 和一个定义权限的 bear er 令牌。

## 用于自动完成搜索的 HTML

这就是我们的 Vue 实例。在我们使用`performSearch()`方法和 GraphQL 之前，让我们先来看看我们正在使用的 HTML 模板:

```
 <!DOCTYPE html>
<html>
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
</head>

<body>
<div class="container p-5">
    {% verbatim %}
    <form id="demo" autocomplete="off">
        <div class="form-group">
            <label for="searchQuery">Search:</label>
            <input v-model="searchQuery" v-on:keyup="performSearch()" id="searchQuery" class="form-control" type="text" />
        </div>

        <div class="form-group">
            <ul class="list-group">
                <li v-for="(searchResult, index) in searchResults" class="list-group-item">
                    <a v-bind:href="searchResult.url">{{ searchResult.title }}</a>
                </li>
            </ul>
        </div>

        <div class="form-group">
            <pre>data: {{ $data }}</pre>
        </div>
    </form>
    {% endverbatim %}
</div> 
```

所以这里没有什么太令人兴奋的；我们有来自 CDNs 的 JavaScript 和引导 CSS。

然后我们有了一个相当奇怪的表情。这只是一个[小树枝标签](http://twig.symfony.com/)，告诉 Craft CMS 不要处理它里面的任何东西。我们必须这样做，因为 Twig 和 Vue JS 使用相同的 mus tache ``{{ }}`` delim iters，我们希望在这里使用 Vue JS，而不是 Twig。

然后我们有一个输入，它通过`v-model`属性绑定到 Vue JS 中的`searchQuery`数据。这意味着，每当`searchQuery`数据的值改变时，我们的输入也会改变……反之亦然，每当用户在输入中键入一些东西时，我们的`searchQuery`数据中的值就会更新。

它还设置了一个`v-on:keyup`属性，使得 es Vue JS 在发生`keyup`事件时调用`performSearch()`方法。这就是导致我们的 API 调用 GraphQL 来在用户输入时动态打开的原因。

<aside>This is the reac­tive mag­ic of Vue­JS at work</aside>

之后，我们有一个列表项，上面设置了`v-for`属性。这导致我们的`searchResults`数据中的每个对象都有一个`<li>`。

因此，当从我们的 GraphQL API 端点返回数据时，`searchResults`数据会得到更新，这导致前端的 DOM 倾向于使用所有结果进行快速更新。

如果没有结果，那么什么也不渲染！

bot tom 只是将我们的 Vue 实例中的所有数据都转储为 JSON，因此我们可以看到引擎内部发生了什么。

## GraphQL 用于自动完成搜索

现在让我们更详细地看看我们的`performSearch()`方法。虽然这在技术上仍然是我们 Vue 实例的一部分，但它与我们将要做的 GraphQL 查询有关:

```
 // Perform a search
            performSearch() {
                // If they haven't entered anything to search for, return nothing
                if (this.searchQuery === '') {
                    this.searchResults = {};
                    return;
                }
                // Set the variables we will pass in to our query
                const variables = {
                    sections: searchSections,
                    needle: searchPrefix + this.searchQuery,
                    limit: 5
                };
                // Execute the query
                executeQuery(this.searchApi, searchQuery, variables, (data) => {
                    this.searchResults = data.data.entries;
                });
            } 
```

首先，它只是检查`searchQuery`是否是 emp ty 字符串，如果是，则将`searchResults`设置为 emp ty 对象，然后返回。

我们这样做是因为如果我们将一个 emp ty 搜索字符串传递到我们的 Craft CMS 后端，它将返回所有的结果。我们希望它返回 *none* 。

然后，它设置我们要传递给 GraphQL 查询的`variables`。如果您熟悉 Craft CMS，这应该与我们可能传递给`craft.entries`来查找数据的内容相当相似:

*   `sections` —在 Craft CMS 中搜索的部分
*   `needle` —要查找的搜索字符串；这是用户用`searchPrefix`键入的前缀
*   `limit` —我们想要返回的结果数

为了使事情变得容易，我们定义了以下常量:

```
 // What to search for
    const searchSections = ['blog'];
    const searchPrefix = 'title:'; 
```

`searchSections`告诉它我们只想搜索`blog`部分。`searchPrefix`用于将搜索限制在`title`区域，它的工作方式和[在 Craft CMS](http://docs.craftcms.com/v3/searching.html#supported-syntaxes) 中的搜索方式一样。如果我们想让它搜索条目中的所有东西，我们可以把它设置成一个 emp ty 字符串(`''`)。

最后我们得到了一些图表！接下来它调用`executeQuery()`，传入我们创建的 Axiois API、我们要执行的查询、我们的`variables`，然后传入一个回调函数。

下面是`searchQuery` GraphQL 查询的样子:

```
 // The query to search for entries in Craft
    const searchQuery =
        `
        query searchQuery($sections: [SectionsEnum], $needle: String!, $limit: Int)
        {
            entries(section: $sections, search: $needle, limit: $limit) {
                title
                url
            }
        }
        `; 
```

虽然 syn 税对你来说可能看起来有点奇怪，但是这里发生的事情应该是很清楚的。我们正在定义一个名为`searchQuery`的 GraphQL 查询，我们正在定义输入变量的名称*以及它们的类型*。类型定义后的`!`表示 GraphQL 中的变量*是必需的，*和`[]`是数组合成税。

这是 GraphQL 中的一个重要概念；它有一个严格的类型系统来确保传递给它的数据的纯度和正确性。如果您想了解更多信息，请参阅关于[模式&类型](http://graphql.org/learn/schema/)的图表文档。

<aside>You must tell GraphQL not only what vari­ables you’re pass­ing in, but what type of data they expect</aside>

GraphQL 使用我们传入的查询和变量来决定选择什么数据。然后`title`和`url`告诉 GraphQL 我们想要回什么数据。

这是 GraphQL 中另一个重要的概念:它将只返回你所要求的数据！因此，即使这些博客条目可能包含大量数据，它也只会返回给我们所请求的`title`和`url`。

<aside>GraphQL returns only what you ask for, which means it can be super lightweight</aside>

即使查询的 syn tax 对您来说没有 100%的意义，也没关系。你可以看到它正在发送一些要在查询中查找的数据，并定义它返回什么。

当查询完成时，它将调用我们的回调函数:

```
 (data) => {
    this.searchResults = data.data.entries;
} 
```

只有当查询结果成功时，它才回调我们的调用；我们只是将我们的`searchResults`设置为返回的`data`(只有条目)的子集。

好了，让我们看看`executeQuery()`函数的内部，看看它到底在做什么:

```
 // Execute a GraphQL query by sending an XHR to our api endpoint
    const executeQuery = (api, query, variables, callback) => {
        api.post('', {
            query: query,
            variables: variables
        }).then((result) => {
            if (callback) {
                callback(result.data);
            }
            console.log(result.data);
        }).catch((error) => {
            console.log(error);
        })
    }; 
```

其实很简单！我们没有使用任何复杂的 GraphQL-spe cif ic JavaScript，我们只是使用我们创建的 Axios 实例向我们的 API URL 发送一个包含我们数据的`POST`!

`.post()`方法的第一个参数是 URL，当我们创建 Axios 实例时，它被附加到我们指定的 ear li er 的`baseURL`中。因为我们对所有的 API 使用一个 URL，所以我们传入一个 emp ty 字符串(`''`)。

`.post()`方法的第二个参数是我们想要`POST`到 API 端点的数据对象；这里我们需要的是 GraphQL 查询的`query`和`variables`。

然后由于`.post()`方法返回一个[承诺](http://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)，`then`当数据成功返回时，我们调用我们的`callback`，或者我们`catch`任何错误，并把它们记录到控制台。

## 喝杯啤酒吧！

唷！你累了吗？我累了！但是我认为这里的概念并不坏，只是需要学习一些新的名词。

[![Have A Beer](../Images/0f89f6d7e3ec502d3468621d7a5afec2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gsnRQ2uD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/have-a-beer.jpg)

我们已经涵盖了您需要了解的所有事情是如何工作的大部分重要概念，所以喝杯啤酒庆祝一下吧，然后让我们开始接触如何避免误解。

没那么糟糕，因为专业是一样的！

## 联系人表单子任务保存

需要做的另一件常见的事情是，用户在前端输入一些数据，而您希望将这些数据保存在数据库的后端。

在我们的例子中，我们希望将联系人的姓名、电子邮件地址和信息从一个联系表单保存到后端的数据库中，这样我们的 CRM 人员就可以与他们保持联系。

从正面看，它是这样的:

[![Contact Form Submission Saving](../Images/0a071fdf3c721e937810575071adb622.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ay8sNE7J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_2166x973_crop_center-center_line/contact-form-submission-saving.png)

所以，斯坦·达德先生。用户填写姓名、电子邮件和信息，然后点击**submit**but ton……我们将信息保存在后端的数据库中。

我们还向用户显示一条很好的消息，告诉他们该子任务已成功提交。这是小事。

## 保存合同表单子任务的实例

我们的联系人表单的 Vue 实例看起来非常熟悉:

```
 // Instantiate our Vue instance
    new Vue({
        el: '#demo',
        data: {
            contactApi: axios.create(configureApi(apiUrl, apiToken)),
            contactName: '',
            contactEmail: '',
            contactMessage: '',
            submitted: false
        },
        methods: {
            // Submit the contact form
            submitContactForm() {
                // Set the variables we will pass in to our mutation
                const variables = {
                    contactName: this.contactName,
                    contactEmail: this.contactEmail,
                    contactMessage: this.contactMessage,
                };
                // Execute the query
                executeQuery(this.contactApi, contactFormMutation, variables, (data) => {
                    this.submitted = true;
                });
            }
        }
    }) 
```

我们的数据如下:

*   `contactApi` —我们将用来通过`http`发送&接收 GraphQL 的 Axios 实例
*   `contactName` —用户输入到联系表单中的名称
*   `contactEmail` —用户在联系表单中输入的电子邮件地址
*   `contactMessage` —用户输入到联系表单中的消息
*   `submitted` —合同表格是否成功提交

这个`configureApi()`函数看起来……嗯，该死，它和我们在自动搜索例子中使用的完全一样。耶，代码重用！

唯一不同的是我们传入的设置，因为我们为 con tact form 提供了一个单独的 rate bear er 令牌，它允许将数据保存到我们的 Con tact Form 通道:

```
 // Information needed for connecting to our CraftQL endpoint
    const apiToken = 'DxOES1XTDtnFVILEp0kNcOpvJpRXOmjFQci4lz6jLrrUqan6zTJ02ZkZyM_VTXlH';
    const apiUrl = '/api'; 
```

这太棒了，它利用了我们已经做过的每一件事，所以让我们直接转到 HTML 的联系表单！

## 保存联系人表单子信息的 HTML

在我们进入`submitContactForm()`方法做什么之前，让我们看一下我们的 con tact 表单的 HTML 模板:

```
 <!DOCTYPE html>
<html>
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
</head>

<body>
<div class="container p-5">
    {% verbatim %}
    <form id="demo" autocomplete="off">
        <div class="form-group">
            <label for="contactName">Name:</label>
            <input v-model="contactName" id="contactName" class="form-control" type="text" />
        </div>

        <div class="form-group">
            <label for="contactEmail">Email:</label>
            <input v-model="contactEmail" id="contactEmail" class="form-control" type="text" />
        </div>

        <div class="form-group">
            <label for="contactMessage">Message:</label>
            <textarea v-model="contactMessage" id="contactMessage" class="form-control"></textarea>
        </div>

        <div class="form-group">
            <button v-on:click="submitContactForm()" type="button" class="btn btn-primary">Submit</button>
        </div>

        <div v-if="submitted" class="alert alert-primary" role="alert">
            Message submitted!
        </div>

        <div class="form-group">
            <pre>data: {{ $data }}</pre>
        </div>
    </form>
    {% endverbatim %}
</div> 
```

同样，我们在顶部有相同的 JavaScripts 和 Boot strap CSS，然后我们有一个具有 HTML 外观的属性标准 con tact，输入我们想要的每一条数据。

我们再次使用`v-model`属性将输入绑定到 Vue 实例中适当的数据上，这样当输入数据时，我们就能得到相应的响应。

然后我们有一个设置了`v-on:click`属性的 but ton，这样当用户点击按钮时，它将调用我们的`submitContactForm()`方法。

最后，我们有一个属性设置为`v-if`的`<div>`，只有当`submitted`为`true`时才显示，向用户显示一条好消息，让他们知道他们的子任务成功了。*因为我们在乎。*

## 保存合同表单子信息的图表

现在让我们回到我们的`submitContactForm()`方法，看看它在做什么:

```
 // Submit the contact form
            submitContactForm() {
                // Set the variables we will pass in to our mutation
                const variables = {
                    contactName: this.contactName,
                    contactEmail: this.contactEmail,
                    contactMessage: this.contactMessage,
                };
                // Execute the query
                executeQuery(this.contactApi, contactFormMutation, variables, (data) => {
                    this.submitted = true;
                });
            } 
```

简单来说，我们提取出想要传递给 GraphQL 的`variables`，并再次调用`executeQuery()`来执行我们的查询。

有趣的是,`executeQuery()`又是完全相同的代码！即使我们正在做一些不同的事情(保存数据而不是读取数据)，我们也可以使用完全相同的`executeQuery()`方法！

<aside>Every­thing in GraphQL is a query</aside>

当我们想要在 GraphQL 中更改或添加新数据时，这被称为**变异**。穆塔选项只是另一个查询，*也可能改变或添加数据*。

这就是我们的`contactFormMutation`的样子:

```
 // The mutation to write contact form data to Craft
    const contactFormMutation =
        `
        mutation contactFormMutation($contactName: String!, $contactEmail: String!, $contactMessage: String!)
        {
            upsertContactForm(
                authorId: 1
                title: $contactName
                contactName: $contactName
                contactEmail: $contactEmail
                contactMessage: $contactMessage
            ) {
            id
            }
        }
        `; 
```

所以它看起来和我们之前做的很相似，但是现在不是`query`而是`mutation`。我们仍然告诉 GraphQL 我们传入了哪些变量，以及这些变量的类型。

但是我们已经添加了`upsertContactForm()`，它有一个我们想要*将*插入数据库的数据列表。Upsert 的意思是“添加或更新数据”，而 Con tact Form 部分是我们想要向上插入的部分的名称。

然后，由于变异只是一种查询，我们必须告诉 GraphQL 我们想要返回什么数据；在这种情况下，我们只需要在 ed 条目后面查询新 ly cre 的`id`。

我们在 Con tact Form chan nel 中插入的字段与我们在 Craft CMS 中定义的相匹配:

[![Contact Form Craft Cms Fields](../Images/90703a4010aa6e25968c4ea5675b847f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9_uStXNR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1650x914_crop_center-center_line/contact-form-craft-cms-fields.png)

唯一有点不寻常的是我们以硬编码的形式传递的内容。这是因为所有条目都需要由 Craft CMS 中的某个人拥有。

就是这样！我们正在保存 Craft CMS 后端的条目。

很明显，这里我们可以做更多的事情，比如用 [vee-val i date](http://github.com/baianat/vee-validate) 来验证表单输入，在表单提交后隐藏表单，等等。亲爱的读者，这是留给你的练习。

## 把乒包起来

虽然这似乎是一个很好的学习点，但是一旦你熟悉了 GraphQL 的工作方式，它比用 [Ele ment API](http://github.com/craftcms/element-api) 来“滚动你自己的”自定义 API 要容易得多，而且你会学到一种可以移植到许多不同平台的技能。

[![Victory Image](../Images/186618460c02b07c51f9dece35d09bd0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RetgI5jz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/victory-image.jpg)

最好的部分是…你已经从实现它的系统中分离出你的 API。因此，如果您决定迁移到不同的 CMS 或 plat form，这将使迁移变得非常容易！

学习 GraphQL 最有趣、最有趣的方式之一就是简单地使用 [CraftQL plu g in](http://github.com/markhuot/craftql) 中包含的 in-browser[graph QL](http://github.com/graphql/graphiql)IDE:

[![Graphiql Ide Explorer Auto Complete](../Images/fa8a1c94eec29a8ce02eeacca28e0d5a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CjUMUxNy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_2396x1648_crop_center-center_line/graphiql-ide-explorer-auto-complete.png)

您可以使用自动完成的编辑器来处理您的查询和变更，该编辑器知道您的 entry Craft CMS 后端的模式。太好玩了！

如果你不能得到足够的 GraphQL，那么接下来可以参考 Vue 文章中的 [GraphQL 基础和实用示例。也看看](https://medium.com/@lachlanmiller_52885/graphql-basics-and-practical-examples-with-vue-6b649b9685e0)[图 QL:比所有其他的更好？](http://devmode.fm/episodes/graphql-better-than-all-the-rest)吊舱投在[开发模式上。fm](http://devmode.fm/) ！

祝你愉快！

## 自动完成搜索全部源代码

下面是自动完整搜索示例的完整源代码:

```
 <!DOCTYPE html>
<html>
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
</head>

<body>
<div class="container p-5">
    {% verbatim %}
    <form id="demo" autocomplete="off">
        <div class="form-group">
            <label for="searchQuery">Search:</label>
            <input v-model="searchQuery" v-on:keyup="performSearch()" id="searchQuery" class="form-control" type="text" />
        </div>

        <div class="form-group">
            <ul class="list-group">
                <li v-for="(searchResult, index) in searchResults" class="list-group-item">
                    <a v-bind:href="searchResult.url">{{ searchResult.title }}</a>
                </li>
            </ul>
        </div>

        <div class="form-group">
            <pre>data: {{ $data }}</pre>
        </div>
    </form>
    {% endverbatim %}
</div>

<script>
    // Information needed for connecting to our CraftQL endpoint
    const apiToken = 'wwYfgLejf27AxoSmR0K3wUzFoj9Y96QSNTICvpPslO2l2JcNsjfRY9y5eIec5KhN';
    const apiUrl = '/api';
    // What to search for
    const searchSections = ['blog'];
    const searchPrefix = 'title:';
    // The query to search for entries in Craft
    const searchQuery =
        `
        query searchQuery($sections: [SectionsEnum], $needle: String!, $limit: Int)
        {
            entries(section: $sections, search: $needle, limit: $limit) {
                title
                url
            }
        }
        `;
    // Configure the api endpoint
    const configureApi = (url, token) => {
        return {
            baseURL: url,
            headers: {
                'Authorization': `Bearer ${token}`,
                'X-Requested-With': 'XMLHttpRequest'
            }
        };
    };
    // Execute a GraphQL query by sending an XHR to our api endpoint
    const executeQuery = (api, query, variables, callback) => {
        api.post('', {
            query: query,
            variables: variables
        }).then((result) => {
            if (callback) {
                callback(result.data);
            }
            console.log(result.data);
        }).catch((error) => {
            console.log(error);
        })
    };
    // Instantiate our Vue instance
    new Vue({
        el: '#demo',
        data: {
            searchApi: axios.create(configureApi(apiUrl, apiToken)),
            searchQuery: '',
            searchResults: {}
        },
        methods: {
            // Perform a search
            performSearch() {
                // If they haven't entered anything to search for, return nothing
                if (this.searchQuery === '') {
                    this.searchResults = {};
                    return;
                }
                // Set the variables we will pass in to our query
                const variables = {
                    sections: searchSections,
                    needle: searchPrefix + this.searchQuery,
                    limit: 5
                };
                // Execute the query
                executeQuery(this.searchApi, searchQuery, variables, (data) => {
                    this.searchResults = data.data.entries;
                });
            }
        }
    })
</script>
</body>
</html> 
```

## 合同表单子任务保存完整源代码

以下是保存合同表单子任务的完整源代码:

```
 <!DOCTYPE html>
<html>
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
</head>

<body>
<div class="container p-5">
    {% verbatim %}
    <form id="demo" autocomplete="off">
        <div class="form-group">
            <label for="contactName">Name:</label>
            <input v-model="contactName" id="contactName" class="form-control" type="text" />
        </div>

        <div class="form-group">
            <label for="contactEmail">Email:</label>
            <input v-model="contactEmail" id="contactEmail" class="form-control" type="text" />
        </div>

        <div class="form-group">
            <label for="contactMessage">Message:</label>
            <textarea v-model="contactMessage" id="contactMessage" class="form-control"></textarea>
        </div>

        <div class="form-group">
            <button v-on:click="submitContactForm()" type="button" class="btn btn-primary">Submit</button>
        </div>

        <div v-if="submitted" class="alert alert-primary" role="alert">
            Message submitted!
        </div>

        <div class="form-group">
            <pre>data: {{ $data }}</pre>
        </div>
    </form>
    {% endverbatim %}
</div>

<script>
    // Information needed for connecting to our CraftQL endpoint
    const apiToken = 'DxOES1XTDtnFVILEp0kNcOpvJpRXOmjFQci4lz6jLrrUqan6zTJ02ZkZyM_VTXlH';
    const apiUrl = '/api';
    // The mutation to write contact form data to Craft
    const contactFormMutation =
        `
        mutation contactFormMutation($contactName: String!, $contactEmail: String!, $contactMessage: String!)
        {
            upsertContactForm(
                authorId: 1
                title: $contactName
                contactName: $contactName
                contactEmail: $contactEmail
                contactMessage: $contactMessage
            ) {
            id
            }
        }
        `;
    // Configure the api endpoint
    const configureApi = (url, token) => {
        return {
            baseURL: url,
            headers: {
                'Authorization': `Bearer ${token}`,
                'X-Requested-With': 'XMLHttpRequest'
            }
        };
    };
    // Execute a GraphQL query by sending an XHR to our api endpoint
    const executeQuery = (api, query, variables, callback) => {
        api.post('', {
            query: query,
            variables: variables
        }).then((result) => {
            if (callback) {
                callback(result.data);
            }
            console.log(result.data);
        }).catch((error) => {
            console.log(error);
        })
    };
    // Instantiate our Vue instance
    new Vue({
        el: '#demo',
        data: {
            contactApi: axios.create(configureApi(apiUrl, apiToken)),
            contactName: '',
            contactEmail: '',
            contactMessage: '',
            submitted: false
        },
        methods: {
            // Submit the contact form
            submitContactForm() {
                // Set the variables we will pass in to our mutation
                const variables = {
                    contactName: this.contactName,
                    contactEmail: this.contactEmail,
                    contactMessage: this.contactMessage,
                };
                // Execute the query
                executeQuery(this.contactApi, contactFormMutation, variables, (data) => {
                    this.submitted = true;
                });
            }
        }
    })
</script>
</body>
</html> 
```

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计