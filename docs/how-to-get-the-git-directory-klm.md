# 如何获取 Git 目录

> 原文：<https://dev.to/burdettelamar/how-to-get-the-git-directory-klm>

下面的代码片段展示了如何获取 git 项目的 git 目录:

```
def git_clone_dir_path
  git_dir = `git rev-parse --show-toplevel`.chomp
  $?.success? ? git_dir : nil
end

p git_clone_dir_path 
```

[根据以下回复进行简化。谢谢你，贝斯！]

这适用于项目中的任何目录。

我将它用于一个涉及项目特定路径的测试。在我的机器上，一个测试可能会生成一个实际值路径，比如`C:/Users/Burdette/Documents/GitHub/markdown_helper`*。*

 *如果我把路径作为期望值，它

[![](img/4875c5f11e98185230a3ef7192f9fc24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HExPQYpB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.codinghorror.com/conteimg/uploads/2007/03/6a0120a85dcdae970b0128776ff992970c-pi.png)

在您机器的克隆体上，路径会有所不同。

所以在我的期望值里，我把`git_dir/` *放成什么*。然后，测试可以在本地 git 目录中进行替换。

这使得测试可以在任何机器上进行。*