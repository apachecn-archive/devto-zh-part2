# git 配置中有什么？

> 原文：<https://dev.to/captainsafia/whats-in-a-git-config-3pni>

所以在过去的几天里，我一直在享受钻研 Git 代码库和 Git 内部的乐趣。我在 Twitter 上分享我的帖子，大部分情况下(谢天谢地！)人们喜欢它们。最近，有人在我的上一篇博客文章上评论说，我本可以只阅读文档来回答我的问题，而不是四处探索代码库和配置文件。我想这是一个很好的机会来强调我为什么写这些博客。

1.  我正努力成为一名更好的作家。
2.  我发现当我把知识和经验联系起来时，我能更好地记住它。
3.  我喜欢！

所以，是的，阅读文档或向维护者或专家寻求我正在探索的许多事情的答案是完全可能的。就我个人而言，我发现不得不更加努力地学习一些东西有助于我更好地记住信息。

所以是的，就是这样。现在说到这篇博文的要点，Git。

在我的上一篇博文(上面有链接)中，我研究了`.git`目录的内容，发现在`.git`目录中有一个`config`文件，其内容如下所示。

```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true 
```

根据我上面的评论，我将了解这些配置参数可能对阅读代码负责。希望在这个过程中，我会学到更多关于 Git 的知识。

上面的第一个配置密钥`respositoryformatversion`，出现在我在 Git init 上的[博客文章中查看的`init-db.c`文件中。](https://dev.to/captainsafia/getting-into-git-init-42nk) 

```
xsnprintf(repo_version_string, sizeof(repo_version_string),
243 "%d", GIT_REPO_VERSION);
git_config_set("core.repositoryformatversion", repo_version_string); 
```

这部分代码将默认值`repositoryformatversion`设置为“0 ”,如上面的配置所示。还有哪里引用的？在代码库的另一部分中，它被设置为值“1”

```
git_config_set("core.repositoryformatversion", "1"); 
```

通过阅读代码库，我发现`repositoryformatversion`的值是二进制的。每当引用它时，它要么与 0 比较，要么与 1 比较。注意:这种比较并不是您在 C 代码库中常见的“检查函数是否返回 0 以表示成功”的检查。为了弄清楚这个配置负责什么，我做的下一件事是查看 Git 上的提交历史。也许，我发现[这个美丽的承诺](https://github.com/git/git/commit/00a09d57eb8a041e6a6b0470c53533719c049bab)，我的意思是真正的美丽，它阐述了差异`repositoryformatversion`价值观的目的。事实证明，`repositoryformatversion`的不同版本值的存在是为了表明一个存储库是否支持对 Git 正常功能的扩展。提交消息提到了用户可能希望对我在上一篇博文中探讨的`refs`目录使用不同格式的情况。太棒了。

好吧！接下来是这个小小的`filemode`配置选项。我在代码库中找到的关于这个配置选项的最有趣的参考是`config.c` 中的[。](https://github.com/git/git/blob/b2e45c695d09f6a31ce09347ae0a5d2cdfe9dd4e/config.c#L1006-L1009) 

```
if (!strcmp(var, "core.filemode")) {
    trust_executable_bit = git_config_bool(var, value);
    return 0;
} 
```

有意思！所以如果`core.filemode`的值被设置为真，那么`trust_executable_bit`的值也被设置为真，反之亦然。但是到底什么是可执行位呢？我承认，在某个时候，我在一次计算机科学讲座上从一位教授那里了解到了这一点，但我很健忘，所以我必须再看一遍。我对它有一个模糊的回忆，但在最新的研究中有一个更清晰的定义。

可执行位是在 Linux 中存储的目录和文件中设置的权限位。因为您可以在 Linux 中更改文件的权限，所以您可以更改底层的可执行位。所以要点是:可执行位只是一种在文件上设置权限的方式。

在阅读了更多的代码，特别是 [`cache.h`](https://github.com/git/git/blob/c6284da4ff4afbde8211efe5d03f3604b1c6b9d6/cache.h#L265-L274) 中的一些代码之后，我了解到将`filemode`设置为“false”将会阻止 Git 检测到对权限(以及底层位)的更改，因为这是对实际文件的更改。我决定测试一下。我更改了 Git 目录中一个文件的权限。看看这个。

```
$ git status
On branch new-thing
nothing to commit, working tree clean
$ chmod +x test.txt 
$ git status
On branch new-thing
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified: test.txt

no changes added to commit (use "git add" and/or "git commit -a") 
```

现在，如果我将`filemode`设置为“假”会发生什么？

```
$ vim .git/config # This is where I changed the filemode value 
$ git status
On branch new-thing
nothing to commit, working tree clean 
```

太酷了。事实上，我对这一发现感到非常兴奋，因为这似乎是一件非常实用的事情。

好吧！下一个配置参数是`bare`参数。它目前被设置为“假”那么这有什么用呢？

通过研究代码和查看引用`is_bare_repository`的地方，我了解到一个空的存储库是没有`.git`目录或工作树的存储库。换句话说，这是一个非常简单的存储库！我假设如果您不像查看代码那样使用 Git 对代码进行版本控制，那么这就是您想要的东西。

旁注:在做这些探索的时候，我还了解到 Git 中的大多数测试用例(或者可能全部)实际上都是作为 shell 脚本编写的。这话有道理，但我觉得很有意思。

好吧！上面提到的下一个参数是`logallrefupdates`参数。我承认，通过研究代码，我费了很大劲才弄清楚这个参数的作用。所以我作弊，看了看[医生](https://www.kernel.org/pub/software/scm/git/docs/git-config.html)，有点不以为然。它基本上规定了您希望在 reflog 中记录更新的粒度，ref log 是一个记录分支上 HEAD commit 更改的本地日志。我相信在未来的探索中，一旦我对 reflog(发音为 ref-log not re-flog，呵)了解得更多，我会对此更感兴趣。

好的。所以，我已经很熟悉下一个参数的作用了。`ignorecase`决定 Git 是否会忽略文件名中的大小写变化。例如，当设置为 true 时，Git 不会将从“test.txt”到“Test.txt”的重命名视为对工作目录的更改。

最后一个参数是`precomposeunicode`参数。这个参数几乎只在[这个文件](https://github.com/git/git/blob/e629a7d28a405e48fae6b064a781a10e885159fc/compat/precompose_utf8.c)中使用。文件顶部的注释非常有用。

```
/*
 * Converts filenames from decomposed unicode into precomposed unicode.
 * Used on MacOS X.
 */ 
```

这两种 Unicode 有什么区别？我发现维基百科文章的这一部分有助于我理解其中的区别。基本上，预先组合的字符是包含存储在单个字节值中的重音或附加字符的字符。分解字符是包含重音符号或附加符号的字符，存储在两个字节中。

嗯！那很有趣！我学到了很多，特别是关于 Git 在边缘情况下的默认行为(文件名大小写的改变或者文件权限的改变)。总而言之，很充实！

让我们看看下一个吸引我眼球的是什么…