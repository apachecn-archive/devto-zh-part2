# 弄清楚“curl”是如何存储配置的

> 原文：<https://dev.to/captainsafia/figuring-out-how-curl-stores-configurations-dd8>

在我的上一篇博文中，我开始钻研`curl`命令行工具的代码库。

侧边栏:我不知道我是否在我的博客文章中一直坚持正确拼写“命令行”。原来正确的拼写方式是两个独立的单词。我认为我做错了，在几篇博文中把它拼成了一个单词。不管怎样，只是一个有趣的事实。

在上一篇博文的结尾，我起草了一个问题列表，这些问题是我在分析命令行工具的核心代码时想要回答的。

1.  在`operate`函数中有哪些对`libcurl`的调用？
2.  对传递给 operate 函数的参数做了什么预处理(如果有的话)？

所以我转向`src/tool_operate.c` ( [链接](https://github.com/curl/curl/blob/36f0f47887563b2e016554dc0b8747cef39f746f/src/tool_operate.c))并开始钻研代码。

另一个边栏:当我引用一个文件名时，我通常会直接链接到 GitHub 上的源文件。我刚刚意识到，当我使用等宽字体设计文本时，我的博客主题并没有让这一点变得明显。从现在开始，我将引用带有“(链接)”括号的文件。

在`operate` ( [链接](https://github.com/curl/curl/blob/36f0f47887563b2e016554dc0b8747cef39f746f/src/tool_operate.c#L1926%5D)函数中，前几行负责解析传递给`curl`命令的参数，对于那些已经关注这些博客文章一段时间的人来说，这并不奇怪。

```
 /* Parse .curlrc if necessary */
  if((argc == 1) ||
     (!curl_strequal(argv[1], "-q") &&
      !curl_strequal(argv[1], "--disable"))) {
    parseconfig(NULL, config); /* ignore possible failure */

...
      else if(res == PARAM_ENGINES_REQUESTED)
        tool_list_engines(config->easy);
      else if(res == PARAM_LIBCURL_UNSUPPORTED_PROTOCOL)
        result = CURLE_UNSUPPORTED_PROTOCOL;
      else result = CURLE_FAILED_INIT;
    }
    else {
#ifndef CURL_DISABLE_LIBCURL_OPTION
      if(config->libcurl) {
        /* Initialise the libcurl source output */
        result = easysrc_init();
      }
#endif 
```

从上一篇博文中，我了解到这里引用的`config`参数是存储在`.curlrc`中的全局配置文件，从上面代码片段中的一些代码注释可以看出。

一旦参数被解析，函数的主要操作就开始了。首先，该函数从全局配置对象中提取一些`OperationConfig`对象。

```
size_t count = 0;
struct OperationConfig *operation = config->first; 
```

我浏览了一下`curl`文档，以进一步了解这个`OperationConfig`对象是什么。我找到了`OperationConfig`结构的定义([文件](https://github.com/curl/curl/blob/ba48863e52ab042a6cf754e254dfb5062a45b090/src/tool_cfgable.h))。本质上，它包含在发出转移请求时使用的特定属性。这包括在 HTTP 请求中使用的用户代理或在 FTP 请求中使用的端口。这对我来说很有意义，但是让我感兴趣的事情之一是`GlobalConfig`对象的定义(在上面的代码片段中被称为`config`)。下面是我感兴趣的部分的摘录。

```
struct GlobalConfig {
  ...
  struct OperationConfig *first;
  struct OperationConfig *current;
  struct OperationConfig *last; /* Always last in the struct */
} 
```

所以看起来它存储了三个不同的`OperationConfig`对象，分别名为`first`、`current`和`last`。为什么会这样呢？当我看到使用`config->first`语句引用了`OperationConfig`对象时，我认为它可能是某种链表类型的结构。当我重读`OperationConfig`结构的定义时，这种怀疑得到了证实。特别是结构定义的最后几行。

```
struct GlobalConfig {
  struct OperationConfig *prev;
  struct OperationConfig *next; /* Always last in the struct */
} 
```

这现在说得通了。`OperationConfig`对象是在一个链表结构中构建的。给定一个名为`config`的`GlobalConfig`，你可以通过调用`config->first->next`找到内存中的第二个`OperationConfig`对象，通过调用`config->first->next->next`找到第三个对象，以此类推。由于这是一个双向链表，您可以通过调用`config->last->prev`找到倒数第二个条目。酷！所以现在我知道这段代码在做什么，但是它为什么会存在呢？为什么需要维护每个操作的配置细节列表？老实说，我有点不知道回答这个问题的最佳方法是什么。我认为探索如何在整个代码库中使用`GlobalConfig`和`OperationConfig`结构将是浪费时间，并可能最终导致我跑题。我不想重蹈 Git 代码基本读取的覆辙。我想知道我是否应该查看代码库的提交日志，以跟踪为什么配置可能以这种方式构造。我发现[这个提交](https://github.com/curl/curl/commit/68920b6c113f7e3dd873d4b2d98f712c187b3765)引用了可能使用`curl`执行的多个操作。操作类似于单个 HTTP 请求。使用`curl`，您可以使用一个命令一次发送多个请求。双向链表结构用于分离每个操作的配置选项。

所以这是回答我上面提到的第二个问题的好方法。`curl`的配置依赖于操作，存储在双向链表中。在下一篇博文中，我将通过下面几行代码来回答第一个问题。

```
 /* Perform each operation */
        while(!result && config->current) {
          result = operate_do(config, config->current);

          config->current = config->current->next;

          if(config->current && config->current->easy)
            curl_easy_reset(config->current->easy);
        } 
```

下次见！