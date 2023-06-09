# 18 行代码中的容器

> 原文：<https://dev.to/napicella/containers-from-scratch-in-18-lines-of-code-3pmn>

想知道容器是什么吗？
回答这个问题的最好方法之一是从头开始构建一个容器。

## 容器

容器是一种抽象，主要由三部分组成:

*   cgroups:控制你能在容器中使用什么
*   名称空间:控制你能在容器中看到什么。例如容器中运行的进程列表、pid、主机名等。
*   网络:管理你可以在一个容器中与谁沟通

这在实践中看起来如何？最好的方法是写一些代码。

所以我们开始吧。让我们创建一个在新进程中打开 bash 的小程序。
**我们希望流程有一个新的名称空间。**
我们如何测试这是否可行？我们可以输入由我们的程序`hostname newhostname`打开的 bash，并在退出程序后查看主机名是什么。

让我们看看它的实际效果。在下面的例子中，程序被称为`namespace`。
你可以在文末找到代码。

[![console1](img/b4b5b7cc3871e30535c7cb73c5195256.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8LKjghqt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wb850r39wkfewf4svn91.gif) 
在运行命令之前我的主机名是:`nicola`。
然后我们运行`sudo ./namespace`，启动`container`。
然后我们将容器中的主机名改为`container`。
当我们退出程序时，主机名又是`nicola`。
容器有了自己的名称空间，真好！
还可以看到容器中的 pid 是`1`。

## 自己动手

试试看。例如，将以下代码复制到名为 namespace.c 的文件中。

```
#define _GNU_SOURCE 
#include <sched.h>  
#include <stdio.h> #include <stdlib.h> #include <signal.h>  
#include <sys/types.h> #include <sys/wait.h> #include <unistd.h>  
int child(void *args)
{
    printf("pid as seen in the child: %d\n", getpid());
    system("bash");

}

int main()
{
    int namespaces = CLONE_NEWUTS|CLONE_NEWPID|CLONE_NEWIPC|CLONE_NEWNS|CLONE_NEWNET;
    pid_t p = clone(child, malloc(4096) + 4096, SIGCHLD|namespaces, NULL);
    if (p == -1) {
        perror("clone");
        exit(1);
    }
    printf("child pid: %d./na \n", p);
    waitpid(p, NULL, 0);
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
gcc namespace.c -o namespace
chmod +x namespace
sudo namespace 
```

Enter fullscreen mode Exit fullscreen mode

### 结论！

不可否认，这是一个超级简单的容器，但是它应该让你开始理解容器的抽象！

谢谢！

在推特上关注我