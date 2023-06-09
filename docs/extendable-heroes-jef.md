# 可扩展的英雄

> 原文：<https://dev.to/moopet/extendable-heroes-jef>

你知道你可以用任意命令扩展`git`吗？你曾经想在你自己的程序中做同样的事情吗？

当您使用 git 扩展时，如 [git flow](https://github.com/nvie/gitflow) ，您可以将它用作 git 子命令。像这样:

```
git flow <action> 
```

Enter fullscreen mode Exit fullscreen mode

你有没有想过 git-flow 是怎么把自己添加成插件的？这不是一个别名，这是一个完全成熟的命令。安装程序把它放在一个特殊的目录里吗？它使用 API 注册自己吗？

嗯，比那个简单:

```
$ which git-flow
/usr/bin/git-flow 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，它实际上是一个名为`git-flow`的完全正常的命令，git 像变魔术一样找到并传递控制给它。如果路径中的任何地方有一个名为`git-<subcommand>`的可执行文件，git 知道它也参与了。是的，这些都在 git 文档中。

这不是一个整洁的系统吗？每个命令仍然有一个单独的责任，可以直接运行，但也作为父命令的一部分出现。

那么，我们能在自己的应用程序中做同样的事情吗？没错。我们当然可以！

我将向你展示如何使用(我老板称之为)*非常不流行的猛击*。我要从头开始。我的意思是，我们总是可以查看 git 的源代码并复制它(哦，自由软件的乐趣！)，但是乐趣在哪里呢？

# 我们的代码需要做什么？

*   列出可用的子命令
*   确定参数是否是有效的子命令
*   将参数和环境传递给经过验证的子命令

# 要做到这一点，我们需要做些什么？

*   父程序。我们称之为`frobulator`
*   一个单独的子命令，我们称之为`demo`

该子命令将作为一个文件存在于用户的`$PATH`中的某个地方，名为`frobulator-demo`，并且可以与命令`frubulator demo [options]`一起使用
。

下面是我将用于演示命令的内容:

```
#!/usr/bin/env bash

echo "Hello from $(basename "$0")!"

if [ "$#" = 0 ]; then echo "I have been passed no arguments"
else echo "My arguments are:"

  while [ -n "$1" ]; do echo "  '$1'"
    shift done
fi

if [ -n "$MESSAGE" ]; then echo "My environment includes \$MESSAGE=$MESSAGE"
else echo "My environment does not include \$MESSAGE"
fi 
```

Enter fullscreen mode Exit fullscreen mode

这应该是不言自明的。

# 我们需要在哪里寻找可执行文件？

我们可以在一个指定的目录中查找，比如`$HOME/frobulator/modules/`。
这样行吗？也许吧。
但是如果它只是在你的普通`$PATH`中，一切都会变得容易得多，因为子命令本身就是可执行的。我们怎样才能找到路呢？

让我们使用`find`来检查`$PATH`环境变量中提到的每个目录中以正确前缀开头的可执行文件。`find`相当酷。

*   我们应该通过指定`-maxdepth 1`来限制它，这样它就不会递归搜索
*   我们应该忽略 GNU find 上的`-executable`标志和 BSD find 上的`-perm +111`条件不可执行的任何内容。这有点尴尬，但这不是世界末日。我们只需找出我们正在处理的系统，并将标志放入一个变量中。
*   由于目录也可以是可执行的，我们可以用`-type f`过滤常规文件。
*   我们应该整理输出，使其不包含路径或文件名前缀。在这个函数的中间做这个有点棘手，所以你知道吗，我们稍后再做 <sup id="fnref1">[1](#fn1)</sup> 。

`$PATH`变量包含一个由冒号(`:`)分隔的路径列表，但是`find`需要一个由空格分隔的路径列表 <sup id="fnref2">[2](#fn2)</sup> ，所以我们将使用一点变量替换来用冒号替换空格。

```
if find --version > /dev/null 2>&1; then executables_flag='-executable'
else executables_flag='-perm +111'
fi find ${PATH//:/  } -maxdepth 1 -type f $executables_flag 
```

Enter fullscreen mode Exit fullscreen mode

在 shell 脚本中，函数只能返回整数，整数通常用作错误状态。然而，发送到 stdout 的任何内容(如`find`的输出...)可以在一个变量中被捕获，比如:

```
my_variable=$(find_subcommands) 
```

Enter fullscreen mode Exit fullscreen mode

## 如何判断`$PATH`上是否存在可执行文件？

我们不希望命令返回意外的错误消息。如果我们只是漫不经心地假设子命令是可用的，那么第一次有人打错了，得到类似于:

```
$ frobulator demon "hello world"
-bash: frobulator-demon: command not found 
```

Enter fullscreen mode Exit fullscreen mode

那他们会很困惑。嗯，也许不是，但拦截这种情况并实现我们自己的 frobulator 特定的错误消息不会有什么坏处。

我们可以用`which <program name>`来判断一个程序是否可用。如果`which`没有找到匹配，就会返回错误，否则会打印出所有匹配。我们可以通过丢弃输出(到`/dev/null`)来抑制它。

注意，虽然 BSD 的`which`有一个静默操作标志(`-s`)，但 GNU 版本中没有，所以为了可移植性，我们将采用前一种方法。

```
if which "$subcommand" > /dev/null; then echo "doing stuff..."
else echo "oh noes!"
fi 
```

Enter fullscreen mode Exit fullscreen mode

## 如何将参数传递给子命令？

如果我们想在一个好的函数中包含所有这些内容，我们将失去查看其余参数的能力，除非它们被传递到自定义函数或保存在一个全局变量中。我们将选择前者，尽管拥有大量的全局变量对于 shell 脚本来说是相当标准的:

```
# call_subcommand <parent_command_name> <subcommand> [args...]
call_subcommand() {
  subcommand="$(basename "$1")-$2"
  shift 2
    exec "$subcommand" "$@"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 如何将我们的环境传递给子命令？

那也很容易。我们从父命令中显式地`export`得到的任何东西对子命令都是可用的。我们可以把它放在一个非常方便的函数中来加分。

```
share_environment() {
  export MESSAGE="hello yes I am dog."
} 
```

Enter fullscreen mode Exit fullscreen mode

# 把所有的东西放在一起...

```
#!/usr/bin/env bash

# find_subcommands <prefix>
find_subcommands() {
  if find --version > /dev/null 2>&1; then executables_flag='-executable'
  else executables_flag='-perm +111'
  fi find ${PATH//:/  } -maxdepth 1 -type f $executables_flag -name "$(basename "$1")-*"
}

# Friendly-print the results of find_subcommands.
list_subcommands() {
  subcommands=$(find_subcommands "$1")

  if [ -n "$subcommands" ]; then echo "Available subcommands:"

    for subcommand in $subcommands; do echo " $(basename "$subcommand")" | sed -e "s#$1-##"
    done
  fi
}

# Choose what to export to the subcommand.
share_environment() {
  export MESSAGE="Hello yes I am dog."
}

# call_subcommand <parent_command_name> <subcommand> [args...]
call_subcommand() {
  subcommand="$(basename "$1")-$2"

  if which "$subcommand" > /dev/null; then share_environment
    shift 2
    exec "$subcommand" "$@"
  fi

  return 1
}

# Example dispatcher.
case "$1" in foo)
    echo "Running internal foo command."
    ;;

  bar)
    echo "Running internal bar command."
    ;;

  *)
    if ! call_subcommand "$0" "$@"; then echo "Invalid subcommand."
      echo "Available commands:"
      echo "  foo"
      echo "  bar"
      list_subcommands "$0"
      exit 1
    fi
esac 
```

Enter fullscreen mode Exit fullscreen mode

# ...并尝试它

```
$ frobulator foo
Running internal foo command.

$ frobulator help Invalid subcommand.
Available commands:
  foo
  bar
Available subcommands:
  demo

$ frobulator demo one two three
Hello from frobulator-demo!
My arguments are:
  'one'
  'two'
  'three'
My environment incudes $MESSAGE=Hello yes, this is dog. 
```

Enter fullscreen mode Exit fullscreen mode

# 你能做的其他事情

*   扩展父程序，允许形式为`<parent command> help <child command>`的命令改为执行`<child command> --help`。
*   重写整件事，只会更好。

请记住，这不一定要在 shell 脚本中完成。您可以在任何命令行应用程序中使用相同的技术，不管它是用什么语言编写的。因为这实际上只是其他命令的调度程序，所以您每次都可以选择不同的语言！

封面图片来自 morguefile.com[的 pschubert](https://morguefile.com/creative/pschubert)

* * *

1.  我以此哲学为生。 [↩](#fnref1)

2.  从技术上讲，内部字段分隔符是用户用 shell 变量`IFS`定义的，我们可以在`find`命令期间将它设置为冒号，然后再将其设置回来。我试着让事情变得简单。 [↩](#fnref2)