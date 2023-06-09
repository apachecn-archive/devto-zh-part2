# 使用 meg 自动化您的侦察工作流程

> 原文：<https://dev.to/edoverflow/-automating-your-reconnaissance-workflow-with-meg-2koi>

在过去的几个月里，我一直在玩一款由汤姆·哈德森开发的名为“T2·梅格·T3”的工具，我已经爱上了这款工具。meg 是一个轻量级的 URL 提取器，它将输出存储在有组织的目录和文件中。这个工具已经成为我侦察工作流程中的精华元素，并且已经融入到我的许多个人工具中。

Tom 来自一个发展中的背景，因此完全理解大规模扫描的问题。这就是 meg 设计的主要目的不是资源密集型的原因。默认情况下，该工具的行为就像普通用户浏览网页一样，因此不太可能关闭服务。

## 基础知识

简单地说，运行 meg 所要做的就是指定一个端点，然后指定一个主机。

```
$ meg <endpoint> <host>
$ meg / https://edoverflow.com 
```

Enter fullscreen mode Exit fullscreen mode

后一个命令为 https://edoverflow.com(`https://edoverflow.com/`)的[请求顶层目录。值得注意的是，协议必须详细说明；meg 不会自动为主机添加前缀。如果您碰巧有一个没有协议的目标列表，请确保`sed`该文件并添加正确的协议。](https://edoverflow.com) 

```
$ sed 's#^#http://#g' list-of-hosts > output 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，meg 将所有输出存储在一个`out/`目录中，但是如果您想要包含一个专用的输出目录，只需将输出目录附加到您的命令中，如下所示:

```
$ meg / https://edoverflow.com out-edoverflow/ 
```

Enter fullscreen mode Exit fullscreen mode

## 在网络上发现有趣的文件

假设我们想要查明特定的文件，这些文件要么可以在针对某个平台时进一步帮助我们，要么如果向公众公开，其本身就是一个实际的安全问题，所需要的只是一个端点列表(`lists/php`)和一系列目标(`targets-all`)。对于这个过程，存储所有返回“200 OK”状态码的页面将帮助我们筛选出大多数噪音和误报(`-s 200`)。

```
$ meg -s 200 \
  lists/php targets-all \
  out-php/ 2> /dev/null 
```

Enter fullscreen mode Exit fullscreen mode

一旦上面的命令运行完毕，所需要做的就是浏览`out-php/`目录来找到保存的响应。您可以从手动搜索特定的字符串开始——确保在 Bash 中使用`grep`和数组自动执行这个过程的一部分——或者您可以使用 Tom 向我展示的一个巧妙的小技巧。

```
$ cat ~/bin/vimprev 
#!/bin/bash
VIMENV=prev vim $@ 
```

Enter fullscreen mode Exit fullscreen mode

你猜对了——vim 预览窗口！对于输出目录中的每个文件，我们可以在 vim 中预览它。抛开所有“退出 vim”的笑话不谈，这是一个绝妙的主意，我对汤姆感激不尽。

```
$ cat /path/to/vimrc
if $VIMENV == 'prev'
 noremap <Space> :n<CR>
 noremap <Backspace> :N<CR>
endif
$ export PATH=$PATH:~/bin
$ cd out/
$ vimprev $(find . -type f) 
```

Enter fullscreen mode Exit fullscreen mode

`vimrc`技巧允许你使用空格键移动到下一个文件，使用退格键移动到上一个文件。按住空格键，尽情发挥吧！看起来有趣的文件应该会很快跳出。

## 实时预览

如果您想要在输出目录中实时预览保存的文件，您需要做的就是在输出目录中跟踪`index`文件，就像您在 web 服务器上跟踪日志一样。

```
$ cd out/
$ tail -f index
out/example.com/2d676fb9c99611db7e6cb75ffa1b137673f4ca04 http://example.com/.well-known/security.txt (200 OK) 
```

Enter fullscreen mode Exit fullscreen mode

## 实验时间

我将缩短这篇文章，并鼓励读者观看汤姆的演讲“[被动侦察技术](https://www.youtube.com/watch?v=DvS_ew77GXA)”。本次演讲中描述的所有技术都可以通过 meg 实现自动化，正如 Tom 所展示的，这些技术非常有用。

[![](img/733e9048b7c9554034e18074e96635f9.png)T2】](https://www.youtube.com/watch?v=DvS_ew77GXA)