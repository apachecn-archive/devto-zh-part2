# 如何使用模板构建可重用的 web 组件

> 原文：<https://dev.to/sait/how-to-build-reusable-web-components-using-stenciljs-5ahj>

Stenciljs 是一个编译器，它帮助我们构建 Web 组件，并在你的 JavaScript 项目(Angular，React，Vue)
的任何地方使用
，而不需要一次又一次地复制一个东西。您可以在普通的 JavaScript 中使用
。

Stenciljs 使用的语法是 jsx 和 typescript 的组合，我们称之为 tsx。

让我们使用 Stenciljs 构建一个 web 组件

打开你的终端并从 GitHub 克隆它

```
git clone https://github.com/ionic-team/stencil-component-starter   my-header
cd my-header
git remote rm origin 
npm install 
```

Enter fullscreen mode Exit fullscreen mode

运行 npm 后，安装所有已安装的依赖项

```
now run npm start    to power up the server 
```

Enter fullscreen mode Exit fullscreen mode

在您喜欢的代码编辑器中打开项目
[![image](img/675f3ac534426623c61d4fb032888279.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rexY3m6j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AgZwWEh8pJbwXlaaFgSZZCQ.png)

文件夹-结构-模板

*   打开你的 src 文件夹，它显示组件文件夹

*   删除组件文件夹中的所有内容

*   让我们从头开始构建一个新的组件

在 components 文件夹中添加名为 my-header 的新文件夹

在我的邮件头文件夹中

创建两个文件如下图所示
[![](img/1584e8ed80a652dc3233f9aabcf1dcbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AVFiSfIk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2A7HwYaSunbmRxBSjDNwUHSw.png)

现在打开 my-header.tsx 文件，添加以下代码