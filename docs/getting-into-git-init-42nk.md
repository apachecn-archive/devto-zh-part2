# 进入 git 初始化

> 原文：<https://dev.to/captainsafia/getting-into-git-init-42nk>

所以，作为一只容易分心的松鼠，我对另一件事感到好奇。

饭桶。

我已经使用 Git 大约五年了，所以我对这个工具有一点了解。但是我从来没有真正钻研过 Git 的代码来弄清楚它是如何实现的。

如果你以写软件为生(或者可能是为了好玩)，你可能也已经使用 Git 很长时间了。使用 Git 有很多东西要解包，但是和所有事情一样，我将从头开始，非常开始。

```
$ git init 
```

运行`Git init`时到底会发生什么？我像往常一样开始阅读一些文件。同样，在你写代码之前写文档也是有帮助的，在你读代码之前读文档也是有帮助的。那么[对于`Git init`](https://Git-scm.com/docs/Git-init) 的文档页面是怎么说的呢？

> git-init——创建一个空的 Git 存储库或重新初始化一个现有的存储库

好吧！那是相当透明的。当然，有几个标志和选项可以传递给`git init`命令来定制它的功能，但是我将忽略它们。我现在很想钻研一些代码，所以让我们开始吧。

我以前从来没有看过 Git 的代码库。我不知道文件夹结构。我什么都不知道。嗯，那不完全正确，但是所有好的作品都有夸张！但是我已经准备好投入了！谢天谢地，GitHub 上有一个`Git`源的镜像。你可以在这里找到。

我做的第一件事是滚动浏览源代码库顶层的一些目录和文件。我惊讶地发现，许多与顶级 Git 命令相关的 C 头文件和源文件，如`git diff`和`git fetch`，实际上都存储在顶级目录中。我希望它们被组织到子目录中。啊，生活充满了惊喜。

我查看了这些顶层文件，看看是否有可能与`Git init`命令相关联的源文件，但是没有。喘息！我做的下一件事是在源文件目录中搜索名称中包含`init`的源文件。我偶然发现[这个](https://Github.com/Git/Git/blob/7e31236f652ad9db221511eaf157ce0ef55585d6/builtin/init-db.c)有希望回归。我通读了代码中的一些注释，确认这确实是负责`git init`功能的源文件。

通常，我会在这个文件中查找`main`函数的定义，以弄清楚发生了什么。我很快发现我找到的文件是一个库文件，它存储了与初始化 Git 存储库相关的函数，但不是实际的入口点代码。不管怎样，在浏览了一些函数并根据它们的名字和接受的参数猜测它们可能负责什么之后，我意识到负责大量繁重工作的函数是 [`init_db`](https://Github.com/Git/Git/blob/7e31236f652ad9db221511eaf157ce0ef55585d6/builtin/init-db.c#L337) 函数。

```
int init_db(const char *git_dir, const char *real_git_dir,
        const char *template_dir, unsigned int flags) 
```

所以，我会试着把大部分的代码阅读集中在，如何在整个函数中使用参数上。我将从弄清楚`git_dir`参数可能用于什么开始。在阅读了一些代码后，我发现`git_dir`命令对应于(显然，事后看来)位于 Git 版本控制项目的源目录中的`.git`子目录的路径。在这个函数的整个代码中，`git_dir`用于确定一个`.git`目录是否已经存在。我还注意到`git_dir`参数和`real_git_dir`参数有着密切的关系。看看下面的代码片段。

```
if (real_git_dir) {
    struct stat st;

    if (!exist_ok && !stat(git_dir, &st))
        die(_("%s already exists"), git_dir);

    if (!exist_ok && !stat(real_git_dir, &st))
        die(_("%s already exists"), real_git_dir);

    set_git_dir(real_path(real_git_dir));
    git_dir = get_git_dir();
    separate_git_dir(git_dir, original_git_dir);
}
else {
    set_git_dir(real_path(git_dir));
    git_dir = get_git_dir();
} 
```

`git_dir`和`real_git_dir`有什么区别？为什么这段代码如此保守？我试图在 Google 上搜索这两个参数名，看看以前是否有人做过类似的查询，但结果是，在这方面，索引的互联网上没有任何对我有用的内容。

当我心不在焉地盯着我的电脑屏幕时，背景播放无声的提示声

所以我需要多读一点代码，来弄清楚两者的区别。看了代码，我终于搞清楚了`real_git_dir`是负责什么的。下面的代码行有所帮助。

```
OPT_STRING(0, "separate-git-dir", &real_git_dir, N_("gitdir"),
           N_("separate git dir from working tree")), 
```

原来`real_git_dir`的值就是设置给自变量`separate-git-dir`的值。根据文档，此参数用于执行以下操作。

> —separate-git-dir=
> 
> 不要将存储库初始化为`$GIT_DIR`或`./.git/`的目录，而是在那里创建一个包含实际存储库路径的文本文件。这个文件充当与文件系统无关的到存储库的 Git 符号链接。
> 
> 如果这是重新初始化，存储库将被移动到指定的路径。

有意思！现在我看到了两者之间的互动。`git_dir`参数是`.git`目录的路径，而`real_git_dir`参数是一个文本文件的路径，用于创建一个跨平台的类似符号链接的东西到`.git`目录。很高兴我发现了这一点！现在我晚上可以安心睡觉了。

所以上面代码片段中的代码负责计算出`.git`目录的位置，不管它是存储在一个类似符号链接的东西中还是一个实际的目录中。

好吧！基于函数调用，接下来的几行代码非常容易理解。

```
startup_info->have_repository = 1;

safe_create_dir(git_dir, 0);

init_is_bare_repository = is_bare_repository();

/* Check to see if the repository version is right.
* Note that a newly created repository does not have
* config file, so this will not fail. What we are catching
* is an attempt to reinitialize new repository with an old tool.
*/
check_repository_format();

reinit = create_default_files(template_dir, original_git_dir);

create_object_directory(); 
```

实际上，上面的代码片段创建了`.git`目录(如果它还没有创建的话),并检查这是否是一个空的存储库。我假设`create_default_files`函数主要负责在`.git`目录中创建目录和文件。

下一段代码根据从`get_shared_repository()`函数返回的值做一些事情。什么是共享存储库？

```
if (get_shared_repository()) {
    char buf[10];
    /* We do not spell "group" and such, so that
         * the configuration can be read by older version
         * of Git. Note, we use octal numbers for new share modes,
         * and compatibility values for PERM_GROUP and
         * PERM_EVERYBODY.
         */
    if (get_shared_repository() < 0)
        /* force to the mode value */
        xsnprintf(buf, sizeof(buf), "0%o", -get_shared_repository());
    else if (get_shared_repository() == PERM_GROUP)
        xsnprintf(buf, sizeof(buf), "%d", OLD_PERM_GROUP);
    else if (get_shared_repository() == PERM_EVERYBODY)
        xsnprintf(buf, sizeof(buf), "%d", OLD_PERM_EVERYBODY);
    else
        die("BUG: invalid value for shared_repository");
    git_config_set("core.sharedrepository", buf);
    git_config_set("receive.denyNonFastforwards", "true");
} 
```

我想我要做的第一件事是浏览 Git 的文档，看看是否有这方面的参考。我最终看到了这个 StackOverflow 帖子，它解释了共享存储库是在系统的一个组中的用户之间共享的存储库。所以基本上，上面的代码所做的是检查用户是否请求在机器上的用户之间共享 Git 存储库，并设置正确的 Git 配置。

旁注:还记得我在阅读节点代码库时，我说过你应该在阅读代码之前阅读文档。还是我五段前说的那句话？我真的应该接受我自己的建议…

最后，程序打印出一些有用的信息，如 Git 存储库是否被初始化以及在哪里被初始化。

```
if (!(flags & INIT_DB_QUIET)) {
    int len = strlen(git_dir);

    if (reinit)
        printf(get_shared_repository()
               ? _("Reinitialized existing shared Git repository in %s%s\n")
               : _("Reinitialized existing Git repository in %s%s\n"),
               git_dir, len && git_dir[len-1] != '/' ? "/" : "");
    else
        printf(get_shared_repository()
               ? _("Initialized empty shared Git repository in %s%s\n")
               : _("Initialized empty Git repository in %s%s\n"),
               git_dir, len && git_dir[len-1] != '/' ? "/" : "");
}

free(original_git_dir);
return 0; 
```

最后一行引起了我的注意。`free(original_git_dir)`。这是怎么回事？我已经知道`free`是负责释放内存的 C 函数。结果并不那么有趣。这一行只是释放一些分配给先前配置的字符串的空间。在我之前的代码阅读中，我忽略了它被分配的那一行，所以这个自由对我来说是一个惊喜。忽略内存分配是许多内存泄漏的根源！

旁注:是的，你被允许在双关语/文字游戏中局促不安。

原来如此！显然不是全部。但我认为这是一个非常有趣的开始。我学到了一些东西。

*   Git 总是用大写字母“G”拼写，即使它在句子中间。
*   您可以创建类似符号链接的对象来引用`.git`目录。
*   您可以在组之间共享存储库。

我也有几个问题想要回答，但我会把它们留给其他帖子。

塔塔现在，互联网无效！直到下一次…