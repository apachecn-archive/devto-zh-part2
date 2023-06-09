# 来自过去的爆炸:节点。射流研究…

> 原文：<https://dev.to/captainsafia/a-blast-from-the-past-nodejs-3dm6>

在我的[上一篇博文](https://dev.to/captainsafia/looking-at-the-first-commit-of-redux-21m8)中，我回到了过去，查看了与 [Redux](https://github.com/reactjs/redux) 代码库的第一次提交相关的代码。对我来说，这是一种不同风格的代码阅读，但结果证明这很有趣。我想这次我会做同样的事情。为[节点](https://github.com/nodejs/node)码库。

侧栏:当我发布上一篇博文时，我收到了一些评论，大意是“为什么有人会在第一次提交时推送完整的代码？”大多数人很可能习惯于使用 Git 将自己的项目推送到 GitHub，而不一定是为了让其他人能够立即与之协作。我认为这取决于目标和意图，但我通常会在最初提交时提交代码和文档，以便个人可以立即开始使用并为项目做出贡献。

对节点代码库的首次公开提交是在 2009 年 2 月 16 日。

```
commit 9d7895c567e8f38abfff35da1b6d6d6a0a06f9aa (HEAD)
Author: Ryan <ry@tinyclouds.org>
Date: Mon Feb 16 01:02:00 2009 +0100

    add dependencies 
```

正如提交消息所指出的，最初的提交增加了项目的依赖性。这些[依赖项](https://github.com/nodejs/node/tree/9d7895c567e8f38abfff35da1b6d6d6a0a06f9aa/deps)被添加为 [Git 子模块](https://git-scm.com/book/en/v2/Git-Tools-Submodules)。事实证明，Git 存储库作为 Git 子模块的引用已经不存在了，所以探索这个提交的代码库就没什么意思了。

紧随第一次提交之后的提交要有趣得多。

```
commit 61890720c8a22a7f1577327b32a180a2d267d765 (HEAD)
Author: Ryan <ry@tinyclouds.org>
Date: Mon Feb 16 01:34:45 2009 +0100

    add readme and initial code 
```

好吧！那看起来肯定更有趣。这个初始提交中有两个 C 源文件: [server.cc](https://github.com/nodejs/node/blob/61890720c8a22a7f1577327b32a180a2d267d765/server.cc) 和[js _ http _ request _ processor . cc](https://github.com/nodejs/node/blob/61890720c8a22a7f1577327b32a180a2d267d765/js_http_request_processor.cc)。

第二个文件负责使用 V8 JavaScript 引擎来解析和解释 JavaScript 源文件。第一个文件负责运行一个用 C++写的小型 HTTP 服务器。

我尝试实际运行这个目录中提供的源文件。一个大的障碍是引用的子模块被移动到了不同的位置。也就是说，ebb 依赖项[已经被转移到另一个 GitHub 组织[([https://github.com/taf2/libebb](https://github.com/taf2/libebb))并且我很难找到另一个`liboi`依赖项在哪里(尽管[这个](https://cs.fit.edu/code/projects/cse2410_fall2014_bounce/repository/revisions/90fc8d36220c0d66c352ee5f72080b8592d310d5/show/deps/liboi)似乎是我能找到的最近的东西)。

我试着看看我是否能找到最早的不利用这些依赖的提交，但是它们会在代码库中停留一段时间。浏览项目的早期提交并不是一项无足轻重的工作。我有机会看到早期的代码库是如何发展的。

```
$ git log --pretty=oneline --abbrev-commit
90ea571602 (HEAD) request.respond(null) sends eof
096384ad58 gitignore
cc1a61c1e7 request.respond works
74f4eb9a2e add http method access
b518ed9db2 add some printfs..
7b7ceea4ec first compile
4a5bab8ef6 intermediate commit. nothing works.
6ded7fec5f ...
61890720c8 add readme and initial code
9d7895c567 add dependencies 
```

您可以看到随着代码库的成熟而发生的重构和清理。我还喜欢这样一个事实，即一些提交与无法编译的代码相关联。作为一个总是努力做出“完美提交”的人，我很高兴看到 JavaScript 行业中一位相对知名的开发人员的这种真实性。

不管怎样，我又尝试了一次让`61890720c8`提交编译，并设法在不使用子模块的情况下转移依赖项。事实证明，即使包括了依赖项，要让`make`正确运行仍然有很多麻烦。每个依赖项都有自己的一组依赖项，很难跟踪，等等。我想我应该把我的技能限制在阅读大约十年前的代码，而不是编译它(当然事情可能会更糟)。

进一步挖掘发现，`liboi`依赖现在变成了`evcom`依赖(从那以后发生了巨大的变化)。

所以，总而言之:

*   Node.js 代码库的第一次提交是实验性的，包括重构和不完全工作的提交。
*   在过去的 10 多年里，项目的一些关键依赖项已经发生了很大的变化。

我知道这篇博文有点无处不在，这是不可否认的，因为没有太多可以挖掘的内容(尤其是那些丢失已久的依赖项)。也许我会在下一个我做考古的代码库上变得更幸运…