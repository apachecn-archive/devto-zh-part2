# 诊断初始化脚本问题

> 原文：<https://dev.to/ferricoxide/diagnosing-init-script-issues-5b89>

最近，我一直在处理一些 COTS 软件，我的一个客户希望我自动部署这些软件。自动化其部署一直是...耐心尝试。

我第一次将安装程序作为启动时“运行一次”脚本运行时，它崩溃了。经过几次故障排除迭代，我发现五个子组件中的*一个*有故障。当我分别部署这五个子组件(并部署到不同的主机上)时，除了一个子组件之外，所有子组件都失败了。更糟糕的是，如果我从交互式用户的 shell 中运行自动安装程序，安装将会成功。每一个。该死的。时间。

所以，我想，“嗯...我需要看看我是否能够充分模拟如何从一个交互式用户的外壳中运行这个东西，同时*愚弄*安装程序，使其认为它有一个类似于主机的 init 进程所提供的环境。”所以，我开始在谷歌上搜索。

[![Commence to Googlin'](img/4958b0f2dc4d05598aab48bfe9a69fa0.png)T2】](https://www.youtube.com/embed/kroEiX5tkE0)

最终，我找到了参考文献`setsid`。基本上，这个工具允许你生成一个从 TTY 中分离出来的子外壳...就像 init 产生的进程一样。所以，我从
开始

```
 setsid bash -c '/path/to/installer -- \
       -c /path/to/answer.json' < /dev/null 2>&1 \
       | tee /root/log.noshell 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，虽然上面为脚本运行创建了一个无 TTY 的子 shell，但它仍然没有完全模拟类似 init 的上下文。该死的安装程序仍在成功完成。这不是我想要的，因为当从 init 产生的上下文中运行时，它以一种非常特殊的方式失败了。所以，回到万能的谷歌。

最终，我意识到，“init 产生的进程与交互式用户 shells 有着非常不同的环境设置。而且，作为从我的登录 shell 中分离出来的一个 shell，那个`setsid`进程将继承我的所有交互式 shell 的环境设置。一旦我意识到这一点，谷歌很快就给我指出了`env -i`。整合这个金块后:

```
 setsid bash -c 'env -i /path/to/installer -- \
       -c /path/to/answer.json' < /dev/null 2>&1 \
       | tee /root/log.noshell 
```

Enter fullscreen mode Exit fullscreen mode

当从交互式 shell 启动时，我的安装程序开始失败，就像从 init 产生的上下文中一样。我有一些东西可以告诉供应商，“您的无人值守安装程序坏了:下面是如何模拟/重现该问题。**修好它**我不知道你，但是，对我来说，一个“无人值守的安装程序”，我不得不从一个交互式外壳手动启动，真的不是那么有用。