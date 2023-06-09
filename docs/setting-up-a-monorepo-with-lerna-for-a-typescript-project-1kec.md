# 使用 Lerna 为 TypeScript 项目设置 monorepo

> 原文：<https://dev.to/bnevilleoneill/setting-up-a-monorepo-with-lerna-for-a-typescript-project-1kec>

[![](img/42a2eb79d4ddcde6d6a50b42d8e99342.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PB6FlykY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABriahqbFqeAW9lXDzuUgzw.png)

使用单个存储库来维护包含所有包的整个项目通常是非常有用的。有传言称，谷歌和脸书对他们所有的项目使用同一个存储库。当公司使用类似的技术，并且项目共享公共的依赖项和文件时，这种解决方案会非常方便。

流行的 JS 项目，如 Angular、React、Meteor、Babel、NestJS 和许多其他项目都使用一个单一的存储库来存储所有的包。事实上，他们中的一些人使用 Lerna。

[**Lerna**](https://lernajs.io/) 是一个流行且广泛使用的用 JavaScript 编写的包，用于用 **NPM** 和 **GIT** 为 Node.js 项目设置和管理多包多项目单库。

Lerna 有两种模式:固定和独立。

固定模式将所有版本的包保持在同一级别。这种方法现在很流行。你可能在 Angular 看过。

[![](img/368338cbbd76e11477359513562d8502.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rii42w_9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/515/1%2APg9JrNskIa60kQX_pLViYg.png)

独立模式允许我们每个包有不同的版本。

在一个存储库中维护所有定制包听起来很诱人，事实上，使用 Lerna 非常容易。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

#### 安装

我们将安装 Lerna 作为一个全局依赖:

```
npm install -g lerna 
```

Enter fullscreen mode Exit fullscreen mode

#### [t1 上的&类型脚本](#lerna-amp-typescript)

由于 **Lerna** 旨在使用 **JavaScript** 而不是 **TypeScript** ，我们将不得不调整这个项目的初始配置。

#### NPM

勒纳与代表节点包管理器的 **NPM** 一起工作。您将需要在那里创建一个帐户:【www.npmjs.com】和组织，因为我们将创建一个带有作用域包的示例:@organization/my-project

*   [如何建立新的 npm 帐户&安装 npm | npm 文档](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)

#### 去吧

我们的包将是公开的，为了这个项目，我们将创建一个 **Github** 库。

### 让我们把手弄脏吧

让我们为名为 **hospital-sdk 的假想项目构建一个由多个包组成的简单项目。**

[![](img/7fe42be4172fbf1b73eb92eab02b3841.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--igJHd7bB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/959/1%2AgwivSLxlisO2rMlteOuUOQ.png)

创建文件夹 hospital，并用
初始化文件夹中的 Lerna

```
lerna init && npm install 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将创建 lerna.json，其中包含一个默认的文件夹结构。

*   /包
*   林娜. json
*   package.json

**/packages** 是我们共享包

**的占位符 lerna.json** 是 lerna 配置文件

```
{
  "packages": [
    "packages/*"
  ],
  "version": "0.0.0"
} 
```

Enter fullscreen mode Exit fullscreen mode

**Lerna** 不创造**。gitignore** 我们将用以下内容创建一个:

。gitignore〔t0〕

```
node_modules/
lerna-debug.log
npm-debug.log
packages/*/lib
.idea 
```

Enter fullscreen mode Exit fullscreen mode

我们将在我们的项目中使用 **TypeScript** ,这样我们可以将它视为一个共享的依赖项。这意味着我们应该在顶层 **package.json** 中添加:

```
npm install typescript @types/node — save-dev 
```

Enter fullscreen mode Exit fullscreen mode

这是一个推荐的方法，因为我们希望在我们的包中使用相同的工具。除了 **TypeScript** 之外，我们还将为 Node.js 安装类型声明。

#### 准备构建脚本

正如我们之前提到的， **Lerna** 旨在与 **Node.js** 一起使用，而不是与 **TypeScript** 一起使用。我们将需要添加额外的配置，使之与**类型脚本**一起工作。

由于这是一个演示项目，我们将假设我们将有几个模块:**病人**、**医生**和**调度**。

#### 创建包

为了创建包，我们将从项目的根目录使用 lerna create terminal 命令。

lerna create doctor 命令将指导我们创建新的包。包的名称很重要。这个包的名字将会是:@hospital-sdk/doctor

[![](img/515f3b74979150106e57b17c3b46f8cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pT1diw8_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/825/1%2ASMRKxkaJI8UNlhxHKhxcQA.png)

我们将对包**患者**和**调度器**重复相同的过程。

结果应该是:

[![](img/8e5ddc1c493010a4e5c1df5becedce6f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--09JkWxJ9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/501/1%2Az4nBnT172voORPe8CoDTXw.png)

因为我们对所有的包都使用了 **TypeScript** ，所以我们将在项目的根中定义一个公共的 tsconfig.json。我们的 tsconfig.json 将看起来像:

```
{
 "compilerOptions": {
   "module": "commonjs",
   "declaration": true,
   "noImplicitAny": false,
   "removeComments": true,
   "noLib": false,
   "emitDecoratorMetadata": true,
   "experimentalDecorators": true,
   "target": "es6",
   "sourceMap": true,
   "lib": [
     "es6"
   ]
 },
 "exclude": [
   "node_modules",
   "**/*.spec.ts"
 ]
} 
```

Enter fullscreen mode Exit fullscreen mode

每个单独的包都有自己的 tsconfig.json，它的扩展根，单独的 tsconfig.json 看起来像:

```
{
 "extends": "../../tsconfig.json",
 "compilerOptions": {
   "outDir": "./lib"
 },
 "include": [
   "./src"
 ]
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们将 tsconfig.json 添加到每个包中之后，我们将在每个包中创建一个 **src** 文件夹，并为该包创建一个 **TypeScript** 文件。

我们还需要在每个单独的**包中注册 **tsc** 脚本**

结果应该是:

[![](img/1f02e170757637ae2b70c3297cd57a3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_hChl3v_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZ97AdHoeuw72NHdxOYwR9g.png)

我们在每个中添加了简单的逻辑。ts 文件。

我们可以通过运行:
来测试我们的设置

```
lerna run tsc 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将在所有创建的包中运行 tsc 脚本:

[![](img/7676eb92a8593c56a24351f9c4bafacb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2idnrr5A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/897/1%2A66nX59a4rOFHJ70hvra10A.png)

如果一切顺利，我们将把 **src** 文件夹中的 **TypeScript** 文件编译到每个包中的 **lib** 文件夹中。

如果我们查看项目中任何包的 package.json，我们会看到类似于**目录**、**文件、类型、publishConfig** 和 **main:** 的属性

<figure>[![](img/3988ab9463bc7aa33c0de8ddefbd9d2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hgqkuScO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/501/1%2AWfg3WmU_7QPtBg9wuCH0Fg.png) 

<figcaption>套餐/医生/package.json</figcaption>

</figure>

这些对我们非常重要，因为它们控制着什么将被推送到 **NPM** 以及什么将成为我们库的入口点(**主**和**类型**属性)。

#### 去吧

我们将为这个项目创建一个 Github 存储库，并将所有代码推送到那里。

#### 出版

我们的目标是在同一个 **NPM** 范围(组织)下发布所有的包。 **NPM** 组织也可以是私有的；在这种情况下，您可以控制谁将看到/使用您的包。

我们在 npmjs.org 创建了一个公共(免费)组织:

[![](img/8118765031a0a28504424a72be13b262.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XnOTtfFl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWqqLdTpsYPiieGScJ9WAUQ.png)

我们必须从终端登录到该组织:

[![](img/e7f6bdeeb27e11e444e3e933c96659e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dGIMbsZk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/764/1%2AGLHj5SVAs6OBCbHbV44hFQ.png)

此时，我们已经准备好了组织和构建脚本。让我们从根包中将它们粘在一个 npm 脚本下

```
{
 "scripts": {
   "publish": "lerna run tsc && lerna publish"
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

从终端我们将运行:

```
npm run publish 
```

Enter fullscreen mode Exit fullscreen mode

Lerna 将指导我们完成发布过程，我们需要选择一个包版本并将标签推送到 Github。

如果一切顺利，我们将在最后看到消息: *lerna 成功发布了 3 个包。*

#### 验证安装

我们将在我们的项目目录中创建文件夹集成，并在其中安装所有的包:

```
npm init --yes
npm install ts-node --save-dev
npm install @hospital-sdk/doctor --save
npm install @hospital-sdk/patient --save
npm install @hospital-sdk/scheduler --save 
```

Enter fullscreen mode Exit fullscreen mode

在 **integration/src/index.ts** 中我们可以导入我们所有的包和控制台

```
import { Doctor } from "@hospital-sdk/doctor";
import { Patient } from "@hospital-sdk/patient";
import { Scheduler } from "@hospital-sdk/scheduler";

console.log(new Doctor());
console.log(new Scheduler());
console.log(new Patient()); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以在**integration/package . JSON**:
中添加启动脚本

```
{
  "scripts": {
   "start": "ts-node src/index.ts" 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

并从终端运行它:

[![](img/17870257f342acf40989bdc492e0aafb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--la9-2wuT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/732/1%2AfWb4hMqrzBmtVqW2XfVxBQ.png)

答对了！我们成功地从包中调用了导出的类。

#### Lerna 命令

有几个 Lerna 命令值得一提:

`lerna add`将 npm 依赖项添加到项目中的所有或特定包

`lerna bootstrap`安装项目中所有包的所有依赖项

#### 链接

源代码发布在:

[vlotesanovic/医院-sdk](https://github.com/vladotesanovic/hospital-sdk)

[https://www.npmjs.com/package/@hospital-sdk/doctor](https://www.npmjs.com/package/@hospital-sdk/doctor)

[粘土/粘土](https://github.com/lerna/lerna)

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[为一个类型脚本项目](https://blog.logrocket.com/setting-up-a-monorepo-with-lerna-for-a-typescript-project-b6a81fe8e4f8/)与 Lerna 建立 monorepo 最先出现在 [LogRocket 博客](https://blog.logrocket.com)上。