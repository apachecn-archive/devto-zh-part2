# 使用最新的 git-reflow 定制 Git 工作流

> 原文：<https://dev.to/codenamev/custom-git-workflows-with-the-latest-git-reflow-42ie>

[![New git-reflow release: Custom Git Workflows](img/071abe10cc841457617ee13dd127fdd0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VKD6aTFm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.codenamev.com/conteimg/2019/07/reflow-workflows-header.jpeg)

这个版本包含了几年的努力，允许您定制我们的过程，以满足您的特定需求。我们已经放弃了严重的依赖性(`gli`)，并引入了一个非常灵活的 DSL 来创建新的 git 命令或覆盖我们现有的命令。

我非常激动地宣布[`git_reflow`gem](https://github.com/reenhanced/gitreflow)0 . 9 . 0 版本的发布！

### 自定义工作流程

Git-reflow 的默认流程并不适合每个团队，这也是我们引入`Workflows`的原因。

我们引入了一个特殊的文件，它可以包含对默认流程的定制。有了它，您可以:

1.  添加要在任何命令之前或之后运行的挂钩
2.  使用我们预先配置的工作流之一作为您自己工作流的基础
3.  覆盖任何默认命令
4.  创建新命令

这里有一个例子，展示了定制流程的各种方法:

```
# Use a pre-defined workflow
use "FlatMergeWorkflow"

# Do something before the `start` command
before :start do
  say "Booya! Started this bad girl."
end

# Do something after the `deliver` command
after :deliver do
  run "say 'Well done!'"
end

# Override the `start` command to auto-name feature branches
command :start, arguments: { feature_branch: nil }, flags: { base: 'dev' }, switches: { loud: false } do |**params|
  base_branch    = params[:base]
  feature_branch = params[:feature_branch] || "feature-#{Time.now.strftime("%Y-%m-%d")}"

  run_command_with_label "git checkout #{base_branch}"
  run_command_with_label "git pull origin #{base_branch}"
  run_command_with_label "git push origin #{base_branch}:refs/heads/#{feature_branch}"
  run_command_with_label "git checkout --track -b #{feature_branch} origin/#{feature_branch}"
end

# Create a new command
# Makes `git reflow boom` available in the command line.
command :boom do
  GitReflow.say "Boom."
end 
```

您可以为每个项目定义一个独特的工作流，只需将它放在项目的根目录中，或者用文件的完整路径设置一个特殊的`reflow.workflow` git-config 设置。

您传递给每个 git-reflow 保留命令(`use`、`before`、`after`、`command`)的块在我们的[核心工作流类](https://github.com/reenhanced/gitreflow/blob/master/lib/git_reflow/workflows/core.rb)的上下文中执行。这使您能够使用任何可用于`GitReflow`模块的代码。我们鼓励您尽可能多地使用 GitReflow 的工具，以便您可以充分利用 GitReflow 内部包含的当前分支名称、日志记录、异常处理、彩色输出和其他几个特性。要获得可用内容的完整列表，请参见我们 wiki 上的[可用工作流助手](https://github.com/reenhanced/gitreflow/wiki/Custom-Workflows#available-workflow-helpers)。

### 升速至 1.0

随着`Workflows`的重新运作，我们正在为我们的第一个主要版本做准备！我们目前的计划侧重于:

1.  介绍 Pivotal Tracker 和 Trello 工作流程
2.  添加 GitLab 集成
3.  添加开源工作流

我们期待着一些激动人心的时刻！我们很乐意听到您使用这些新工作流程的所有有趣方式，所以请不要犹豫让我们知道。

黑客快乐！