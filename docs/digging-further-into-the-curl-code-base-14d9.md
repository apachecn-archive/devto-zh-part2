# 深入挖掘 curl 代码库

> 原文：<https://dev.to/captainsafia/digging-further-into-the-curl-code-base-14d9>

在[我写的最后一篇博文](https://dev.to/captainsafia/figuring-out-how-curl-stores-configurations-dd8)中，我学习了`curl`如何维护不同操作的配置细节。在这篇博文中，我想弄清楚`curl`是如何执行这些操作的。具体来说，我想深入下面列出的代码段。

```
/* Perform each operation */
while(!result && config->current) {
    result = operate_do(config, config->current);

    config->current = config->current->next;

    if(config->current && config->current->easy)
        curl_easy_reset(config->current->easy);
} 
```

这是怎么回事？如前所述，`curl`正在执行的每个操作的配置存储在一个双向链表中。

侧边栏:对于那些不熟悉双向链表数据结构的人来说，它类似于一个链表，但是除了在每个节点中有一个指向下一个节点的指针之外，你还有一个指向上一个点的指针。阅读此了解更多信息。

上面的 while 循环遍历当前存储在内存中的每个配置，并执行相关操作。这里最有趣(听起来也不吉利)的函数是`operate_do`函数。你可以从它的名字看出来，但是`operate_do`函数确实做了很多事情。我不骗你；整个函数大约有 1700 行。这样做合法吗？我觉得应该有一个规则禁止做这样的事情。你怎么开始研究这么长时间的函数呢？

我告诉你怎么做。你快速浏览代码。我没开玩笑。当一个函数如此之长时，您通常可以直观地看到模式。例如，当我滚动代码时，我意识到相当一部分代码包含了对像`my_setopt_str`、`my_setopt`和`my_setopt_enum`这样的函数的调用。如果我必须仔细观察，我会说函数中大约 70%的代码行都是对`my_setopt*`风格函数的调用。

这些函数将一个`curl`参数作为输入，该参数是对存储在配置中的一个`CURL`对象和应该设置的配置项的引用。我对钻研这个函数不太感兴趣。通过查看函数名和参数，我大致了解了这些代码块的用途:“在我们实际执行操作之前，确保它的操作配置存储在正确的位置。”

一旦我发现了`operate_do`函数中的`my_setopt`趋势，我就试图通过噪声来查看，看是否能找到实际的“做”在哪里发生。有几个有趣的片段。例如，下面的代码介绍了遍历 URL 列表中的每个 URL 并对每个 URL 执行一组特定函数的逻辑。

```
 /*
  ** Nested loops start here.
  */

  /* loop through the list of given URLs */

  for(urlnode = config->url_list; urlnode; urlnode = urlnode->next) { 
```

例如，它执行与将文件上传到特定 API 端点相关的代码。

```
 /* Here's the loop for uploading multiple files within the same
       single globbed string. If no upload, we enter the loop once anyway. */
    for(up = 0 ; up < infilenum; up++) {
    ...
    } 
```

我在上面观察到的大多数`my_setopt`函数都位于遍历每个 URL 的 for 循环中。

我想调查的最后一件事是从`operate_do`函数返回的`result`对象是在哪里被操作的。该对象是一个整数状态代码，代表`curl`执行的状态(是否遇到错误或者是否缺少参数)。因为它是一个特别重要的状态代码的表示，所以每当它被修改时，它很可能与一个重要的函数调用相关联。这里是`operate_do`函数中`result`对象被修改的所有地方。

1.  `result = CURLE_FAILED_INIT;` ( [L241](https://github.com/curl/curl/blob/899630021153b2a26a43008cccc6620b6c3f9bbf/src/tool_operate.c#L214) )
2.  `result = curl_easy_getinfo(config->easy, CURLINFO_TLS_SSL_PTR, &tls_backend_info);` ( [L237-L239](https://github.com/curl/curl/blob/899630021153b2a26a43008cccc6620b6c3f9bbf/src/tool_operate.c#L237-L239) )

哇哦。我只写了个参考文献，我意识到如果我都写了，我会在这里呆一整天，还有很多事要做。我将免去你通读所有参考资料并总结我所看到的趋势的痛苦。实际上，每当 HTTP 请求失败，或者某种 SSL 证书配置不正确，或者没有足够的内存来写入请求的输出，等等，都会修改`result`。通读代码中修改了`result`的地方让我对`operate_do`函数做了多少工作有了更多的了解，也就是所有的工作。

我正在考虑是否要更严格地研究这个函数。我一方面觉得还有更多有待发现，但另一方面又觉得我需要更有条理地阅读这些代码。思考了一会儿之后，我决定通过观察执行下面的`curl`命令时具体会发生什么来尝试进行另一次代码潜水。

```
$ curl https://safia.rocks 
```

下次见！