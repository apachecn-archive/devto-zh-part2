# 当没有“标准方式”时，Express REST API 的项目结构

> 原文：<https://dev.to/ccleary00/project-structure-for-an-express-rest-api-when-there-is-no-standard-way-4gk3>

***原载于 [coreycleary.me](https://www.coreycleary.me/project-structure-for-an-express-rest-api-when-there-is-no-standard-way/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我还定期发送备忘单、其他开发人员的优秀教程的链接以及其他赠品！*

使用 Node + Express 在 GitHub 或 Google 上搜索 REST API 结构，你会发现很少有遵循相同组织的。

更糟糕的是，虽然有很多好的教程，但是很多根本没有结构。他们只是把所有东西放进一个 200 行的 server.js 文件，然后就收工了...

关于节点的最好的事情之一也是最痛苦的事情之一——很少有真正的约定(如果有的话)出现在 T2。

当然，有推荐的做事方法。但是这是一个非常灵活的平台，你可以选择任何一种方式来做事情，而且很可能会成功。

但是即使没有约定，开发人员也想知道做事情的最佳方式。说到 REST API(以及一般的节点项目...)，当涉及到构建项目时，每个人似乎都觉得自己在瞎飞！

### 没有“正确的方法”

最终，我不相信对于快速项目有一个最好的项目结构。

而不是问:

> "组织我的文件和文件夹的最好方法是什么？"

我认为最好问:

> "我的不同类型的逻辑去了哪里？"

对于这个问题，我们有更清晰的答案，也有可以遵循的东西。

而把我们的逻辑做好了**层**的分离，一个项目结构自然就浮现出来了。一个灵活的结构，你可以选择如何组织它，无论是通过更传统的 MVC(或 MVC-ish)或由酷的新的孩子，组件。这是因为这些结构无论如何都是分层的！您可以简单地对路由、控制器、服务等进行分组。到组件文件夹中。

只要逻辑处于“正确”的位置，结构/组织就没什么大不了的。

在奇怪的地方没有逻辑更容易重构，在奇怪的地方没有逻辑更容易测试，而且一旦你把你的代码签入源代码控制，无论如何都很容易做出改变！想尝试组件结构吗？很容易做出改变！

### “好的，我在船上...但是现在呢？”

关于这篇文章范围的一个快速提示:显然所有的项目都可以包含许多不同的东西。为了让这篇文章易于理解，不至于让你不知所措，我将省略请求/模型验证和认证。这两者本身都是动物，但期待未来的帖子来解决它们。

此外，这并不是一个“样板”项目，你可以复制回购协议，在控制台中输入`npm start`并立即拥有一个成熟的应用程序。虽然，如果你跟着做，你会得到一个**跑步 app** 。但是一个样板文件并不能真正达到解释应用程序中的层，逻辑放在哪里，以及如何得到基于此的结构的目的。

因为我们处理的是 REST API，一个 API 接收到一个**请求**并返回一个**响应**，让我们从这个请求到达我们的应用程序开始，穿过我们的层，然后应用程序返回一个响应。在这个过程中，我们将找出我们不同类型的逻辑应该去哪里。

### 层次？

我们先来看一个架构图:
[![](../Images/b385732d5b5d39825ba75a69ab5c787c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VgMaKeRW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.coreycleary.me/wp-content/uploads/2018/11/Express-REST-API-Struc.png)

剥过洋葱吗？剥掉第一层外层后，下面还有几层。

这里的“层”也差不多，意思是我们有:

在**服务层** - >之外的 **HTTP 层** - >在**数据库访问层** - >之外...你明白了

### 逻辑去哪里了？

我们将使用一个博客应用程序的例子来演示逻辑分离和我们得到的结构。

当我提到“逻辑类型”时，我指的是 REST API 逻辑的两个“主要”类别——HTTP 逻辑和业务逻辑。当然，你可以把“逻辑类型”尽可能的分解，但是这两个是主要的类别。

| 主要图层 | 类型 | 这里有什么逻辑？ |
| --- | --- | --- |
| HTTP 逻辑层 | 路线+控制器 | Routes -处理命中 API 的 HTTP 请求，并将它们路由到适当的控制器；控制器——获取请求对象，从请求中提取数据，进行验证，然后发送给服务 |
| 业务逻辑层 | 服务+数据访问 | 包含源自业务和技术需求的业务逻辑，以及我们如何访问我们的数据存储** |

* *数据访问层逻辑通常是更“技术性”的业务逻辑，我将它与业务逻辑归为一类，因为需求通常会驱动您需要编写的查询和需要生成的报告。

### 路线

```
const express = require('express')

const { blogpost } = require('../controllers')

const router = express.Router()

router.post('/blogpost', blogpost.postBlogpost)

module.exports = router 
```

Enter fullscreen mode Exit fullscreen mode

从上面的代码中可以看出，在您的`routes/routers`中不应该有任何逻辑。他们应该只将您的`controller`函数链接在一起(在这种情况下，我们只有一个函数)。所以`routes`很简单。导入您的控制器并将功能链接在一起。

I *通常*每条路线只有一个控制器，当然也有例外。如果您有一个处理身份验证的控制器，并且有需要身份验证的路由，那么您显然也需要导入它，并将其连接到您的路由。

除非你有大量的`routes`，否则我通常会把它们都放在一个`index.js`文件中。如果你*有*大量的路线，你可以将它们放入单独的路线文件，将它们全部导入一个`index.js`文件，然后导出。

如果你想了解如何避免手动将“/api”添加到每条单独的路由前面，[看看我在](https://www.coreycleary.me/avoid-manually-prepending-api-to-every-express-route-with-this-simple-method/)上写的另一篇文章。

### 控制器

```
const { blogService } = require('../services')

const { createBlogpost } = blogService

/*
 * call other imported services, or same service but different functions here if you need to
*/
const postBlogpost = async (req, res, next) => {
  const {user, content} = req.body
  try {
    await createBlogpost(user, content)
    // other service call (or same service, different function can go here)
    // i.e. - await generateBlogpostPreview()
    res.sendStatus(201)
    next()
  } catch(e) {
    console.log(e.message)
    res.sendStatus(500) && next(error)
  }
}

module.exports = {
  postBlogpost
} 
```

Enter fullscreen mode Exit fullscreen mode

我认为`controllers`是“指挥者”。他们称之为`services`，其中包含更多“纯粹的”业务逻辑。但是，`controllers`本身除了处理请求和调用`services`之外，并不包含任何逻辑。`services`完成大部分工作，而`controllers`协调服务调用并决定如何处理返回的数据。

如果这还不明显，它们接受从路由转发的 HTTP 请求，或者返回一个响应，或者继续调用链。它们也将 HTTP 状态代码作为响应的一部分进行处理。

### 为什么 Express/HTTP 上下文应该在这里结束

我经常看到的是 Express `req`对象(这是我们的 HTTP“上下文”)通过`routes`和`controllers`传递到`services`甚至`database access layer`。但问题是，现在应用程序的其余部分不仅依赖于请求对象，还依赖于 Express。如果你要换出框架，找到`req`对象的所有实例并删除它们将需要更多的工作。

这也使得测试变得更加困难，并且这并没有实现我们在设计应用程序时努力实现的关注点分离。

相反，如果您使用析构从`req`中提取您需要的数据片段，您可以简单地将它们传递给服务。快速逻辑*就在控制器那里“结束”。*

 *如果您需要从您的一个服务中调用外部 API，那也没问题，我们将在讨论`services`中的逻辑时对此进行更多的讨论。但是现在我们知道这些调用在您的应用程序的*的 HTTP 上下文之外。*

这样，我们就知道将 REST API 将要处理的“初始”逻辑放在哪里(routes + controllers)。在业务逻辑层上...

### 服务

```
const { blogpostDb } = require('../db')

/*
  * if you need to make calls to additional tables, data stores (Redis, for example), 
  * or call an external endpoint as part of creating the blogpost, add them to this service
*/
const createBlogpost = async (user, content) => {
  try {
    return await blogpostDb(user, content)
  } catch(e) {
    throw new Error(e.message)
  }
}

module.exports = {
  createBlogpost
} 
```

Enter fullscreen mode Exit fullscreen mode

应该包含大部分业务逻辑:-封装业务需求、调用数据访问层或模型、调用节点应用程序外部的 API 的逻辑。总的来说，包含了你大部分的算法代码。

你当然也可以从你的`controllers`内部调用外部 API，但是想想那个 API 是否返回了应该是“单元”一部分的东西。`Services`应该最终返回一个内聚的资源，因此如果外部 API 调用返回的内容需要扩充您的业务逻辑，就将逻辑保留在那里。

例如，如果创建 blogpost 的一部分也是发布到 Twitter 的链接(一个外部 API 调用)，您应该将它放在上面的服务中。

*如果这就是这个服务所做的，为什么不直接从`controllers`调用模型/数据层呢？*

虽然我们上面的例子很简单，因为它所做的只是通过我们的数据访问层函数- `blogpostDb`访问数据库-随着更多业务需求的增加，您可以添加 Twitter API 调用、需求变更等。它会变得复杂**快速**。

如果您的控制器处理所有的逻辑，加上它已经处理的请求处理逻辑，它将开始变得非常难以测试，非常快。请记住，控制器可以进行多个不同的服务调用。因此，如果您将其他服务中的所有逻辑都取出来，放在同一个控制器中，就会变得更加难以管理。你最终会陷入可怕的“肥胖控制者”噩梦。

### 数据访问层/模型

```
const blogpostDb = (user, content) => {
  /*
   * put code to call database here
   * this can be either an ORM model or code to call the database through a driver or querybuilder
   * i.e.-
    INSERT INTO blogposts (user_name, blogpost_body)
    VALUES (user, content);
  */
  return 1 //just a dummy return as we aren't calling db right now
}

module.exports = {
  blogpostDb
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我没有建立一个完整的数据库连接，只是对它进行了伪代码化，但是添加它非常容易。当您像这样隔离您的逻辑时，很容易使它仅限于数据访问代码。

如果不明显的话，“数据访问层”是指包含访问持久数据的逻辑的层。这可以是数据库、Redis 服务器、Elasticsearch 等。所以当你需要访问这些数据的时候，把这个逻辑放在这里。

“模型”是相同的概念，但是作为 ORM 的一部分使用。

尽管两者不同，但它们包含相同类型的逻辑，这就是为什么我建议将两者都放在一个`db`文件夹中，这样就足够通用了。无论您是使用 ORM 中的模型，还是使用查询构建器或原始 SQL，您都可以将逻辑放在那里，而无需更改目录名。

### Utils

我们将讨论的最后一种逻辑是公共逻辑函数，它们不一定特定于您的业务逻辑或领域，甚至不一定是一般的 REST API。**实用函数**的一个很好的例子是将毫秒转换为分钟和/或秒的函数，或者检查两个数组以查看它们是否包含相似的项的函数。这些足够通用——而且*足够可重用——它们应该放在自己的文件夹中。*

我的首选方法是将这些全部放入一个`index.js`文件中，然后导出每个函数。我就说到这里，因为它们对项目结构的其余部分没有真正的影响。

### app.js / server.js

```
const express = require('express')
const bodyParser = require('body-parser')
const app = express()
const routes = require('./routes')

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));

app.get('/', (req, res) => res.send('App is working'))

app.use('/api', routes)

app.listen(3000, () => console.log('Example app listening on port 3000!'))

module.exports = {
  app
} 
```

Enter fullscreen mode Exit fullscreen mode

为了将它们联系在一起，我在项目结构的根中包含了一个示例入口点(通常命名为`app.js`或`server.js`)。您可以在这里添加中间件(如`bodyParser`)并导入您的 routes 文件。

### 现在，出现的结构

[![](../Images/cc2643d15aac0308ecd9c51ac5dc2ecb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H1J5o_xH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.coreycleary.me/wp-content/uploads/2018/11/role-structure.png)

当你像我在这篇文章中描述的那样分离你的逻辑时，结构看起来像上面那样“自然地”到位。这就是为什么我喜欢在这样的 Express 应用程序中分离我的逻辑，这样很容易找到放东西的地方！

当然，如果你认为合适的话，你可以在这个结构中添加更多的目录(比如一个`config`文件夹)。但是这是一个很好的起点，如果你按照描述的那样分离你的逻辑，90%的代码将会落入这些文件夹中。

### 最后但同样重要的是，测试！

既然我们已经讨论了遵循这种模式将会出现的结构，那么就有必要指出测试的方向。我不认为这是一个严格的规则，但是我倾向于将我的测试放在一个根`tests`文件夹中，并模仿应用程序其余部分的结构。

[![](../Images/fdc19c15171ddc17498836302347469b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6TB43NKE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.coreycleary.me/wp-content/uploads/2018/11/tests-struc.png)

*如果你注意到了，`routes`不见了！这是因为如果你像我一样把你的逻辑分离出来，你真的不需要测试路线。如果您愿意，您可以使用类似于`supertest`的东西，但是核心逻辑——随着代码的变化更容易中断的东西！-已经在您的控制器、服务等中测试过。*

或者，您也可以在每个“层”文件夹中添加一个测试文件夹，例如，一个测试目录在控制器中，一个在服务中，等等。只是看喜好，不要为此苦恼。

另外，一些开发人员喜欢通过**单元测试**和**集成测试**来分离测试目录。我的想法是，如果你有一个应用程序，其中有一个清晰的描述，并且你有一个*批次*的集成测试，把它们分开可能是好的。但是更多的时候，我把它们放在同一个目录中。

### 包扎

就像我在这篇文章开头所说的，我不相信有一个“最佳结构”。相反，确保您已经将您的逻辑划分到角色中会更有帮助。**会给你一个副产品的结构，同时给你你需要的灵活性，让你以后可以很容易地做出改变。**

 **因此，如果你正在开始一个新的 Express 项目，并浪费时间决定应该创建哪些文件夹，应该给它们起什么名字，应该在它们里面放什么——或者如果你正在重构一个现有的 Express 应用程序——使用我在这里描述的方法来让你摆脱困境，开始行动。从那以后就不用担心了。

记住，只要你的逻辑分离是合理的，你随时可以改变它！

### 还有一件事！

我能说的就这么多了，不会让你觉得太多，也不会让你很快关上窗户。我将在不久的将来用额外的结构/逻辑分离文章对此进行补充。

如果你想要那些额外的文章直接通过电子邮件发送给你，[这里是那个链接](https://www.coreycleary.me/about/)来订阅我的时事通讯！除了备忘单、快速提示等等之外，我每一两周都会发一些新文章。***