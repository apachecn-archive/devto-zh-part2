# 带有帮助消息的 Makefile

> 原文：<https://dev.to/alexbender/makefile-with-help-message-3d4h>

# Makefile

这个想法非常简单:给 makefile 中的每个目标添加注释
这里是 Makefile 中的`clean`目标:

```
clean:
    rm -f *.o 
```

Enter fullscreen mode Exit fullscreen mode

此处的帮助消息是多余的，但为了完整起见，让我们添加它:

```
clean: ## Clean directory
    rm -f *.o 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要过滤掉所有的*文档字符串*:这是命令:

```
ag '^[a-zA-Z_-]+:.*?## .*$$' --nofilename Makefile \
| sort \
| awk 'BEGIN{FS=": ## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}' 
```

Enter fullscreen mode Exit fullscreen mode

这里我用的是`silver-searcher`又名`ag`。
看起来很复杂，是吗？那么这个命令是做什么的呢？

*   基于此正则表达式过滤出行:`'^[a-zA-Z_-]+:.*?## .*$$'`，即任何类似“target_name: ##doc string”的字符串。
*   使用命令`sort`对输出进行排序
*   用`awk`命令`BEGIN{FS=": ## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}`格式化输出

让我们来看看这个命令。我们有什么？

*   BEGIN{FS=": ## "}
*   { printf " \ 033[36m %-30s \ 033[0m % s \ n]，$$1，$$2}

    来自文档:`A BEGIN rule is executed once only, before the first input record is read`。所以它只是读取输入并将字段分隔符设置为“:##”，将输入分成两部分:FS 之前和之后。

    第二部分只是打印带有格式和颜色的过滤输出。

还有一件事——我们必须定义当`make`命令不带参数启动时要执行的默认目标:
`.DEFAULT_GOAL := help`
现在我们准备好了。

结果是这样的: