# 我如何在一个周末使用 Tailwind CSS 和 Amazon Web Services 启动一个新项目

> 原文：<https://dev.to/kylegalbraith/how-i-launched-a-new-project-in-a-weekend-using-tailwind-css-and-amazon-web-services-1nb0>

我必须承认，当谈到 CSS 框架时，我是一个长期的 Bootstrap 用户。这很大程度上是因为这是我第一次使用，我从来没有分支。另外，不可否认的是，我没有花很多时间做 CSS 工作。

但是随着我开始迎接新的挑战，在 100 天内启动 4 个不同的项目，我意识到我需要能够快速启动想法。这意味着创建高质量和有效的登录页面需要很容易。我使用 Bootstrap 的时候感觉它很笨重，需要我做很多定制的修改。就快速行动而言，这是行不通的。

当我开始为我的最新项目[做每周时事通讯](http://www.kylegalbraith.com/learn-by-doing/)整理登陆页面时，我想起了关于[顺风 CSS](https://tailwindcss.com/) (TW)的阅读。人们对 Tailwind 赞不绝口，因为它是为快速 UI 开发而设计的基于 CSS 框架的精益实用工具。这听起来正是我想要的。

我想通过登陆页面来测试一下我的学习。但是，因为我计划在未来的项目中使用它，所以我需要一种方法来测试它是否解决了我的问题。一致、干净、快速地创建新的登录页面。

要认为它是一个很好的工具，我得出结论，我必须能够在周末启动登陆页面。

### 信任流程

在我开始创建和设计我的登陆页面之前，我首先提供了我需要的所有 Amazon Web Services 资源。如果你还没有看我写的关于在 AWS ~30 秒内建立 CI/CD 管道的博客文章，请读一读，因为这正是我做的事情。

这创建了一个持续的部署管道，将我的登录页面部署到我的个人静态网站所在的 S3 存储桶。它还创建了一个新的 Git CodeCommit 存储库，每当我对 master 进行更改时，它都会在我的管道中触发构建。

不到一分钟就完成了我的存储库设置和 AWS 基础设施的配置。这样一来，我可以专注于重要的任务，开发我的登录页面。

要开始使用顺风，我只需要通过 npm 将它安装到我的项目中。

```
npm install tailwindcss --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，我将`gulp-postcss`添加到我的包中，这样我就可以配置 Tailwind 通过 gulpfile 运行。

```
npm install gulp-postcss --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

然后我配置了一个 gulp 任务，将我的完整 CSS 输出到一个分发文件夹。我添加了一个默认任务，它将监视对我的标记或 CSS 文件的任何更改。

```
gulp.task('css', function () {
    var postcss = require('gulp-postcss');
    var tailwindcss = require('tailwindcss');

    return gulp.src('src/styles/main.css')
      // ...
      .pipe(postcss([
        // ...
        tailwindcss('./tailwind.js'),
        require('autoprefixer'),
        // ...
      ]))
      // ...
      .pipe(gulp.dest(publishPath + 'styles/'));
});

gulp.task('default', ['css'], function() {
    gulp.watch('**/*.css', ['css']);
    gulp.watch('**/*.html',['publish']);
}); 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我花了大约 5 分钟来配置 Tailwind 并将其集成到我的 gulp 流程中。这使我能够快速编辑我的登录页面，保存，然后自动查看我的更改。

到目前为止一切顺利。我能够在大约 5 分钟内建立一个构建过程，并将 CSS 框架合并到我的开发过程中。但是这会让我的登陆页面开发更快吗？

是的，是的，会的。我不用写一个**单个** CSS 规则。

[![Whaaaaat](img/512b4711d4925612df336890e8468eda.png)T2】](https://i.giphy.com/media/qdDO6hTcyeDks/giphy.gif)

Tailwind 提供的所有东西足以让我快速创建我的登陆页面。我不需要造型。这正是我所需要的，因为:

1.  我没有很好的设计技巧。
2.  CSS 样式就像一个黑洞，你可以被吸进去~~小时~~年。

因为 TW 是一个实用优先的 CSS 框架，所以有成百上千的实用 CSS 类可以应用于标记。

```
<div class="w-full md:w-3/4 lg:w-1/2 xl:w-1/2 ml-auto mr-auto p-4 bg-indigo-darker rounded">
    ....
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这里的所有样式都是内置的实用程序类。我可以使用`w-full`将默认宽度设置为全幅。但是对于中、大或超大屏幕，我想要不同的宽度，比如大屏幕的 50%宽度只是在宽度样式前面加上`lg`，然后指定我想要的宽度`w-1/2`。

添加实用程序类的便利性使得开发登录页面变得轻而易举。我可以专注于我想给我的用户的内容和演示。

我对 TW 提供的现成样式非常满意，所以我不需要写自己的样式。当然，如果你想创建自己的风格，设置自己的调色板，或者改变边框宽度，你可以通过 PostCSS 定制[。](https://tailwindcss.com/docs/what-is-tailwind/)

### 胜负

我选择了 Tailwind，因为我想快速开发一个优秀的登录页面，并让它看起来很棒。顺风带来了十倍于此的收益。我能够在一个周末发布一个新的登录页面并启动一个项目。这是一个快速开发令人愉悦的用户界面的伟大框架。

也就是说，有些事情可以改进。

这需要一点时间来适应无情的短类语法，如代表 margin-bottom 的`mb`。对于那些每天生活在 CSS 的杂草中的人来说，这可能是好的。但是对我来说，在你进入心流之前去阅读和记忆什么是什么是有点不和谐的。

有些人会对分配给 HTML 元素的类的数量非常怀疑。我同意，如果不止一个人在代码上工作，它有可能扼杀可读性。然而，它是一个快速的 UI 开发框架，这意味着几乎所有东西都有实用程序类。为了易用性，我愿意牺牲一点点可读性。

如果你想快速开发一个新的登陆页面或作品集网站，试试 TailWind 吧。[亚当·瓦森](https://twitter.com/adamwathan)总是在推特上，并且愿意回答问题。如果你有任何问题，我也在[推特](https://twitter.com/kylegalbraith)上。

### 通过实际使用来学习 AWS

如果你喜欢这篇文章，并且渴望开始学习更多关于 Amazon Web Services 的知识，我已经创建了一个关于如何在 AWS 上托管、保护和交付静态网站的新课程！这是一个书籍和视频课程，从信息的海洋中切入，加速你对 AWS 的学习。给你一个框架，让你通过实际使用来学习复杂的东西。

前往登录页面，为自己获取一份副本！