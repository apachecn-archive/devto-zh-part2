# winds——一个关于如何为开源软件做出第一份贡献的深度教程

> 原文：<https://dev.to/nickparsons/winds--an-in-depth-tutorial-on-making-your-first-contribution-to-open-source-software-1g9h>

[![](../Images/24535603f9a75889a62141039e080ed7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pnPRTBdw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w7pmwrfoix49b9lssb9q.png)

在 [Stream](https://getstream.io/try-the-api) 的团队喜欢构建开源示例应用程序来展示我们 API 的功能。我们的观点一直是，最好在一个全功能的平台上展示我们产品的功能。在这种情况下，利用 Stream 和其他优秀的服务，我们可以在几个月而不是几年内构建一个播客和 RSS 阅读器 [Winds](https://getstream.io/winds) 。此外，作为一个[开源](https://github.com/getstream/winds)项目，Winds 由于其不断增长的用户群的贡献而变得越来越好(现在超过 14，000 名用户和大约 5，500 名明星！).

在本帖中，我们将向您简要介绍 Winds - Stream 最受欢迎的开源示例应用程序是如何构建的。如果你对风不熟悉，你可以在这里阅读更多相关信息。我们将从添加一个需要我们接触应用程序前端和后端多个方面的特性的详细演练开始。

在这篇文章结束时，你将准备好为 Winds 添加你自己的特性，并为开源社区做出贡献！无论你是新程序员还是老手，我们相信你会学到新东西。😀

**请注意，本教程假设如下**:

1.  您正在运行 macOS，或者了解如何在您选择的 OS 上安装各种所需的依赖项。🎁
2.  你懂 JavaScript 吗🤔
3.  您对 React 有基本的了解(如果没有也没关系，但它很有帮助)💻
4.  您对 git 有所了解(我们不会深入探讨，但需要一般知识)。🔦
5.  你非常渴望学习如何逆风编码！💥

**我们开始吧！**

# 系统依赖🧙‍

您可能知道，每个应用程序都需要系统范围的依赖关系。为了确保我们不偏离主题，让我们只讨论 macOS 的安装。

## 1。公司自产自用

对于那些刚接触编码的人来说， [Homebrew](https://brew.sh/) 是一个处理系统依赖安装的神奇工具。在一个简单的命令中，您可以安装您选择的编码语言，或者使用 Homebrew 的屏蔽功能在您的机器上安装成熟的应用程序。如果你没有安装家酿软件，你可以用下面的命令安装:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

Enter fullscreen mode Exit fullscreen mode

一旦你把自制程序都准备好了，我们就可以进入下一步了...

## 2。节点. js

Node.js 在整个项目中被大量使用——主要用于 API 和测试套件。也就是说，让我们确保您运行的是最新版本的 node。写这篇文章的时候 Node.js 在 **v10.7.0** (而且经常换)。如果你已经安装了 Node.js，你可以用下面的命令检查你的节点版本:

```
node --version 
```

Enter fullscreen mode Exit fullscreen mode

> **注意**:我们假设你运行的是 node 的最新版本。如果没有安装 Node.js，可以通过以下方式之一进行安装:

### a)自制

`brew install node`

**或**

### b) NVM(推荐)

NVM 或节点版本管理器是一种流行的开源工具。它允许您用一个简短的命令在 Node.js 版本之间跳转。一切都被记录在这里。安装非常简单，只需遵循以下步骤:

**步骤 1** :安装 NVM:

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash 
```

Enter fullscreen mode Exit fullscreen mode

**第二步**:安装 Node.js 的最新版本:

```
nvm install 10.7.0 
```

Enter fullscreen mode Exit fullscreen mode

> **Pro 提示**:你可以运行命令 **nvm ls-remote** ，它会在你的控制台中列出所有版本，包括新版本。现在，如果您运行**节点版本**，您应该会看到您安装的最新版本。

[![](../Images/2a7eac19b151ee28d30979a8f8e2fe45.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b4y-s8v2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image5-3.png%3Fw%3D1024%26h%3D1024)

## 3。MongoDB

MongoDB 是我们存储用户数据、RSS、播客等等的主要数据库。我们使用 MongoDB Atlas，它是由 MongoDB 构建和维护的 MongoDB 的托管版本。

`brew install mongodb`

> **注意**:启动 MongoDB 的命令是 **brew services start MongoDB** 。

## 4。再说一遍

Redis 很重要，因为它是我们处理 RSS 和播客提要的工作队列。我们还使用 Redis 对不更新的项目进行一些基本的缓存(比如兴趣)。

`brew install redis`

> **注意**:启动 Redis 的命令简单来说就是 **redis-server** 。

完整的命令列表可以在[这里](https://redis.io/commands)找到。

## 4。故事

[Yarn](https://yarnpkg.com/) 是 npm(节点包管理器)的替代品。我们推荐 yarn over npm，因为我们发现它更可靠，并且对于 Node.js 依赖项来说是一个整体上更好的包管理器。

`brew install yarn`

## 全局纱线依赖性🌎

有一个 Node.js 依赖项我们需要是本地的，为此，我们将使用 [Yarn](https://yarnpkg.com/en/) 。依赖关系是 [PM2](https://pm2.io/) ，一个我们稍后会谈到的过程管理器。现在，运行以下命令来安装 PM2:

`yarn global add pm2`

## 克隆回购💾

现在您已经安装了所有必需的依赖项，所以让我们继续克隆存储库。你可以从 [GitHub](https://github.com/getstream/winds) 获取 URL，或者你可以使用下面的命令(只要确保你正在克隆到一个对你有意义的目录中(例如~ ~/Code))。

`git clone git@github.com:GetStream/Winds.git`

如果一切顺利，您的终端将类似于下面的屏幕截图:

[![](../Images/afbdef93c66a8ae05a8927fe61e56997.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OUa4sOC2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image30.png%3Fw%3D1024%26h%3D1024)

## 设置第三方服务👨‍👨‍👧‍👦

Winds 依靠一些第三方资源来运行。所有的外部服务都有 API 密匙/秘密和其他值，你需要保存这些信息，以备后文使用——我推荐使用 macOS 中的 Notes 应用程序。总的来说，您大约需要 15-20 分钟来完成。

> **注意**:运行 Winds 所需的所有服务在一定程度上都是免费的(一般是生产数字)，所以现在不用担心费用。我们推荐使用的服务都不需要信用卡。

### 1。Mercury Web 解析器(大约 2 分钟)

由 [Postlight](https://postlight.com/) 开发的 Mercury Web Parser 在 Winds 中发挥了巨大作用。它确保了我们解析的所有 RSS 文章都去除了脚本标签和其他在呈现之前注入到 HTML 中的杂乱代码。

要注册 Mercury，请打开[主页](https://mercury.postlight.com/web-parser/)，点击“注册”。一旦你完成了这些，获取提供的 API 密匙并把它保存在一个特别的地方。

**第一步:**

[![](../Images/096b19fef917fd9fb93f7fbb87ead3cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d3SurHdp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image15.png%3Fw%3D1024%26h%3D1024)

**第二步:**

保存生成的 API 密钥。

[![](../Images/7ba482c741612109d83c00924f4bb4ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V4jIhMm7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image39.png%3Fw%3D1024%26h%3D1024)

### 2。流(大约 5 分钟)

[Stream](https://getstream.io) 为应用程序中的提要以及个性化内容建议提供动力。

**第一步**:

前往 Stream [网站](https://getstream.io/)，点击“注册”按钮。

[![](../Images/4b2c6d6f9400e5f4d9ecf9f9b51fc1fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xIklvNV9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image8-2.png%3Fw%3D1024%26h%3D1024)

**第二步:**

点击“查看仪表板”，如下图中突出显示的。或者，先玩玩 API。😀

[![](../Images/7b6200701acb6757f913a3b6ab4639e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kGJrtYXx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image18.png%3Fw%3D1024%26h%3D1024)

**第三步:**

点击“创建应用”并填写详细信息。请注意，应用程序名称**必须是全球唯一的**——我建议用您的名字作为前缀，因为这将是一个测试项目。

[![](../Images/3457632acd205603b80e625c4619096f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CDTWF47c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image6-2.png%3Fw%3D1024%26h%3D1024)

**第四步**:

接下来，我们需要在 Stream 中配置我们的"[提要组](https://getstream.io/docs/#creating-feeds)。所需的进给组位于 [GitHub](https://github.com/getstream/winds#create-your-stream-feed-groups) 上。

1.  **播客**(平)
2.  **rss** (平面)
3.  **用户**(平板)
4.  **时间轴**(平面)
5.  **用户 _ 剧集**(平)
6.  **用户 _ 文章**(平面)

**第五步**:

最后，让我们继续获取 Stream 的凭据。在您创建的提要组下，您应该看到一个包含您的“Key”和“Secret”的部分。

请保留这些，因为我们稍后在设置过程中会用到它们。

[![](../Images/2636debce130a2b5324ae7166da9b30d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1h8_Qd5V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image36.png%3Fw%3D1024%26h%3D1024) 你还需要获取位于页面顶部的“应用 ID”。

[![](../Images/cc956165a8a03cd13526c27c6bcd216f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CauQtQ8s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image4-4.png%3Fw%3D1024%26h%3D1024)

Stream 到此为止！

### 3。阿尔戈利亚语(约 10 分钟)

阿尔戈利亚能源搜索风。这是应用程序的一项关键技术，在用户体验中扮演着重要角色。**第一步** : Algolia 超级容易设置；我们只需要去他们的[网站](https://www.algolia.com/users/sign_up)创建一个账户。

[![](../Images/d6cc48514e80ddf6f64995dc44242e72.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YGUtz-Sj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image12-1.png%3Fw%3D1024%26h%3D1024)

**第二步:**

接下来，填写 Algolia 所需的信息。

[![](../Images/fb3db9b40943f840177be5334fde5037.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QTjkUSUG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image22.png%3Fw%3D1024%26h%3D1024)

**第三步**:

选择您的数据中心。出于本教程的目的，这无关紧要；但是，我将选择离我最近的美国中部。

[![](../Images/073625bd72d05d66964549f48f322f8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qUbcX4Ik--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image32.png%3Fw%3D1024%26h%3D1024)

**第四步**:

选择“其他”作为您正在构建的应用程序的类型，并在下拉列表中选择“尽快”。然后点击“完成”结束。

[![](../Images/f7dc9683e68f823008ccc486a065cad8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AbdpgAmI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image9-2.png%3Fw%3D1024%26h%3D1024)

**第五步**:

这个过程的下一步是创建一个索引，所有的 Winds 可搜索数据都将存储在这个索引中。要绕过入职流程，请通过此链接直接进入仪表板[。然后点击左栏中的“索引”按钮。页面加载后，单击“添加新索引”按钮生成一个索引。随便你怎么命名，但是一定要写下你的索引的名字。我要把我的命名为“dev_Winds”。](https://www.algolia.com/dashboard)

[![](../Images/6534b477b0bd3653512e5281c206ceb4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XpyznBek--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image28.png%3Fw%3D1024%26h%3D1024)

**第六步**:

该过程的最后一步是获取我们的“应用程序名称”、“仅搜索 API 密钥”和“管理 API 密钥”。两者都可以在页面右侧“API 密钥”部分的“API 密钥”下找到。请将这些凭据放在手边，以便以后在设置过程中使用。

[![](../Images/d35af11758c9d7d28a8f9215fadb4089.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YXK38yfe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image27.png%3Fw%3D1024%26h%3D1024)

### 4。哨兵(~2 分钟)

哨兵是我们工具箱中另一个最重要的工具。Sentry 捕获后端 API 中出现的错误，允许我们在用户知道之前就进行错误修复。

**第一步**:

在这里创建一个新账户[。](https://sentry.io/signup/)

[![](../Images/65d03a8aeb0ee635c707f8d7475f74d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6DTkIvHZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image25.png%3Fw%3D1024%26h%3D1024)

**第二步**:给你的项目起个名字。我把我的叫做“风”,因为我们正在做风的项目。😀

[![](../Images/327a78706df8089ac013f75ce80f9612.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--loxYtfuj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image13.png%3Fw%3D1024%26h%3D1024)

单击“创建项目”,您将被重定向。

**第三步**:

通过单击“已经设置好了吗？获取您的 DSN。”

[![](../Images/5658ca565756de92b037db2191d70aa6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b_PG93pD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image29.png%3Fw%3D1024%26h%3D1024)

复制这个值，因为我们将在接下来的部分中用到它。

# 克隆回购📀

要开始下一步，您需要从 GitHub 克隆存储库。您可以使用以下命令来完成此操作:

`git clone git@github.com:GetStream/Winds.git`

太好了！现在您已经克隆了 repo，让我们继续用 yarn 安装所需的依赖项。

## 风 API

您将希望进入 **/api** 目录，并运行 **yarn** 命令。这里有一个小片段可以帮助你:

`cd winds/api && yarn install`

[![](../Images/fe78df6a907f8f18fb0f8a7ccac5cfc8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E-B22EyQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image42.png%3Fw%3D1024%26h%3D1024)

## 大风 App

假设您在 **/api** 目录中，您可以移出并移入 **/app** 目录来进行 yarn 安装。

`cd ../app && yarn install`

> **注意** : API 和 App 有单独的 package.json 文件。虽然这可能会令人困惑，但这是必要的，这样我们就不会膨胀每个目录——即使它们在同一个存储库中，目录也是作为单独的应用程序部署的。

[![](../Images/556550af202ca077158f59cb3b0b0ae5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tiV4Rmfq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image24.png%3Fw%3D1024%26h%3D1024)

# 打造

在我们继续之前，我想花点时间讨论一下这个网站的前端和后端结构。对于任何应用程序，理解其背后的架构和思维过程是非常重要的。

## 风前端

Winds 的前端部分非常简单。我们使用 Create React App (CRA)来引导应用程序，然后开始开发过程。前端代码可以在这里找到:[https://github.com/GetStream/Winds/tree/master/app](https://github.com/GetStream/Winds/tree/master/app)

## 大风后端

后端 API 比前端稍微复杂一些。除了由 Node.js 提供支持之外，后端还处理几乎所有的业务逻辑——与第三方服务通信，协调工作人员解析 RSS、播客和开放图形数据等。后端可以看这里:[https://github.com/GetStream/Winds/tree/master/api](https://github.com/GetStream/Winds/tree/master/api)。

### ES6 标准

我们使用的几乎所有代码都是用 ES6 编写的。这允许我们在保持代码可读的同时保持较小的内存占用。

### API 路线

路线相当简单。顾名思义，它们将请求路由到期望的目的地。下面是一个简短的路由文件示例:

```
import Playlist from '../controllers/playlist';
import { wrapAsync } from '../utils/controllers';

module.exports = api => {
    api.route('/playlists').get(wrapAsync(Playlist.list));
    api.route('/playlists/:playlistId').get(wrapAsync(Playlist.get));
    api.route('/playlists').post(wrapAsync(Playlist.post));
    api.route('/playlists/:playlistId').put(wrapAsync(Playlist.put));
    api.route('/playlists/:playlistId').delete(wrapAsync(Playlist.delete));
}; 
```

Enter fullscreen mode Exit fullscreen mode

> **注意**:该路线被包装在一个 **wrapAsync()** 函数中。这个函数捕获任何冒泡的错误，并将它们发送给 Sentry。

[![](../Images/a491ba3ae5bcdac1afaa2c271cf82842.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ONve5bDe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image14.png%3Fw%3D1024%26h%3D1024)

### API 控制器

控制器由路由文件调用，包含 API 中的大部分(如果不是全部)业务逻辑。控制器与模型通信，这允许它们与数据库对话。

[![](../Images/acd9d836e28bffe42c8c8e55c4f75201.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rdUrmmzS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image16.png%3Fw%3D1024%26h%3D1024)

### API 型号

本质上，模型是 API 的核心基础。它们通过实施所谓的“模式”来提供后端数据存储(MongoDB)的结构。

模式包含各种类型，如“字符串”、“布尔”等。下面是我们的用户模式的一个简短示例(为了缩短示例，我删除了一些助手函数，所以请务必查看代码):

```
import mongoose, { Schema } from 'mongoose';
import bcrypt from 'mongoose-bcrypt';
import timestamps from 'mongoose-timestamp';
import mongooseStringQuery from 'mongoose-string-query';

import FollowSchema from './follow';
import PinSchema from './pin';
import ListenSchema from './listen';

import PlaylistSchema from './playlist';
import jwt from 'jsonwebtoken';
import config from '../config';
import gravatar from 'gravatar';
import { getStreamClient } from '../utils/stream';

export const UserSchema = new Schema({
    email: {
        type: String,
        lowercase: true,
        trim: true,
        index: true,
        unique: true,
        required: true
    },
    username: {
        type: String,
        lowercase: true,
        trim: true,
        index: true,
        unique: true,
        required: true
    },
    password: {
        type: String,
        required: true,
        bcrypt: true
    },
    name: {
        type: String,
        trim: true,
        required: true
    },
    bio: {
        type: String,
        trim: true,
        default: ''
    },
    url: {
        type: String,
        trim: true,
        default: ''
    },
    twitter: {
        type: String,
        trim: true,
        default: ''
    },
    background: {
        type: Number,
        default: 1
    },
    interests: {
        type: Schema.Types.Mixed,
        default: []
    },
    preferences: {
        notifications: {
            daily: {
                type: Boolean,
                default: false
            },
            weekly: {
                type: Boolean,
                default: true
            },
            follows: {
                type: Boolean,
                default: true
            }
        }
    },
    recoveryCode: {
        type: String,
        trim: true,
        default: ''
    },
    active: {
        type: Boolean,
        default: true
    },
    admin: {
        type: Boolean,
        default: false
    }
});

UserSchema.plugin(bcrypt);
UserSchema.plugin(timestamps, {
    createdAt: { index: true },
    updatedAt: { index: true }
});
UserSchema.plugin(mongooseStringQuery);

UserSchema.index({ email: 1, username: 1 });

module.exports = exports = mongoose.model('User', UserSchema); 
```

Enter fullscreen mode Exit fullscreen mode

要获得模式类型的完整列表，请看一下[mongose 网站](http://mongoosejs.com/docs/schematypes.html)。

[![](../Images/ac6a39a8daeb86445547103497658cbb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XQPzeYiz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image40.png%3Fw%3D1024%26h%3D1024)

### API 工人

工人执行非常特殊的任务，否则会阻塞进程。例如，我们使用专门的任务来处理 RSS 提要、播客提要、打开的图形图像等等。如果没有针对这些任务的专用流程，我们的 API 将很快停止，用户将无法及时收到响应消息——API 可能会超时。

我们的工作人员使用 [Bull Queue](https://github.com/OptimalBits/bull) ，这是 Redis 的排队基础设施。基本上，我们的 API 使用 Bull Node.js 库插入一个对 Redis 的调用，然后我们的工人拾取作业并异步处理它。

例如，下面是来自 **Podcast.js 控制器**的代码，它在用户将一个播客添加到系统后添加该播客(注意我们是如何添加高优先级 1 的):

```
let scrapingPromise = PodcastQueueAdd(
    {
        podcast: p._id,
        url: p.feedUrl,
    },
    {
        priority: 1,
        removeOnComplete: true,
        removeOnFail: true,
    },
); 
```

Enter fullscreen mode Exit fullscreen mode

从那里，接下来的事情发生了:

1.  [指挥](https://github.com/GetStream/Winds/blob/master/api/src/workers/conductor.js)拾取需要处理的任务
2.  通知文件 [podcast.js](https://github.com/GetStream/Winds/blob/master/api/src/workers/podcast.js) 它有工作要做(处理传入的工作)
3.  数据库中充满了已填充的剧集
4.  通知用户有新的播客可用

[![](../Images/246dfa5ef2df9b1a7d59a8ca243daca2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C-Jvzp3C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image10-2.png%3Fw%3D1024%26h%3D1024)

### CLI 命令

commands 目录保存了特定 Winds 相关任务的代码——这是 Winds API 的一个简单而强大的 CLI 在您需要调试 RSS 提要时尤其有用。如果你感兴趣，这里的列出了入门指南和所有命令。

运行**winds RSS[https://techcrunch.com/feed/](https://techcrunch.com/feed/)T3】的输出示例:**

[![](../Images/38a0195f85578a9db63d87e62e650511.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x4HHDrSa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image33.png%3Fw%3D1024%26h%3D1024)

### API 测试

考试是用摩卡和柴写的。欢迎您在任何时候运行测试套件(找到需要修复的东西也无妨)。目前，只有 Workers 和 API 有覆盖率——我们仍在努力达到 100%的目标；然而，jest 的前端覆盖将很快到来！

[![](../Images/56ce2ccd34acf76c208f2efc6f8fbe4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4UC-_Pyz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image3-5.png%3Fw%3D1024%26h%3D1024)

# 风投环境🌪️

有两个地方需要一个. env ( [dotenv](https://www.npmjs.com/package/dotenv) )文件来运行应用程序: **/app/。env** 以及 **/api/tests** (假设您将要编写测试)。你需要创建一个**。env** 文件放在 **/app** 中，以便应用程序工作。这里有一个样板**。env** 文件帮助您开始:

```
DATABASE_URI=mongodb://localhost/WINDS # This value can remain as is
CACHE_URI=redis://localhost:6379 # This value can remain as is
JWT_SECRET=YOUR_JWT_SECRET # This should be a 256-bit random string. You can generate one here: https://randomkeygen.com/

API_PORT=8080 # This can remain as is
REACT_APP_API_ENDPOINT=http://localhost:8080 # This can remain as is, unless you're hosting on a server
STREAM_API_BASE_URL=https://windspersonalization.getstream.io/personalization/v1.0 # This can remain as is

STREAM_APP_ID=YOUR_STREAM_APP_ID # This should be the saved value that you wrote down earlier
REACT_APP_STREAM_APP_ID=YOUR_STREAM_APP_ID # this needs to be included twice, once for the backend, and once for the frontend to make realtime connections directly to Stream
STREAM_API_KEY=YOUR_STREAM_API_KEY # This should be the saved value that you wrote down earlier
STREAM_API_SECRET=YOUR_STREAM_API_SECRET # This should be the saved value that you wrote down earlier

REACT_APP_ALGOLIA_APP_ID=YOUR_ALGOLIA_APP_ID # This should be the saved value that you wrote down earlier
REACT_APP_ALGOLIA_SEARCH_KEY=YOUR_ALGOLIA_SEARCH_ONLY_API_KEY # This should be the saved value that you wrote down earlier
ALGOLIA_WRITE_KEY=YOUR_ALGOLIA_ADMIN_API_KEY # This should be the saved value that you wrote down earlier

MERCURY_KEY=YOUR_KEY_HERE # This should be the saved value that you wrote down earlier 
```

Enter fullscreen mode Exit fullscreen mode

> **注意事项**:在线注意事项有助于指导您完成的设置。环境文件

# 奔跑的 PM2🏃

PM2 是一名流程经理，我们在风能领域广泛使用它。这是一个非常强大的工具，我们是这个项目的忠实粉丝，也是维护者。如果出现错误，他们会迅速做出反应，而且最重要的是，它对我们需要做的事情非常有效。

Node.js 的设计是单线程的。这有好有坏——速度极快，但在给定时间内只能进行一次 I/O 操作。在幕后，PM2 使用 Node.js 集群模块，以便扩展应用程序的子进程可以自动共享服务器端口。集群模式允许联网的 Node.js 应用程序跨所有可用的 CPU 进行扩展，而无需修改任何代码。这极大地提高了应用程序的性能和可靠性，具体取决于可用的 CPU 数量。

如果你打算在 Winds 上开发，或者如果你打算在自己的应用程序中使用 PM2，我建议你学习一下 PM2 的命令。老实说，最好的功能是内置的 **watch** 命令——它会自动观察变化，并在必要时重新加载应用程序。*这里是我日常使用的几个命令*:

*   **pm2 启动 process_dev.json(通过 process_dev.json 文件中设置的命令启动进程)**
*   **pm2 列表(列出所有正在运行的进程)**
*   **pm2 重启所有**(重启 pm2 管理的所有正在运行的进程)
*   **pm2 日志**(跟踪各个进程正在吐出的日志)

> **注意**:使用 ctrl+c 退出拖尾日志。

# 我们开始吧👯

你已经走到这一步了。恭喜你！安装了所有依赖项，克隆了 repo，您的。env 设置好了…我们准备好了！

## 创建新的分支

在您的工作目录中，创建一个名为“feature”的新分支。如果你需要，这里有它的代码:

`git checkout -b feature`

## 启动 MongoDB

现在您已经将代码克隆到了您的机器上，让我们继续让 MongoDB 启动并运行。您可以在单独的终端中使用以下命令。

`brew services start mongodb`

> 注意:如果你正在为 MongoDB 寻找一个像样的 GUI，看看 Compass 吧。你可以在这里[下载](https://www.mongodb.com/products/compass)或者运行**brew buck 安装 mongodb-compass** 。

[![](../Images/bf43626b28fc5953e16184f444fd3c19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yQjWBWHM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image41.png%3Fw%3D1024%26h%3D1024)

## 开始再审

与 MongoDB 类似，让我们继续运行 Redis。为此，我喜欢使用本机命令(从您的命令行):

`redis-server`

[![](../Images/c03ba192f8a7d553fe2d7ae08d97f5e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QciKmvlD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image2-4.png%3Fw%3D1024%26h%3D1024) 一旦启动，你应该在终端中看到 Redis 标志(如上图)。

## 启动大风 API &工人

MongoDB 和 Redis 一起运行。现在是起风了。转到 Winds 目录的根目录，运行以下命令:

`pm2 start process_dev.json`

一旦应用程序启动，您应该会看到以下内容:

[![](../Images/cbe70c97cf38d0c41029793b4b3c8575.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ik9O60A5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image35.png%3Fw%3D1024%26h%3D1024)

## 让我们启动 Winds UI

对于 Winds，我们提供了两种启动应用程序 UI 的方法:第一种方法在电子包装器中启动应用程序:

`cd app && yarn start`

[![](../Images/f790b405afddd2f1d9b392d7c17e02e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zwWY5uJ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image37.png%3Fw%3D1024%26h%3D1024)

第二种选择是在 Chrome 浏览器中启动应用程序，这对于调试来说要容易得多:

`cd app && yarn dev`

[![](../Images/4865cc9229c07e2f5e84a2f087f6c355.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7eqM6Mex--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image38.png%3Fw%3D1024%26h%3D1024)

你喜欢哪个就随便选吧！我将使用浏览器版本，因为它更容易浏览 DOM，并且重新加载似乎更快。呜！您已经成功地在您的机器上设置并启动了 Winds！🎉

# 添加新功能🔔

到目前为止，我们已经讨论了很多，但是在向平台添加新功能时，还没有具体的内容。由于这是我们第一次展示如何添加新功能，我们将保持简单——我们将在前端添加一个社交按钮。在继续开发之前，请选择 3 个或更多兴趣并按照指导步骤创建一个帐户。

[![](../Images/6a1a7fa526c0f168ed4d0107926c6649.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dmySSwYA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image31.png%3Fw%3D1024%26h%3D1024)

## 空白状态

登录时不要惊慌。您将看到一个相当空白的屏幕，因为我们尚未添加任何内容。

[![](../Images/1cba6070141d8ee3c6944e6069e8a322.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BfmKwXg7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image23.png%3Fw%3D1024%26h%3D1024) 这很容易用 OPML 文件导入解决😀。

[点击这里下载 OPML 文件](https://s3.amazonaws.com/winds-static/marketing/sample.opml)，然后按照下面的说明将其导入 Winds。

点击“新建”>“新建 OPML”，会出现一个对话框:

[![](../Images/bcdd93c85fbaa5514e859a030276f8ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ybK_hI8t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image21.png%3Fw%3D1024%26h%3D1024)

对话框出现后，将下载的 OPML 文件拖放到拖放区。

[![](../Images/0ee52d458f1b2e58fa2a17f3193aa4d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oVLp0BrP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image20.png%3Fw%3D1024%26h%3D1024)

点击“添加 RSS”。重新加载页面，你应该会看到一个文章列表！

[![](../Images/c2c7d031d69c76ea4a830a7759221e78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2uFBpNuu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image26-1.png%3Fw%3D1024%26h%3D1024)

如果你想知道为什么“精选风”和“发现”部分是空的，有两个原因:

1.  Winds 上的特性要求 MongoDB 数据库标志设置为 true。例如，它必须在 RSS 提要或播客提要上注明“特色:真实”。
2.  发现推荐功能由我们的机器学习提供支持。机器学习需要时间，因为它从你与内容的交互中学习。你与你的内容互动得越多越好。

> **注意**:对于本教程，没有必要启动并运行这两个组件。但是如果您想进行试验，我建议您跳到 MongoDB Compass，将 RSS 提要的“featured”键设置为布尔值 true。

## 开始编码

如前所述，我们将在前端添加一个社交按钮。出于本练习的目的，我们将把它添加到顶级 RSS 提要中。首先，点击 RSS 部分标题:

[![](../Images/caed0002c74d7632359a2ca5ca177501.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UHHEAVAj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image19.png%3Fw%3D1024%26h%3D1024)

接下来，看看每个元素。注意到他们是如何少了一个 Twitter 标志的吗？我们要加上这一点。

[![](../Images/f7c62a9d825382888fd92dd67379a41c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xHThTDql--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image17.png%3Fw%3D1024%26h%3D1024)

> **专业提示**:找到您想要的东西的最简单的方法是找到类名，然后在您的编辑器中搜索它。对于这个组件，我们将寻找两个类——“far fa-bookmark”。

你可以在你的编辑器中搜索它，或者你可以直接进入“app/src/components/article listitem . js”——第 57 行。

[![](../Images/2370dbc2924161758487b6768043cd00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F1wALoBF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image1-5.png%3Fw%3D1024%26h%3D1024) 首先，我们需要包括一个叫做 is 的模块——电子。这个模块确保我们在 web 环境中只显示一个图标(并使用功能)。软件包已经安装好了，您只需要将它添加到文件顶部的导入中，如下所示:

`import isElectron from 'is-electron';`

在第 59 行和第 60 行的之间，我们将添加我们的 Twitter 按钮！

```
{!isElectron() ? (
    <span>
        <a
            href="#"
            onClick={e => {
                e.preventDefault();
                e.stopPropagation();
                this.handleTweet(
                    this.props.title,
                    this.props.url,
                );
            }}
        >
            <i className="fab fa-twitter" />
        </a>
    </span>
) : null} 
```

Enter fullscreen mode Exit fullscreen mode

添加上面的代码片段后，您的代码应该如下所示:

[![](../Images/41e798b998eadbb866112732ed47ea6b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jATiE8D8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image11-1.png%3Fw%3D1024%26h%3D1024)

我们调用函数 **tweet()** ，所以我们要确保我们也创建了它。就在 render 方法之前，创建一个名为“tweet”的新方法。你可以复制并粘贴下面的代码:

```
tweet(title, url) {
    const getWindowOptions = function() {
        const width = 500;
        const height = 350;
        const left = window.innerWidth / 2 - width / 2;
        const top = window.innerHeight / 2 - height / 2;

        return [
            'resizable,scrollbars,status',
            'height=' + height,
            'width=' + width,
            'left=' + left,
            'top=' + top,
        ].join();
    };

    const shareUrl = `https://twitter.com/intent/tweet?url=${url}&text=${title}&hashtags=Winds`;
    const win = window.open(shareUrl, 'Share on Twitter', getWindowOptions());

    win.opener = null;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/1c1f57a7f0c11a2d22c00adc4c232f6f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZbjbceqD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image34.png%3Fw%3D1024%26h%3D1024)

现在，试着点击 UI 中的 Twitter 标志。如果一切顺利，您应该会看到一个 Tweet 对话框，显示文章的标题，旁边是带有标签 Winds 的 URL！

[![](../Images/92656848a276872a155302f3110ad95f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ssLyFK-k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image7-2.png%3Fw%3D1024%26h%3D1024)

呜！你创作了你的第一个关于风的专题——希望是许多专题中的一个！庆祝的时间到了！🍾🥂

如果你对这个过程仍然有点模糊，运行 **git stash** 并再次尝试。一件事多做几次也无妨😉感觉你什么都放下了？让我们看看一些代码！这里有一些想法可以帮助你开始:

*   脸书喜欢纽扣
*   书签支持
*   支持 macOS Mojave 的黑暗模式
*   喜欢(我们的 API 已经为他们提供了支持)
*   常规 CSS 清理
*   常规 JavaScript 清理
*   API 和工人的测试覆盖率

> ***注*** _:我们还聘请了一名全职(合同)开发人员加入团队，从事 Winds 方面的工作。你认为你有这个能力吗？Ping 我 [*这里*](//mailto:nick@getstream.io) 用你的 LinkedIn 个人资料和你作为唯一贡献者所做的任何编码工作的例子。

# 最后的想法🤔

[Winds](https://getstream.io/winds) 是*同类型中最受欢迎的* [开源](https://github.com/getstream/winds)应用——我们非常兴奋。macOS、Linux 和 Windows 都有免费的桌面应用程序，也有网络版本。该应用程序具有几项功能，特别是提要和个性化内容推荐，所有这些都由基于 API 的新闻提要、活动流和个性化服务的领导者 [Stream](https://getstream.io) 提供支持。

谢谢你留下来学习一些关于风的知识！我们希望在不久的将来看到你的一些公关！

**快乐编码**！