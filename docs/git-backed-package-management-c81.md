# Git 支持的包管理

> 原文：<https://dev.to/oleksiyrudenko/git-backed-package-management-c81>

作为一名开发人员，我想尽可能地保持我的代码库[干燥](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)以及 DRO <sup>1</sup> 。为了实现这个目标，我希望能够

*   导入第三方包
*   从源更新导入的包

有包和依赖项管理器来帮忙。JavaScript 宇宙中的 [**npm**](https://www.npmjs.com/) 和 [**纱**](https://yarnpkg.com/en/) ，以及经常归于 PHP 世界的**等等。**

 **但是，如果您的技术堆栈生态系统不提供任何(*顺便说一句，您知道您可以在几乎任何生态系统中有效地使用上述任何一种吗？或者你能(或可能)使用的工具只有 Ctrl-C/Ctrl-V <sup>2</sup> 和/或 git？*

复制粘贴是最新的手段。还是坚持 git 吧。

许多人使用以下基于 git 的工具:

*   [git-子模块](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
*   [git 子树合并策略](https://mirrors.edge.kernel.org/pub/software/scm/git/docs/howto/using-merge-subtree.html)
*   [git-subtree](https://github.com/git/git/blob/master/contrib/subtree/git-subtree.txt) (从 1.7.11 版开始与 git 捆绑在一起)
*   有史以来最酷的合并！

好了，以上作品。但是最近我发现了一个工具，它不仅消除了麻烦，还允许将对导入包的更改推回到源代码中(不过考虑一下拉请求机制)。

今天的英雄来了！

[git-subrepo](https://github.com/ingydotnet/git-subrepo)

基本工作流程简单如

```
# init
git subrepo clone anotherRepo.git localSubdir4anotherRepo -b master
# update from anotherRepo
git subrepo pull localSubdir4anotherRepo
# export back
git subrepo push localSubdir4anotherRepo -b anotherRepo-target-branch 
```

我就是喜欢。

有关该主题的更多详细信息和进一步阅读，请参见[回购导入和导出回复](https://gist.github.com/OleksiyRudenko/86d378b56fe690e47a066b8eeb4ac5b2)要点。

* * *

<sup>(1)</sup> -不重复别人；有争议，但我认为完全符合这里的背景。

<sup>(2)</sup> -或同等支持内容复制粘贴。**