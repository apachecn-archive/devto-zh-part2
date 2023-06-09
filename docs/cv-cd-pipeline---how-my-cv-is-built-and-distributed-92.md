# CV CD 管道-我的 CV 是如何构建和分发的

> 原文：<https://dev.to/s1hofmann/cv-cd-pipeline---how-my-cv-is-built-and-distributed-92>

**前言:**

这是一个永恒故事的另一部分，在这个故事中，某个地方的某个人认为，手头的任务最好是自动化的。

[![Automate all the things!](img/0659b44a2218b0fad4ec52a71f7f35dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4uFpNKSD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/automation.png)

**剧透:**

目前我正在努力，但请你继续阅读！

## 创建、更新、忘记、重复

回到我申请第一份工作的时候，大约是 2002 年(时间过得很快……)，我必须写我的第一份简历。而且一得到这份工作，我几乎立刻又把简历给忘了。

然后下一次我需要一份最新的简历时，我之前的努力早就烟消云散了，所以是时候从头再来了…

而下一次我需要一份最新的简历时，我之前的努力早已烟消云散，所以是时候从头再来了…再来…

下一次我需要一份最新的简历时…嗯，你知道这个故事…

快进到今天，我刚刚更新了我的简历，这是我经常做的事情，因为我知道要花一些时间来记住你在过去几年里做了什么。但我也花了一些时间在我的总体简历设置上，让它更“令人愉快”。

## 创建、更新、提交、重复

自从我不得不写第一份简历以来，很多事情都发生了变化，大部分时间我都在用 Word 文档和 Dropbox / Ubuntu One 等工具记录自己在做什么。大约在我即将完成计算机科学硕士论文的时候，我开始考虑写一份新的简历。由于我喜欢跟踪事物，它应该是受版本控制的，所以我在我的服务器上创建了一个新的存储库，并打开了一个 LaTeX 模板。

大约在同一时间，我开始彻底检查我的网页，产生了我的[外壳式的](https://simon-hofmann.org)登陆页面和一个更加[图形化的版本](https://simon-hofmann.org/me)。

## 将枯燥的东西自动化

两个页面都有我的简历供下载，但在我的初始设置中，我不得不手动*创建两个 PDF 文档，以便稍后手动*将它们复制到正确的位置。**

 **手动做事相当麻烦，所以我开始考虑自动化这个过程…

自动化这项工作的工作流程非常简单:

1.  签出提交的更改
2.  生成 PDF 文件
3.  将它们移动到正确的位置

所有的魔法都是通过一个简单的 **post-update** git 钩子完成的，而这个钩子只是一个简单的 shell 脚本。

```
git clone $REPO $TMP
cd $TMP
for i in *.tex ; do docker run -v $(pwd):/pwd s1hofmann/texlive:latest "$i"
    if [$? -eq 0]; then mv "${i%.tex}.pdf" $DEST
    fi
done rm -rf $TMP
exit 
```

Enter fullscreen mode Exit fullscreen mode

我决定构建一个 TexLive 容器，因为整个包相当大(图像大约有 2.9GB)，我不想仅仅为了构建我的 CV 就把它安装在我的服务器上。

```
FROM debian:stable-slim
LABEL maintainer="s1hofmann"
LABEL version="0.1"

RUN apt-get update && apt-get install texlive-full -y
RUN mkdir /pwd
WORKDIR /pwd
ENTRYPOINT ["/usr/bin/latexmk", "-pdf"] 
```

Enter fullscreen mode Exit fullscreen mode

这只是一个简单的包装器，它挂载一个本地目录并在传递的文件上运行`latexmk`。

现在，如果 LaTeX 构建成功，那么生成的 PDF 文件将被移动到它们的公共文件夹中，我的可怜的 man 版本的 CD 管道就完成了。

太好了，在我的私人服务器上实现所有服务容器化的又一步！

也许下次我会把哲基尔从我的清单上划掉，我们会看到…

再见

西蒙**