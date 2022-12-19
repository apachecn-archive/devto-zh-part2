# Fire CMS 范例

> 原文：<https://dev.to/megazear7/the-fire-cms-paradigm-4pag>

我对创建一个简约的内容管理系统感兴趣已经有一段时间了。内容管理系统(CMS)是我的日常工作，我觉得在这个领域有很多机会使用现代网络平台。我已经看到并使用了这一领域的一些新产品，但是由于许多原因，我想开发自己的解决方案。有鉴于此，我开始用 Neo4j 进行概念验证，并在这个图形数据库的基础上开发了许多 CMS 特性。

Neo4j 支持的最有趣的事情是对数据层次的多种解释，而不是单一的数据规范层次。这是因为 Neo4j 中的两个节点能够通过多条边连接起来。然而，我发现虽然在这个领域有许多有趣的机会，但我想缩小规模，在一天内尝试一个 CMS，我发现我的 Neo4j 计划虽然强大，但需要几个月的时间才能实现。

这是我改用 Firebase 的地方，特别是 Firestore 数据库和静态托管。我的目标如下:

1.  基于国家和语言的本地化和翻译能力。
2.  作为数据存储在数据库中的页面层次结构，其中可以从层次结构中派生出面包屑、主页、子页面、导航链接等。
3.  页面的内容也将作为数据存在于数据库中。在某种程度上，甚至格式和布局都可以由数据决定。
4.  没有服务器端的逻辑或渲染。这意味着页面渲染都必须发生在客户端。
5.  发送到浏览器的供应商 JS 数量非常有限。

我如何着手解决这些目标？先来看数据。

## 定义一个数据层次

#### 所选择的技术

Firestore 是一个文档数据库。最常见的例子是 MongoDB，但是还有很多其他的例子。在文档数据库中存在两个顶级实体:文档和集合。规则很简单，文档包含数据，通常以容易转换或表示为 JSON 的形式，集合包含许多文档，其中每个文档都有惟一的 ID。此外，文档本身可以包含集合。

#### 定义层级

对于我们的例子，我们的顶级集合将被命名为“页面”。这是我们的多国，多语言网站的所有页面的内容。您可能希望数据库中有其他形式的数据，因此您希望有一个顶级名称空间来将页面与其他形式的数据分开。

在 pages 集合中将有许多文档，每个文档代表一个国家。这里每个文档的 ID 应该是国家的两个字母的国家代码，例如“us”或“ca”。

每个国家都将包含一个名为 children 的集合，其中包含国家页面的子页面。这些子集合中的每一个都将包含许多代表该国可用语言的文档。这些文档的 ID 将是该语言的两个字母的语言代码，例如“en”或“fr”。

这些语言页面又会有一个 children 集合，代表该语言的子页面。在这里，您可以根据自己的喜好和需要来构建页面，但是每个文档都需要一个名为“children”的集合来表示该页面的子页面(如果它有子页面的话)。我这样做的方法是在语言的 children 集合中有一个“home”文档，然后在 home pages children 集合中有一系列的子页面。请记住，这些文档中的每一个都代表网站的一个页面，最终将通过该页面的唯一 url 来访问。

#### 页面的内容

这些文档中的每一个，我现在称之为页面，将包含表示页面元数据的数据，比如页面标题、页面类型、重定向信息等等。此外，这些页面将包含页面的内容数据。这些内容数据将用于呈现页面的 HTML。此外，页面在数据库层次结构中的位置将用于确定到主页的链接、到子页面的链接以及生成导航元素等。

有些页面，比如国家和语言页面，不应该直接显示给用户，而应该将用户重定向到主页。由于这个原因，这些页面需要一个“重定向”的“类型”属性和一个用户应该被重定向到的主页的路径的“重定向”属性。这些重定向也需要构建到 firebase 托管模式中，这是一个不幸但必要的重复。我仍在寻找避免这种重复的方法。

#### 渲染页面

如果我们不需要任何服务器端的渲染，但是我们需要将这种层次结构的数据渲染为 HTML 页面，那么我们将不得不进行客户端渲染。解决方案是简单地让客户端查看 url，然后根据一些呈现逻辑来确定指定的页面。该呈现逻辑的第一步是让客户端路由器根据 url 确定使用什么数据和什么逻辑来呈现页面。
初始化 Firestore 数据库并导入 lit-html

