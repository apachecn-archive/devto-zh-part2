# 正在调查 ls

> 原文：<https://dev.to/captainsafia/looking-into-ls-1ed5>

你好啊。看起来我正在探索命令行函数的实现。在我最近的几篇博文中，我深入研究了 sudo 的代码。今天，我想看看我的开发工具包中另一个常用的命令:`ls`。

在我的上一篇博文中，我了解到`cd`是一个内置命令，依赖于`chdir` Unix 函数来改变当前的工作目录。事实证明，`ls`不是 Bash shell 上的内置命令。我通过以下操作确认了这一点。

```
$ which ls
/bin/ls 
```

所以`ls`函数是作为实际的二进制实现的。这个二进制文件的源代码位于 GNU 生态系统中的`coreutils`页面。一些谷歌搜索揭示了`ls`函数的源代码位于[这里](http://git.savannah.gnu.org/cgit/coreutils.git/tree/src/ls.c)。

旁注:我知道“谷歌搜索”掩盖了很多事情。在很大程度上，我在 Google 上搜索类似“ls 源代码”的东西，或者在 Git 存储库中搜索类似“ls”的查询。如果你想要更多关于我的“Google 代码探索”实践的信息，请告诉我。

由于这是一个 C 文件，开始探索这个代码库的可靠地方是 read the `main`函数。`main`函数是 C 程序的入口点，所以当我们运行`ls`时，我们开始执行`main`函数中定义的代码。文件中的前几行负责基本的变量初始化。

```
int i;
struct pending *thispend;
int n_files;

initialize_main (&argc, &argv);
set_program_name (argv[0]);
setlocale (LC_ALL, "");
bindtextdomain (PACKAGE, LOCALEDIR);
textdomain (PACKAGE);

initialize_exit_failure (LS_FAILURE);
atexit (close_stdout);

assert (ARRAY_CARDINALITY (color_indicator) + 1
        == ARRAY_CARDINALITY (indicator_name));

exit_status = EXIT_SUCCESS;
print_dir_name = true;
pending_dirs = NULL;

current_time.tv_sec = TYPE_MINIMUM (time_t);
current_time.tv_nsec = -1;

i = decode_switches (argc, argv); 
```

上面代码中一个看起来更有趣的函数调用是`initialize_main`函数调用。事实证明，并不是(嗯，取决于你对有趣的定义)！我试图找到`initialize_main`的定义，但当[找到](http://git.savannah.gnu.org/cgit/coreutils.git/commit/src?id=1844eee69a9c94701606f9d274fce3dc84b15f86)时，我感到相当失望。

```
/* Redirection and wildcarding when done by the utility itself.
   Generally a noop, but used in particular for native VMS. */
#ifndef initialize_main
# define initialize_main(ac, av)
#endif 
```

我不知道什么是本机虚拟机(我不认为这与虚拟机中的虚拟机有任何关系)。我过会儿得调查这件事。

引起我注意的第二个函数是`decode_switches`函数。我对这个函数特别感兴趣，因为像`initialize_main`函数一样，它将主函数的参数(即您在命令行传递给它的参数)作为其参数。

我没有阅读`decode_switches`函数的代码，只是阅读了函数定义上面的注释来确定它做了什么。

```
/* Set all the option flags according to the switches specified.
   Return the index of the first non-option argument. */

static int
decode_switches (int argc, char **argv) 
```

所以看起来这个函数负责设置选项标志，这些选项标志是你希望`ls`命令拥有的排序顺序和显示格式。

在这一点上，值得一提的是，当我通读`ls`命令的代码时，我意识到它的大部分负责格式化和呈现命令的输出。整个源文件大约有 5，3000 行长，但是它的大部分都定义了处理类似“如何打印出 Y 列中 X 个单词的列表？”或者“我如何用正确的分隔符打印这个字符串？”

我承认，浏览所有这些 C 代码可能有点烦人。哦，我多么怀念那些我只需要阅读 JavaScript 源代码的日子啊！因为 C 给你的现成的东西很少，所以你最终读到的很多代码并不那么有趣。这在很大程度上是高级语言在其标准库中实现的东西。

这是我的方式来说，我厌倦了阅读所有这些 C 代码，我将在这里结束我的博客帖子。总之，当您在 Unix 机器上运行`ls`命令时，您正在运行`ls`命令的`coreutils`实现。这个实现是用 C 编写的，负责解析您提供的参数，并以您在命令行中看到的原始格式适当地呈现`ls`命令的结果。

万岁。