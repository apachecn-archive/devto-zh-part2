# 在 Ansible 中创建警报回调插件-第一部分

> 原文：<https://dev.to/jrop/creating-an-alerting-callback-plugin-in-ansible---part-i-1h0n>

最近，我的部门已经将 Ansible 应用到我们更多的工作流程中。我太喜欢它了，以至于我把我的 dotfiles repo 转换成使用 Ansible。尽管我们对将如此多的工作流引入自动化流程感到无比兴奋，但我们最近也不得不承认，我们对 Ansible 实际上对我们的基础设施做了什么的了解并不完美。

回到周一早上，我开始收到电子邮件抱怨我们的一个批处理作业没有完成。在查看了一个完整的日志文件后，我发现问题是试图执行我们的脚本的用户没有我们的脚本所在的父目录的目录执行权限。这是一个简单的修复。该作业被释放并运行至完成。尽管如此，我还是想知道在更改目录权限时发生了什么，特别是因为我知道无论是我还是其他人都没有对那个特定的系统进行任何修改。

果不其然，在联系我们的系统管理员后，证实了剧本已经运行并出现了前面提到的不良结果。没什么大不了的:不到 15 分钟，有问题的剧本就被修复了。

危机解除。冲突已解决。然而，我开始在脑海中回顾我们如何在未来避免这种情况，这给了我一个想法:我们(方便地)将我们的主机清单按团队分成不同的组。如果有一个插件可以在主机发生变化时，查找该主机上的变量以查看是否设置了任何“警报配置”，并向给定系统的相关人员发出警报，这难道不方便吗？

## 提出解决方案

让我们从我想要的解决方案开始，反向工作。说我有以下库存:

```
[webteam]
webteam1.tld
webteam2.tld

[systeam]
systeam1.tld
systeam2.tld 
```

Enter fullscreen mode Exit fullscreen mode

我想通过`group_vars`管理在系统发生变化时我想提醒的人:

**group _ vars/web team . yml**:

```
notify_on_change:
  email:
    - somebody1@domain.tld
    - somebody2@domain.tld
  slack:
    ...slack config... 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。但是 Ansible 并没有现成的这种功能。但是等一下！事实证明，开发一个插件[相对来说并不痛苦](https://docs.ansible.com/ansible/2.5/dev_guide/developing_plugins.html)。原来我要开发的东西叫做“回调插件”。回调插件是一个 Python 类，在 Ansible 生命周期的不同阶段都会被调用。我们可以编写的最短的回调插件如下:

```
from ansible.plugins.callback import CallbackBase

class CallbackModule(CallbackBase):
  CALLBACK_VERSION = 2.0
  CALLBACK_TYPE = 'aggregate'
  CALLBACK_NAME = 'custom' 
```

Enter fullscreen mode Exit fullscreen mode

这个回调插件不会做任何事情，但是有几点需要注意:这个回调插件正在宣传:

*   它希望使用 v2 API
*   它的类型是 **`aggregate`** :回调插件的类型很重要。更具体地说，什么类型的回调插件是**而不是**更加重要，因为**一次只能启用一个`stdout`类型的回调插件**。
*   它的名字叫`custom`。我们以后会用这个名字来指代回调插件。据我所知，文件名(如`whatever.py`)不是用来指回调插件的

如前所述，可以同时激活多个回调，但是一次只能激活一个类型为`stdout`的回调插件。那么我们如何启用回调插件呢？很简单，进入你友好的`ansible.cfg`，设置如下配置:

```
[defaults]
# ...
# comma separate callback plugins to whitelist: callback_whitelist = custom 
```

Enter fullscreen mode Exit fullscreen mode

这将指示 Ansible 启用我们已经定义的回调插件。但是这些回调插件去哪了呢？同样，Ansible 对此有一个简单的答案:在`callback_plugins/`目录中，要么 1)在您包含的某个角色中，要么 2)在您的剧本旁边。到目前为止，可能的目录结构如下所示:

```
my-playbook-folder/
  callback_plugins/
    my-callback-plugin.py
  ansible.cfg
  playbook.yml 
