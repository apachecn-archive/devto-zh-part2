# 用于 Bash 的 Python

> 原文：<https://dev.to/_andy_lu_/python-for-bash-3798>

*这最初发表在我的[博客](https://andyrlu.com/2018/07/28/python-for-bash.html)上。*

你喜欢`bash`剧本吗？我个人不这么认为。

所以当我需要写 bash 脚本时，我会找出我需要的命令，然后用 Python 把它们粘在一起。

自从我需要这样做已经有一段时间了，虽然我以前忽略了它，
`subprocess`模块是运行这些命令的最佳方式。

## Python 的快速介绍`subprocess.py`

### 开发环境

如果你跟我一起在这里，你会想要使用至少`python 3.5`。在此之前的任何版本，你将不得不在这个模块中使用不同的 API 来做我将向你展示的事情。

### 命令

这个模块的主力是`subprocess.Popen`类。有很多论点你可以通过这门课，但是如果你是新手的话，它可能是压倒性的，更不用说是过度的。

幸运的是，`subprocess`模块中有一个函数我们可以与之交互:`subprocess.run()`。

这是传递了一些典型参数的函数签名。(我从[文档](https://docs.python.org/3/library/subprocess.html) )

```
subprocess.run(args, *, stdin=None, input=None, stdout=None, stderr=None,
shell=False, cwd=None, timeout=None, check=False, encoding=None, errors=None,
text=None, env=None)*) 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很复杂，但是我们实际上可以忽略其中的大部分，仍然可以做一些很好的事情。让我们看一些例子。

#### 一个基本例子

```
import subprocess as sp

result = sp.run("pwd")
print(result) 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
/this/is/the/path/to/where/my/terminal/was/
CompletedProcess(args="pwd", returncode=0) 
```

Enter fullscreen mode Exit fullscreen mode

其输出是运行该脚本的目录的路径；正如你所料。然后还有一些`CompletedProcess`物体。这只是一个对象，它存储了关于所运行命令的一些信息。对于本指南，我将忽略它，但我会在最后提供链接，您可以在那里阅读所有相关内容。

但就是这样！这就是运行一些基本的`bash`命令所需的全部内容。唯一的警告是您将缺少 shell 的一些特性。

为了克服这一点，让我们看看下一个例子。

#### 更好的例子

```
import subprocess as sp

result = sp.run("ls -lah > someFile.txt", shell=True)
output = sp.run('ls -lah | grep ".txt"', shell=True) 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到前面函数签名中的`shell=False`，但是这里我把它设置为`True`。通过这样做，我想要的命令实际上是在 shell 中运行的。这意味着我可以访问重定向和管道，就像我展示的那样。

关于这样运行的注意事项:您想要执行的命令必须完全按照您在 shell 上执行的方式键入。如果通读文档，您会注意到有一种运行命令的方法，比如传入一个字符串列表，其中每个字符串要么是命令，要么是标志，要么是主命令的输入。

我发现这令人困惑，因为如果你遵循我的“更好的例子”的方式，你永远不会怀疑你是否正确地传递了参数。最重要的是，您可以自由地使用 Python 来构建基于各种条件的命令。

这是我这样做的一个例子。

#### 一个“真实世界”的例子

```
#!/usr/bin/env python3

###############################################################################
#                                   Imports                                   #
###############################################################################
import subprocess as sp
from datetime import date

###############################################################################
#                                  Functions                                  #
###############################################################################

def getTodaysDate():
  currDate = date.today()
  return f"{currDate.year}-{currDate.month}-{currDate.day}"

def moveToPosts():
  lsprocess = sp.run("ls ./_drafts", shell=True)
  fileList = lsprocess.stdout.decode('utf-8').strip().split("\n")
  hasNewPost = len(fileList)

  if (hasNewPost == 1):
      print("New post detected")

      srcName = "./_drafts/" + fileList[0]
      destName = " ./_posts/" + getTodaysDate() + "-" + fileList[0]

      command = "mv "+ srcName + destName
      sp.run(command, shell=True)

      return [destName, files[0]]

  elif hasNewPost == 0:
      print("Write more!")
  else:
      print("Too many things, not sure what to do")

def runGit(fullPath, fileName):

  commitMsg = "'Add new blog post'"

  c1 = "git add " + fullPath
  c2 = "git commit -m " + commitMsg

  cmds = [c1,c2]

  for cmd in cmds:
    cp = sp.run(cmd, shell=True)

if __name__ == "__main__":
  pathToPost, fileName = moveToPosts()
  runGit(pathToPost, fileName)
  print("Done") 
```

Enter fullscreen mode Exit fullscreen mode

由于这个博客的运行要感谢 Jekyll，我利用了我可以使用的`_drafts`文件夹。

对于那些不熟悉 Jekyll 的人来说，`_drafts`是一个文件夹，你可以在其中存储尚未准备好发布的博客文章。发布的帖子进入`_posts`。

该文件夹中的文件名类似于:`the-title-of-my-post.md`。位于`_posts`文件夹中的已发布帖子的文件名相同，但在草稿名称前面附加了`year-month-day-`。

有了这个脚本，我只需写一篇帖子，然后放入`_drafts`。然后我打开一个终端并运行这个脚本。首先，它在`_drafts`中查找并创建一个文件名数组。除了找到一个文件之外的任何事情都会停止脚本——总有一天我会改进它。有了这个文件名，在`subprocess.run()`的帮助下，脚本将草稿移动到`_posts`，给它一个合适的名称，然后提交给`git`给我。

## 总结起来

我介绍了`subprocess.run()`函数，给出了用它运行`bash`命令的 3 个例子，并以最初激发这篇文章的脚本结束。

我个人对`bash`脚本没有太多的用处。当我需要的时候，我肯定会用 Python 来写，如果它适合你的需要，你也应该这样做。

## 进一步阅读

*   [Python 文档](https://docs.python.org/3/library/subprocess.html)