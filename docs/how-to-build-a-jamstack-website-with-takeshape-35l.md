# 如何用 TakeShape 建立一个 JAMstack 网站

> 原文：<https://dev.to/takeshape/how-to-build-a-jamstack-website-with-takeshape-35l>

## 在这个 4 部分系列的第 2 部分中，您将学习如何使用一个无头 cms 来构建一个静态站点。

这是 4 部分系列的第 2 部分。查看第 1 部分，获得对 [TakeShape](https://www.takeshape.io/) 的介绍，并学习内容建模和内容创建。在第 3 部分(即将推出)中，您将学习如何将静态站点部署到 Netlify。在第 4 部分(即将推出)中，您将进一步扩展您的技能，并学习如何在 Gatsby.js 中使用 TakeShape。

[![A screenshot of the JAMstack site you’ll be building.](../Images/cf1a4e47aa5a81eb16a2b68ae7fbf2b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EBfMITIe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4g8kx0b0z5qt62u86cw5.png)

在第 1 部分中，您学习了如何建模和创建内容，并通过 TakeShape 的 GraphQL API 使其可用。现在，您将了解如何结合使用 TakeShape 的静态站点生成器和 API 来快速创建 JAMstack 网站。您将在本地机器上创建并配置一个 TakeShape 静态站点，编写 GraphQL 查询文件，并在 HTML 模板中使用这些查询返回的数据。

如果你想跳过从零开始建立你的静态站点，你可以从" [Shape Portfolio](https://shape-portfolio.takeshapesampleproject.com/) "示例模板开始。只需运行命令`git clone https://github.com/takeshape/takeshape-samples.git && cd takeshape-samples/shape-portfolio`。这将下载一个静态站点[回购](https://github.com/takeshape/takeshape-samples)，它被预先配置为与 TakeShape 中的“Shape Portfolio”模板一起工作。这个回购比我们刚刚一起构建的版本更复杂、更有风格、更有脚本。它可能会给你一些灵感和指导，告诉你下一步该怎么做。

如果你决定留下来，我们将从头开始建立同样的静态网站，这样你就可以了解它是如何工作的。准备，预备，开始！

### 您好组合

你将从克隆 Github 的`blank-project`模板开始，这将为你提供一个[基本的形状设置](https://github.com/takeshape/takeshape-samples.git)。首先，将存储库克隆到您的计算机上，然后将空白项目复制到一个新文件夹中，最后删除克隆的 repo，因为您不再需要它的任何内容。在您的终端中，它看起来是这样的:

```
> git clone https://github.com/takeshape/takeshape-samples.git
> cp -r takeshape-samples/blank-project/ shape-portfolio
> rm -rf takeshape-samples 
```

然后，您将进入新复制的项目文件夹，安装使用 npm 运行项目所需的软件，并使用 TakeShape 配置对其进行初始化:

```
> cd shape-portfolio
> npm install
> npm run init 
```

运行`init`将在您的本地开发环境和 TakeShape 服务之间建立连接。将提示您进行几个部分来配置您的环境。第一个提示将是您的 TakeShape 凭证。输入您的 TakeShape 帐户电子邮件和密码，然后使用箭头键选择您的投资组合项目。您将得到一个关于还没有建立任何站点的提示(不要担心，我们将在第 3 部分中讨论)。

初始化创建两个文件，`.tsgrc`和`graphql.config.json`。这些已经在你的项目的`.gitignore`文件中了，因为你是从空白项目模板开始的。

您现在可以通过运行`npm start`来测试所有的设置是否正确。如果一切按预期进行，您的默认浏览器将打开到`localhost:5000`并显示空白模板页面。万岁！您刚刚从本地机器上运行了一个 TakeShape。现在是时候开始黑你的投资组合了🤓。如果出现错误或无法加载站点，请阅读输出到终端的任何错误消息以调试问题。

### 项目结构

通过克隆`blank-project`模板，您已经获得了 TakeShape 期望的一些基本文件和文件夹。我们现在将对它们中的每一个进行更多的解释。

#### 配置

项目的根目录是一个名为`tsg.yml`的文件。它保存了项目的配置，是开始的最佳地方。以下是`tsg.yml`中的内容:

```
templatePath: src/templates
staticPath: static
buildPath: build

routes:
  homepage:
    path: /
    template: pages/index.html 
```

*   顶部是`templatePath`变量，它告诉 TakeShape 在构建项目时在哪里寻找模板文件。
*   接下来是`staticPath`变量，它告诉 TakeShape 在哪里寻找像样式表、脚本这样的文件，以及像 favicons 这样的静态图像。
*   之后是`buildPath`变量，这是 TakeShape 在项目构建完成后放置它的地方。
*   最后，还有一个`routes`变量。此变量的每个子变量配置项目的路由规则。现在我们只有`homepage` route，它指定了路由路径(在本例中是索引路径)和路由应该使用的模板文件。模板都是相对于`templatePath`设置在文件顶部的。

构建路线有更复杂的方法，包括来自您在 TakeShape 中创建的内容的动态变量。我们一会儿会讲到这个。首先，让我们看看您将要使用的模板。

### 模板文件

默认情况下，TakeShape 使用 [Nunjucks](https://mozilla.github.io/nunjucks/) 模板系统。

`src/templates`文件夹包含三个启动子目录:`data`、`layouts`、`pages`。`data`包含从 TakeShape API 中检索内容的 GraphQL 查询文件。`pages`包含单个独特的页面模板。`layouts`包含一个其他模板扩展的基础模板。在`layouts`里面是一个名为`default.html`的启动布局模板，看起来是这样的:

```
<!doctype html>

<html>
<head>
  Blank Project - TakeShape

  <meta charset="utf-8">

  <link rel="stylesheet" href="/stylesheets/main.css"/>
  <link rel="author" href="humans.txt"/>
</head>

<body>

<div class="main">
  {% block content %}{% endblock %}
</div>

<script src="/javascripts/main.js"></script>
</body>
</html> 
```

如您所见，这为其他页面使用 Nunjuck 的扩展功能在布局中包装自己提供了基础。扩展是通过使用 using `{% extends "layouts/default.html" %}`和`{% block %}`标签来完成的。

在`pages/index.html`中，你可以看到这个模板是如何扩展默认布局的:

```
{% extends "layouts/default.html" %}

{% block content %}
  <h1>Blank Project Template For TakeShape</h1>
{% endblock %} 
```

有了这些基础，你就可以用最少的重复来构建大量的页面。

#### 静态文件

最后，我们将快速浏览一下静态文件，如样式表和脚本。上面的起始`default.html`模板已经链接到文件`/stylesheets/main.css`和`/javascripts/main.js`。您将在顶级`static`目录中找到这些文件。

### 建立您的投资组合

#### 首页

现在你知道了所有的东西是如何组合在一起的，你可以开始定制你的主页来创建一个惊人的作品集了！

首先，你需要在你的主页上展示你的项目。为此，您需要向主页路径添加一些数据，编写一个查询，然后在主页上呈现这些数据。为了向路线添加数据，您将在`tag.yml`中更新您的主页路线配置，如下所示:

```
routes:
  homepage:
    path: /
    template: pages/index.html
    context: data/projects.graphql 
```

然后在`src/templates/data`目录下创建一个`projects.graphql`文件。在这个文件中，您将为发送到 TakeShape API 的项目数据创建查询。只要您按照第 1 部分中的模式设置了项目内容类型，您的文件应该如下所示:

```
query {
 projects: getProjectList {
   items {
     name
     coverImage {
       path
     }
   }
 }
} 
```

现在，您可以在我们的主页模板中使用`projects`作为变量，以便在主页上呈现所有项目的列表。在`pages/index.html`中，你将把它更新成这样:

```
{% extends "layouts/default.html" %}

{% block content %}
<ul>
  {% for project in projects.items %}
  <li>
      <img src="{{project.coverImage.path|image({h: 200, w: 300, fit: 'crop'})}}">
      <p><strong>{{project.name}}</strong></p>
  </li>
  {% endfor %}
</ul>
{% endblock %} 
```

对于封面图像，请注意应用于图像路径的`image`滤镜。TakeShape 用户可以在 [imgix](https://www.imgix.com/) 上免费保存他们的图片，这是一个令人惊叹的强大的图片 CDN！`image`过滤器是一个[特殊的 TakeShape 过滤器](https://www.takeshape.io/docs/template-tags/)，它接受 IMGIX 过滤器参数并返回图像的 imgix URL。我们已经在图像上设置了自定义的高度和宽度，并告诉 imgix 裁剪图像以适应这些尺寸。所有可能的选项都列在 imgix 的全面 API 文档中。

#### 独立项目页面

接下来，您将希望为您的项目创建独立的页面，以便您可以提供每个页面的更多详细信息，并直接链接到项目。

首先，用项目页面的新路径更新您的`tsg.yml`配置:

```
 project:
    path: /projects/:name/
    template: pages/project.html
    paginate:
      data: data/projects.graphql
      itemName: project 
```

这比我们上一条路线稍微复杂一点，所以让我们来分解一下:

*   `path`使用变量`:name`来为每个项目页面呈现唯一的 URL。这个变量来自我们在`paginate`变量中使用的查询。
*   `paginate`告诉 TakeShape 如何将我们之前创建的`projects.graphql`查询中的数据分割成单独的页面。在这种情况下，查询中的每个`item`都将获得自己的页面。`itemName`设置引用每个分页项目的变量。在这种情况下，我们将从名为`project`的变量中访问数据。

接下来，您将希望向`projects.graphql`查询添加几个字段，以便您可以在其独立页面上呈现关于每个项目的更多信息。更新您的查询，如下所示:

```
query {
  projects: getProjectList {
    items {
      name
      startDate
      endDate
      coverImage {
        path
      }
      descriptionHtml
      client {
        name
        url
      }
    }
  }
} 
```

相当简单！你认为你已经掌握了建立一个初具规模的网站的诀窍了吗？请记住，您可以在 TakeShape 的 web 界面中探索整个项目的 API。自己写查询的时候真的很方便。

现在，您可以创建`pages/project.html`模板来将项目数据呈现为 HTML。下面是它的样子:

```
{% extends "layouts/default.html" %}

{% block content %}
<article class="project">
  <header>
    <img class="project__cover-image" src="{{project.coverImage.path|image({h: 600, w: 1200, fit: 'crop'})}}">
    <h1>{{project.name}}</h1>
    <div class="project__metadata">
    <p>{{project.startDate|date('YYYY')}}{% if project.endDate %} – {{project.endDate|date('YYYY')}}{% endif %}</p>
    {% if project.client %}
    <p><a href="{{project.client.url}}">{{project.client.name}}</a></p>
    {% endif %}
    </div>
  </header>
  <div class="project__description">{{project.descriptionHtml|safe}}</div>
</article>
{% endblock %} 
```

这也很简单！请注意如何使用内置的 TakeShape `date`过滤器来格式化项目的开始和结束日期，以便只显示年份。另外，请注意，`safe`过滤器必须应用于项目描述。这是因为它是从 TakeShape API 作为原始 HTML 输出的。

现在，您的每个项目都有了一个页面，您希望能够从您的主页链接到各个项目。在主页模板上，用锚标记`<a href="{{project|route('project')}}">`包裹每个项目列表项。应该是这样的:

```
{% extends "layouts/default.html" %}

{% block content %}
<ul>
  {% for project in projects.items %}
  <li>
    <a href="{{project|route('project')}}" title="{{project.name}}">
    <img src="{{project.coverImage.path|image({h: 200, w: 300, fit: 'crop'})}}">
    <p><strong>{{project.name}}</strong></p>
    </a>
  </li>
  {% endfor %}
</ul>
{% endblock %} 
```

`route`是另一种特殊的 TakeShape 过滤器。如果您为一个对象定义了一个路由，比如为项目页面定义了一个动态路径，那么`route`过滤器将返回该项目的路由。挺顺手的！只要参考你的`tsg.yml`中出现的路线名称，你就可以上路了！

现在，如果您在终端中运行`npm start`，网站应该会成功构建。你会看到一个主页，上面有你的项目列表，你应该可以点击每个项目。

下一步，你可以试着用你的传记创建一个关于你的页面，或者一个列出你所有客户的页面。如果您在编写配置或模板时遇到困难，请务必查阅 TakeShape 的文档。

## 下一步

在第 3 部分中，我们将把您刚刚创建的静态站点部署到 Netlify！只需几个步骤，你就能把你的网站部署到 Netlify 的 CDN 上，并在一个安全的 HTTPS 连接上托管你的页面。多酷啊。

在那之前，继续黑吧！

## 对 JAMstack CMS 感兴趣？

JavaScript、API 和标记是 JAMStack。是一个无头的 GraphQL CMS 和静态站点生成器，用于构建 JAMstack 网站。在 TakeShape，我们致力于为最有创造力的设计师和开发者打造最好的 CMS 工具。使用我们的项目模板，很容易开始。此外，价格灵活且实惠。[注册一个免费账户](https://app.takeshape.io/signup)，花更多的时间发挥创造力！