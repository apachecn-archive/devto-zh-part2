# 当分支不是主节点时，在 git push 上上传内容

> 原文：<https://dev.to/mte90/upload-stuff-on-git-push-when-branch-is-not-master-54e8>

优化的第一条规则是:当一件事需要很多步骤且可以自动化时，就写一个脚本。

至少在我心中这是我的第一条规则，当我做同样的事情很多次时，我会写一个剧本。

让我们用一个真实的带有代码的用例来看看 Git 钩子吧！

帖子[分支不是主](https://daniele.tech/2018/04/upload-stuff-on-push-when-branch-is-not-master/)上传东西在 git push 上最早出现在[Daniele mte 90 Scasciafratte](https://daniele.tech/eng)上。