```
<div class="page-container"></div>

<script type="module">
  import {html, render} from '/vendor/lit-html.js';

  let app = firebase.app();
  var db = firebase.firestore();

  // The firebase website told me to do this.
  db.settings({
    timestampsInSnapshots: true
  });
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这假定了默认的 Firestore 托管设置。我们在这篇博文后面添加的所有 JavaScript 都将被添加到这个脚本标签中。

## 定义路由器

这里面有很多东西，所以我把它归结为一个方法。给定一个路径(比如 location.pathname ),我们可以对数据库的结构做一些假设，以便为我们需要的数据形成一个查询。

```
function getPath(path) {
  const truePath = ['', '/'].includes(path) ? '/us/en/home' : path;
  const parts = truePath.substring(1).split('/')
  let query = db.collection('pages');
  parts.forEach((part, index) => {
    query = query.doc(part);
    if (index !== parts.length -1) query = query.collection('children');
  });
  return query;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里所做的假设是，如果没有提供可用的路径，那么我们将重定向到美国英语主页。然后我们请求页面集合。最后，我们迭代路径的每个部分，并假设每个部分是当前集合中的一个文档的名称，然后我们在这个文档中检索“children”集合。

这形成了一个采用 url 路径的查询，例如“/us/en/home/example”。它将返回一个 Firestore 查询，查询位于以下位置的 id 为“example”的文档:

*   *页* - > **美** - > *子* - > **恩** - > *子* - > **家** - > *子* - > **例**

其中粗体字是文档，斜体字是集合。现在，我们可以使用这个查询来检索 are 数据库中页面的数据，不包括路径中的集合名称。

```
getPath('/us/en/home/example').get().then(page => page.data()) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以使用这些数据来生成页面的 HTML。

## 将数据渲染为 HTML

为此我使用了 lit-html。为此，我们使用 getPath 方法为页面形成一个查询，然后使用 lit-html 将模板呈现到页面上的特定位置。当请求完成时，我们的承诺将被解析，DOM 将更新我们的标题和描述。这假定这些属性存在于表示该页面的数据库中的文档中。

```
let pageQuery = getPath(window.location.pathname);

render(html` ${pageQuery.get().then(page => html`
    <h1>${page.data().title}</h1>
    <p>${page.data().description}</p>
  `)} `, document.querySelector('.page-container')); 
```

Enter fullscreen mode Exit fullscreen mode

#### 上下文渲染

如果我们想要一个到“主页”的链接，或者一个到主页下面的顶级页面的链接列表，该怎么办？这被称为上下文渲染。

```
let pageQuery = getPath('/us/en/home');
let homePageQuery = getHomePageQuery(pageQuery)
                    .then(homePageQuery => homePageQuery.get());
let navPagesQuery = getNavPagesQuery(pageQuery);

homePageQuery.then(homePage => console.log(homePage.data()));

render(html` ${pageQuery.get().then(page => html`
    <h1>${page.data().title}</h1>
    <p>${page.data().description}</p>
  `)} <div class="navigation"> ${homePageQuery.then(homePage => html`
      <a href="${homePage.path}"
         class="${homePage.path === window.location.pathname ? 'active' : ''}"> ${homePage.title} </a>
    `)}  ${navPagesQuery.then(navPages => navPages.map(navPage => html`
      <a href="${navPage.type === 'redirect' ? navPage.redirect : navPage.path}"
         target="${navPage.type === 'redirect' ? '_blank' : ''}"
         class="${navPage.path === window.location.pathname ? 'active' : ''}"> ${navPage.title} </a>
    `))} </div>
`, document.querySelector('.page-container')); 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到，我们使用代表页面的 Firestore 文档的“path”属性来设置链接的 href。这意味着我们需要在每个页面的“path”属性中提供一个规范的路径。我们还可以为每个文档生成这个值，方法是查找层次结构并删除集合名称，或者在创建表示页面的文档时让数据库挂钩生成这些值。

这里，我们使用两个新函数 getHomePagesQuery 和 getNavPagesQuery 定义了一组链接。这些函数将获取当前页面查询，并沿着页面和集合的层次结构向上爬行，以找到属性为“type”=“home”的文档。一旦找到这个主页，getHomePagesQuery 函数将返回这个页面的查询。然后，getNavPagesQuery 函数将为主页的所有子页面制定一个查询，并返回这个新的查询。

通过这种方式，你可以进行上下文渲染。也就是说，可以基于页面层次结构中的当前页面位置来生成导航。可以遵循类似的方法来创建面包屑或查找子页面列表。上面使用的两种方法的示例实现如下所示:

```
function getHomePageQuery(pageQuery) {
  return (async () => {
    let type = (await pageQuery.get()).data().type;

    while (type != 'home') {
      pageQuery = pageQuery.parent.parent;
      type = (await pageQuery.get()).data().type;
    }

    return pageQuery;
  })();
}

function getNavPagesQuery(pageQuery) {
  let homePageQuery = getHomePageQuery(pageQuery);

  return homePageQuery
  .then(homePageQuery => homePageQuery.collection('children').get())
  .then(children => {
    let firstLevelPages = [];
    children.forEach(child => {
      firstLevelPages.push(child.data());
    });
    return firstLevelPages
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

对于这个“getNavPagesQuery”方法，你需要在你的“主页”下面创建更多的页面，这样你就可以看到这些“第一层”页面的链接列表——这可能是站点导航的开始。

## 扩展范式

很明显，这个例子只是使用 Firebase Firestore 作为分层内容管理系统的开始。这里提供的示例需要进一步开发和组织，但是已经演示了上下文数据和页面层次结构的基本概念。我用这个概念作为我的个人“关于我”网站的起点: [about.alexlockhart.me](https://about.alexlockhart.me/us/en/home) 。

我在这里描述的并不依赖于所选择的技术。事实上，唯一需要的技术是一个基于 promise JavaScript API 的文档数据库和一个客户端渲染库。为此，我选择了 Firebase Firestore 和 lit-html，但也有许多其他选项可用。关键是，这与所使用的具体技术无关，而是与所采用的范式有关。

#### 页面内容

我们可以通过在每个页面文档中创建更复杂的数据来扩展这一点，然后将这些复杂的数据呈现为一个完整的 web 页面，其中可以包含文本、图像和其他功能。在以后的博客文章中，我将演示如何做到这一点。

#### SPA + PWA

除此之外，基于我们所做的设计决策，我们离拥有深度链接的单页面应用程序只有半步之遥了。我们也非常接近一个进步的网络应用程序，这将使我们的 CMS 可以作为一个应用程序安装在现代平台上，并可以离线使用。我将很快发表一篇博文，介绍如何在这些方向上扩展这种范式。

#### 翻译整合

最后，我们还可以实现数据库挂钩和翻译 API，以便在更新美国英语层次结构时生成其他国家和语言的页面层次结构。我计划也写一篇关于这个的博客，所以请继续关注！

更多我的文章请登录我的博客