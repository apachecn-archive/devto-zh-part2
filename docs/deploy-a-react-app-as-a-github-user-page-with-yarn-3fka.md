# 使用 Yarn 将 React 应用程序部署为 Github 用户页面

> 原文：<https://dev.to/javascripterika/deploy-a-react-app-as-a-github-user-page-with-yarn-3fka>

*tldr；参见解决方案*

我一直致力于重做我的投资组合网站，它仍然是一个 WIP -这就是说[到目前为止](https://javascripterika.github.io/)。我需要在移动视图和其他一些任务中完善我的投资组合部分功能，我还需要重构代码。我觉得已经完成了 90%了。

它需要一个巨大的改变，以更好地反映我去年以来的技能。我认为大约每年一次的重新设计是不断重申和更新投资组合的好时机。我从用 Foundation 和 jQuery 到 React，Flexbox，还有 p5js！

说到 React，在我开始我的作品集之前，我想确保我可以轻松地将我的项目发布为 Github 用户页面！你知道在哪里你可以进入一个网站:`https://yourUserName.github.io/`而不是一个项目页面:`https://yourUserName.github.io/yourProjectRepo`。我总是得到那些困惑的命名，然后忘记它们。谢天谢地**你可以**用`gh-pages`包部署成用户页面或项目页面！

我开始需要在移动设备上测试我的网站，并不得不四处挖掘，找出如何将一个`create-react-app`项目部署为用户页面。如果你想通过`create-react-app`、[部署一个项目页面，请查看这里的文档](https://github.com/gitname/react-gh-pages)——在作为用户页面部署和项目页面部署之间有一些微妙的区别，这些区别在项目页面中并没有完全涵盖。

我错误地经历了上述步骤，没有意识到会有差异。我部署了我的项目！！我的项目没有出现在`https://javascripterika.github.io/`上，我的`master`分支拥有所有的构建代码。我想将变更推送到我的`master`分支，但是当然，我不打算引入 5k 变更的构建代码，并且我不能不引入就推变更。哇，现在我有一个随机的`gh-pages`分支？而我的主人只是显示为所有的 HTML(那个又大又长的红色条)。WTF！不不。我的 JavaScripts 在哪里？？！😭

[![react-master](img/eb9fa6979e2fee3c38de7fee1ae22f36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c8uwC-X5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/19316487/40633973-bf8fd6b8-62c0-11e8-9fc7-7efc0efa8400.PNG)

## 解

*本教程假设你有一个工作项目，并创建了你的回购作为`username/username.github.io`，正在使用`create-react-app`，和纱*

在项目的当前目录中:

`$ yarn add gh-pages`

在您的`package.json`文件中，添加

```
"homepage": "https://yourUserName.github.io/", 
```

Enter fullscreen mode Exit fullscreen mode

*   我把这个加在了`"dependencies"`的正上方

仍然在您的`package.json`文件中，在`"scripts"`中添加以下内容:

```
"predeploy": "yarn run build",
"deploy": "gh-pages -b master -d build", 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们创建一个新的分支...由于`master`将很快包含我们的构建文件，并且变得迷你、捆绑和怪异，我们需要一个新的分支作为我们的源代码——这样我们就可以进行修改，并且不会丢失我们漂亮的人可读代码。

在`master`分支中，让我们创建`source`分支...你想叫它什么都可以。

`$ git checkout -b source`
T1】

耶！我们的`source`分支是我们的`master`的直接副本。现在，在 Github 上，我们需要将我们的默认分支`master`更新到我们的`source`分支...这也是*为什么*我们推它(所以 Github 知道它的存在)。

在 Github 上导航到您的 repo，然后选择“设置”。
在左侧面板上，点击“分支机构”。
接下来，您可以选择您的`source`分支，并在默认分支标题下更新它。

[![setdefaultbranch](img/be0376f6776a71283c4103f03cff8859.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2-LwGese--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/19316487/40635304-7460f17a-62c7-11e8-9442-3d8d6b7a2eeb.PNG)

现在，当您通过终端进入`source`分支时，运行:

`$ yarn deploy`

还有！就是这样！您的主分支将包含构建代码。给它几分钟时间，然后在`https://yourUserName.github.io/`访问您的网站

### 做出改变

假设您想要进行更改，您的`source`分支就像您的`master`一样。所以使用`source`分支，并根据需要将你的分支合并到`source`中。除了下面提到的，你现在不需要用`master`做任何其他事情。

假设我在`source`中做了一些“测试”更改。我照常把它推给 github:

`$ git push origin source`

切换到终端中的`master`、`git merge source`或您想要的任何分支，切换到您的`source`分支并运行:

`$ yarn deploy`

这就对了。这些变更被发布和部署。

### 快速侧记

我最终删除了我的`gh-pages`分支，并重试了部署，它工作得很好！我不再有 HTML 的大红色条，因为我的默认分支是`source`，我的更改生效了！现在这个世界一切都好！

*如果在您部署后通过 Github 创建了一个`gh-pages`分支，请继续删除它！根据 Github，“用户页面必须从主分支构建。”如果你在“设置”下导航到你的 repo，向下滚动到“Github 页面”部分，你会在“来源”下看到一个灰色的选项！*

* * *

### 更新和编辑 5/30

我正在做我的项目，意识到一个重大问题！很可能我们大多数人会将用户页面作为作品集页面，并通过 Github 链接到我们的项目页面。

**如果你使用 React 路由器**，服务人员会缓存你的项目页面&他们不会正确定向(我的所有项目页面都显示为空白页，是我的投资组合的一部分，只有它的菜单)...无论您是否点击应用程序中项目页面的链接，或者在浏览器中复制并粘贴链接，都会发生这种情况。即使你没有在你的用户页面中使用项目页面，它仍然会受到影响！

要解决这个问题，您必须删除服务工作者文件以及在您的`index.js`文件中对它的任何引用...&记得清空你的浏览器缓存(硬刷新是不够的)。