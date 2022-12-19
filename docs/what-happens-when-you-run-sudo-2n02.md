# 运行 sudo 会发生什么？

> 原文：<https://dev.to/captainsafia/what-happens-when-you-run-sudo-2n02>

那么，当你`sudo`的时候到底发生了什么？

如果您一直在使用计算机，特别是类似 Unix 的系统，您可能使用过`sudo`命令。它代表**s**super**u**ser**d**o。它运行你作为管理员想要运行的任何命令。它通常用于授予您编辑系统文件(如`/etc/hosts`)或向系统目录添加目录等权限。

但是它是如何工作的呢？

如果你已经在这个博客呆了足够长的时间，你知道接下来会发生什么。你不是害怕就是兴奋。

是时候读点代码了！

您在命令行上运行的任何命令都可能作为 C 程序来实现。所以，为了弄清楚`sudo`是如何工作的，我们只需要阅读一些 C 代码。

*无法控制地颤抖*

命令的主页可以在找到[。看来`sudo`的最新稳定版本，截至本文撰写之时，是 2018 年 1 月 16 日发布的 1.8.22。这是令人惊讶的最近。无论如何，我在 GitHub](https://www.sudo.ws) 上找到了最新版本的`sudo`命令[。是时候开始了！](https://github.com/millert/sudo/tree/fc82a16655e566277678d2530e85f6bdf2d63b83)

这是我第一次阅读 Unix 命令的 C 代码，所以我不知道从哪里开始。理想情况下，我希望找到应用程序的入口点。我假设这将是某个名为`sudo.c`的文件中的`main`函数。我在这个文件中找到了这样一个切入点[。该函数的前几行似乎主要与变量设置和初始化有关。](https://github.com/millert/sudo/blob/fc82a16655e566277678d2530e85f6bdf2d63b83/src/sudo.c#L131) 

```
int nargc, ok, status = 0;
char **nargv,** env_add;
char **user_info,** command_info, **argv_out,** user_env_out;
struct sudo_settings *settings;
struct plugin_container *plugin, *next;
sigset_t mask;
debug_decl_vars(main, SUDO_DEBUG_MAIN)

/* Make sure fds 0-2 are open and do OS-specific initialization. */
fix_fds();
os_init(argc, argv, envp);

setlocale(LC_ALL, "");
bindtextdomain(PACKAGE_NAME, LOCALEDIR);
textdomain(PACKAGE_NAME);

(void) tzset(); 
```

我可以花很多时间来研究这些功能是什么，但是生命是短暂的，我今天不想去那个兔子洞。我又浏览了几行，直到遇到一个引起我兴趣的函数。

```
/* Make sure we are setuid root. */
  sudo_check_suid(argc > 0 ? argv[0] : "sudo"); 
```

耐人寻味！`setuid`是什么意思？这是一种设置将要运行的命令的用户 ID(或组 ID，但我们不在这里讨论它)的方法。也许你想在你的标准用户(captainsafia)下运行一个命令，或者也许你想在 sudo 下运行它。您将使用`setuid`来完成这个任务。所以`sudo_check_suid`函数调用`geteuid`函数，函数[获取当前运行进程](https://linux.die.net/man/2/geteuid)的用户 ID。它检查是否与`ROOT_UID`(根用户的用户 id)相等。如果不相等，它检查`sudo`二进制文件是否存储在用户的`PATH`变量中，这同样会将用户提升到 sudo。

如果在用户的路径中找到了`sudo`二进制文件，它会将`qualified`变量设置为`true`，并通过调用 [`stat`](http://man7.org/linux/man-pages/man2/stat.2.html) 和检查 stat struct 的属性来查看`sudo`命令是否运行正常。下面是`sudo_check_suid`函数的全部内容。

```
static void
sudo_check_suid(const char *sudo)
{
    char pathbuf[PATH_MAX];
    struct stat sb;
    bool qualified;
    debug_decl(sudo_check_suid, SUDO_DEBUG_PCOMM)

    if (geteuid() != ROOT_UID) {
    /* Search for sudo binary in PATH if not fully qualified. */
    qualified = strchr(sudo, '/') != NULL;
    if (!qualified) {
        char *path = getenv_unhooked("PATH");
        if (path != NULL) {
        const char *cp, *ep;
        const char *pathend = path + strlen(path);

        for (cp = sudo_strsplit(path, pathend, ":", &ep); cp != NULL;
            cp = sudo_strsplit(NULL, pathend, ":", &ep)) {

            int len = snprintf(pathbuf, sizeof(pathbuf), "%.*s/%s",
            (int)(ep - cp), cp, sudo);
            if (len <= 0 || (size_t)len >= sizeof(pathbuf))
            continue;
            if (access(pathbuf, X_OK) == 0) {
            sudo = pathbuf;
            qualified = true;
            break;
            }
        }
        }
    }

    if (qualified && stat(sudo, &sb) == 0) {
        /* Try to determine why sudo was not running as root. */
        if (sb.st_uid != ROOT_UID || !ISSET(sb.st_mode, S_ISUID)) {
        sudo_fatalx(
            U_("%s must be owned by uid %d and have the setuid bit set"),
            sudo, ROOT_UID);
        } else {
        sudo_fatalx(U_("effective uid is not %d, is %s on a file system "
            "with the 'nosuid' option set or an NFS file system without"
            " root privileges?"), ROOT_UID, sudo);
        }
    } else {
        sudo_fatalx(
        U_("effective uid is not %d, is sudo installed setuid root?"),
        ROOT_UID);
    }
    }
    debug_return;
} 
```

是啊！代码太多了。但是不要害怕。c 代码通常过于冗长，因为您必须手动完成许多事情，如字符串连接和拆分以及错误检查(errr，比其他语言更需要手动完成)。高级语言会帮你处理很多这类事情。谢谢 Python 和 JavaScript(还有其他)！

此时，我正在滚动查看`sudo.c`中的`main`函数的剩余代码，天哪，还真多！大部分是设置配置，并在设置不正确时警告用户。

有一行代码引起了我的注意。

```
if (!sudo_load_plugins(&policy_plugin, &io_plugins))
    sudo_fatalx(U_("fatal error, unable to load plugins")); 
```

`sudo_load_plugins`？我们正在加载什么插件？这是怎么回事？该调查了！

原来，`sudo_load_plugins`是在[的另一个文件](https://github.com/millert/sudo/blob/fc82a16655e566277678d2530e85f6bdf2d63b83/src/load_plugins.c)中定义的。这个函数真的很长，所以我决定只阅读函数定义，看看它是否能提供一些关于函数可能做什么的信息。通常，在 C 中，你将指向对象的指针传递给一个函数。然后，该函数修改这些指针所指向的对象，并返回一些状态代码。所以通过查看函数的定义，我可以很好的理解这个函数在做什么。在这种情况下，`sudo_load_plugins`的函数定义如下。

```
static bool
sudo_load_plugin(struct plugin_container *policy_plugin,
    struct plugin_container_list *io_plugins, struct plugin_info *info) 
```

所以在这个函数中被修改的三个结构是`plugin_container`、`plugin_container_list`和`plugin_info`。我对代码做了更多的研究。我不会在这里叙述我所做的一切，因为这需要一段时间，但我会在这里总结一下。

`sudo_load_plugins`函数负责加载策略插件。策略插件用于定义特定用户使用`sudo`命令的权限。如果您正在管理一个 Unix 系统，您可以配置 sudo，使其永远不能为任何用户执行`sudo`下的某个命令，或者为会话添加日志记录。那有用！

好吧！回到看`main`函数。下一段有趣的代码是[一个巨大的开关语句](https://github.com/millert/sudo/blob/fc82a16655e566277678d2530e85f6bdf2d63b83/src/sudo.c#L216)。它根据一组常量检查按位变量和`sudo_mode`和`MODE_MASK`的值。

```
switch (sudo_mode & MODE_MASK) { 
```

我不会一行一行地深入代码的细节，但这里是总体要点。事实证明，`sudo`命令可以在各种不同的模式下运行。您可以在一种模式下运行它，这种模式允许您保留命令使用的`SHELL`变量(`MODE_SHELL`)。

`main`函数的最后一部分有一个额外的有用注释来解释正在发生的事情。

```
/*
* If the command was terminated by a signal, sudo needs to terminated
* the same way. Otherwise, the shell may ignore a keyboard-generated
* signal. However, we want to avoid having sudo dump core itself.
*/
if (WIFSIGNALED(status)) {
    struct sigaction sa; 
```

所以基本上，如果我们正在运行的命令 sudo 意外退出，那么 sudo 也应该如此。

唷！在这个功能中发生了很多事情。而且说实话，一般来说在`sudo`命令中更是如此。我了解到`sudo`不仅仅是`sudo !!`。我不是 Unix 系统管理员或其他什么人，所以我不知道可以使用`sudo`来监控和限制跨 Unix 系统的特权命令访问的所有方法。你知道的越多！

感谢 sudo 的维护者 [Todd Miller](https://github.com/millert) 维护了这么有价值的工具！