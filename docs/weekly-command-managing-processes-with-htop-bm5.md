# 每周命令:使用 htop 管理流程

> 原文：<https://dev.to/roperzh/weekly-command-managing-processes-with-htop-bm5>

[![overview](../Images/94a326d7917ce79540950a83381f75bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xNyct_pe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/4419992/40049134-474ba27e-580a-11e8-9068-bc5951933f9e.jpg)

## 基础知识

```
htop [-dChustv] 
```

`htop`是一个交互式命令行流程管理器，允许您可视化、排序和管理系统中运行的流程。

与许多其他优秀的命令行实用程序一样，`htop`是用`ncurses`构建的，所以您可能对这个界面很熟悉。

您可以运行不带任何标志的命令，并立即开始与正在运行的进程进行交互。

*有趣的事实:社区有一个`htop`出现过的电视节目和电影的[列表。](https://hisham.hm/htop/index.php?page=sightings)*

## 例子

设计得非常好，你最大的盟友是底部的帮助栏，这使得程序使用起来非常直观，尽管如此，这里还是有一些例子。

### 发送不同的信号

当底部栏菜单显示用`F9`终止所选过程的选项时，您可以通过按下`k`键并选择所需信号向其发送任何信号。

### 标记多个流程

通过使用标记功能，可以标记多个进程，然后同时发出信号。您可以用空格键`␣`标记一个进程，或者用`c`标记一个进程及其所有子进程。

要一次取消所有进程的标记，您可以使用`U`。

### 搜索和过滤

通过使用`F3`或`/`进行搜索，使用`F4`或`\`进行过滤，您可以按名称逐步搜索和过滤流程。

### 借力论辩

除了用户界面的所有优点，你还可以在通过自定义标志启动时管理`htop`的一些功能，以下是我的一些最爱:

*   以单色模式启动程序，个人觉得很好用。
*   `--pid=PID1,PID2...`仅显示给定的以逗号分隔的 PID 列表。
*   `--tree`以树形视图显示流程。

## 配置

[![config](../Images/8eee76f772af4cdfbeedb54780d7ddc9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--orrM4SKq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/4419992/40049133-471ec18c-580a-11e8-94ba-04ec5cf3370a.jpg)

提供了一个很好的界面来配置 UI:从顶部的仪表到每个进程显示的列数，几乎一切都可以改变。这个界面存在于设置菜单中，可以用`F2`打开，一旦打开就会出现几个菜单供你配置。

默认情况下，您的配置存储在`~/.config/htop/htoprc`中，该文件由`htop`自动管理，因此您不必手动更改它。配置文件的路径可以用`$HTOPRC`环境变量来设置，允许你为不同的场景设置不同的配置文件。