```

Enter fullscreen mode Exit fullscreen mode

## 构建回调插件

好吧，那么我们的插件到底应该做什么？为了简单起见，我们将指定插件的行为来完成以下任务:

*   将 Slack 用作通知服务
*   除非任务报告它“更改”了目标系统，否则不要发送通知
*   如果发生了变化，使用特定的 hosts host-/group- vars 查找给定系统的利益相关者。

这就是事情开始变得有点棘手的地方:为了检查游戏和任务的状态，我们将深入 Ansible 的内部。值得庆幸的是，有一个很好的社区来支持你的努力，但是让我们从提到你的回调插件可以覆盖的所有方法可以在`CallbackBase`类的[中找到开始。经过一番挖掘，以及等待 StackOverflow 问题的答案](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/callback/__init__.py)的一小段时间后，我选定了下面的基本代码:

```
from ansible.plugins.callback import CallbackBase

class CallbackModule(CallbackBase):
  CALLBACK_VERSION = 2.0
  CALLBACK_TYPE = 'aggregate'
  CALLBACK_NAME = 'is'

  def v2_playbook_on_play_start(self, play):
    self.vm = play.get_variable_manager()

  def v2_runner_on_ok(self, result):
    if self.vm.get_vars()['ansible_check_mode']:
      return

    host_vars = self.vm.get_vars()['hostvars'][result._host.name]
    if 'notify_on_change' in host_vars and result.is_changed():
      pass
      # TODO: Post Slack message 
```

Enter fullscreen mode Exit fullscreen mode

*警告*:我不知道 Ansible 的内部机制，在经过多次反复试验后，我发现上面的代码可以“工作”。我的*印象*是主机和组变量在每次游戏的基础上被实例化和合并。这就是为什么变量管理器在“播放开始时”被捕获并存储以备后用。稍后，当一个任务完成时，我们使用变量管理器来检查 Ansible 是否运行在`--check`模式，然后稍后检索特定主机的`notify_on_change`配置。

## 一点调试策略

所以这是我对 Python 不支持类型注释感到不可理喻的愤怒的部分。如果是这样的话，我也许能够围绕着传递给我的回调插件钩子的参数，快速地发现我到底能得到什么。谁知道呢，我甚至可以使用像 IntelliSense 或 Content Assist 这样的 IDE 提示来快速检索我需要的内容。

如果你能看到我此刻的样子，我气得满脸通红，汗水顺着脖子往下滴，你可能会注意到我正在冷静下来，并提醒自己脚本语言的优点。事实上，我最喜欢的快速开发工具之一是 Node.JS。时间拉了一些调试-傅。

经过一番探索，有一个简单的技巧可以让你暂停任何 Python 程序并调用一个交互式调试器:

```
import pdb; pdb.set_trace() 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。我现在可以把它放到我的插件中，在我想检查变量的地方进行迭代(*非常慢*)，直到我得到一些有效的代码。

例如:

```
class CallbackModule(CallbackBase):
  # ...
  def v2_runner_on_ok(self, result):
    import pdb; pdb.set_trace()
    # ... same code as before
  # ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我像平常一样调用 Ansible，它会一直运行，直到调用我的`v2_runner_on_ok`方法，然后它会立即挂起，让我进入一个请求-求值-打印循环(REPL ),在这里我可以交互式地单步执行我的代码，并检查我的作用域内的变量！

## 现在就这些

我将在这里结束这篇文章，因为它已经超出了我的计划。即使不是因为这个，我仍然不得不结束它，因为这是我在这一点上实现的全部。希望我能尽快完成我的回调插件(或者你们中的一个会抢先我一步)，我会在“第二部分”分享我的发现和代码。