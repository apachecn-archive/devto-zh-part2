# 走出你的壳，学习巴什

> 原文：<https://dev.to/dmgarland/come-out-of-your-shell-and-learn-bash-4cci>

每当我在[We get Coders](https://www.wegotcoders.com)开始一个新的队列时，我注意到的一件事是，尽管我尽了最大努力，但学生们往往不知道如何使用 shell。笔记本电脑越闪亮，就越不愿意放开 GUI，把手放在键盘上。

我会整理一份小抄。然而，我想不出还有什么技术比 Bash(也许是 SQL？).这将是一个无聊的第一天:让我们学习如何建立一个目录，然后再删除它！尤其是当最终目标是学习 web 开发，或者找一份程序员的工作时；为什么要复习这些基本的命令行命令呢？

有一些概念是你学编程的时候假设的。以文件系统为例。对于新手来说，什么是路径、相对路径如何工作或者如何在树的概念中导航并不明显。这并不难——但这足以阻止新来者错误地认为他们面对的是“聪明”的人，也足以分散我们在课堂上可能涉及的实际高层次概念的注意力。

问题是，我们拖得越久，壳就越会成为绊脚石。在最初的几个星期里，我用 Ruby 和面向对象编程的新概念轰炸他们，他们会与他们的操作系统搏斗，使用文本编辑器重命名文件和创建目录，或者使用 finder 删除文件夹并丢失他们的工作。一切都变得更慢，所有这些额外的努力都是从学习编码中带走的。

虽然关于 bash 编程还有很多东西要学，但是除非你打算成为 Debian 包的维护者，我发现在过去的 15 年里，我一直坚持使用 Bash 命令的一个相对较小的子集，并在上面编写一些奇怪的脚本。我从来不听那些其他贝壳的支持者，zsh 会如何改变我的人生之类的。我觉得我已经学到了足够多的东西，这使我在工作中比我从来不学的时候做得更好。

最终，我们中的许多人将 shell 编程作为其他事情的子任务来学习，这是一个必须克服的障碍。一旦我们学会了 Ruby、JavaScript、Go 或我们关注的任何东西，我们就不会在 bash 脚本上花太多时间。但是对于自动化重复任务、转换文件、创建初始项目目录结构、导航文件系统以及找到我们放置代码的位置，了解 shell 的基础知识对于任何严肃的程序员来说都是至关重要的技能。

我决定为初学者准备一个[的简短课程，涵盖一些基本的 Bash 概念。我试图专注于我多年来所依赖的知识子集，而不是背诵一本圣经。我想做一个必要的指南——可以支撑任何在线课程、训练营或培训项目的指南。这些内容可以免费使用和下载——如果你遇到任何问题，可以请求我的帮助。](https://www.dangarland.co.uk/courses/introduction-to-shell.html)

为了让你有个感觉，这是我们在课程中要做的一部分练习。

## 在 Bash 中生成 HTML 项目框架

Bash 的一个优点是，它不需要任何其他依赖项来运行，它通常与 UNIX 机器一起提供。因此，对于创建新项目的代码来说，这是一个很好的选择，因为我们可以直接运行它，而无需任何进一步的设置。

假设我们想要实现一个适合 web 开发的目录结构。我们可能希望为我们不同的资产保存单独的文件夹，以及一个 starter HTML 文件。首先，我的目标是这样的:

```
site/
├── css
├── images
├── index.html
└── javascripts 
```

使用之前的命令，我们可以使用`mkdir`和`touch`轻松实现这一点。您可以通过将这些片段放到您的 shell 中来尝试它们，或者您可能想要创建一个脚本(对于这个例子，我假设您已经创建了一个`new_html_project`脚本并使其可执行)。

```
mkdir -p site/images
mkdir -p site/javascripts
mkdir -p site/css
touch site/index.html 
```

但是有点重复。一旦我们的文件列表变长，我们可能希望避免多次编写`mkdir`命令。这就是`for`循环的用武之地。循环中的任何代码都是重复的，因此代码可以用不同的输入运行多次。让我们把这些卷成一个循环，看看它是否节省任何重复:

```
for folder in "site/css" "site/images" "site/javascripts"
do mkdir -p $folder
done 
```

这里，我们使用一个 bash 变量(前面有美元符号的位)来连续存储“site/css”、“site/images”、“site/JavaScript”的值，并使用该值作为`mkdir`的参数。

至于索引页面，我们需要使用不同的方法。您可以使用`touch`创建一个空白文件，或者甚至使用`echo`通过重定向添加一些内容:

`echo "<h1>My amazing webpage</h1>" > index.html`

因为我们通常需要一些内容，所以我们可以考虑使用 here 文档构建一个更大的字符串，并在将输出重定向到索引文件之前，将其用作`cat`的输入。here 文档使用任意分隔符(这里是 HTML)来表示字符串的开始(< < HTML)和结束(HTML 在它自己的行上)。

```
cat > site/index.html <<HTML <!DOCTYPE HTML>
<html>
  <head>
    My Amazing HTML document
  </head>
  <body>
    <h1>My Amazing HTML document</h1>
  </body>
</html> HTML 
```

到目前为止，我们已经得到了一个类似这样的脚本:

```
for folder in "site/css" "site/images" "site/javascripts"
do mkdir -p $folder
done cat > site/index.html <<HTML <!DOCTYPE HTML>
<html>
  <head>
    My Amazing HTML document
  </head>
  <body>
    <h1>My Amazing HTML document</h1>
  </body>
</html> HTML 
```

到目前为止，我们偶然发现了一个很好的巧合，我们选择的命令甚至在后续使用中也能工作——也就是说，即使我们运行脚本两次。然而，我们可能不想继续运行命令来设置 index.html，否则如果我们重新运行脚本，我们有可能会丢失我们的工作。幸运的是，对于`if`语句来说，这是一个很好的用例，可以在创建文件之前检查文件是否已经存在。让我们更新我们的脚本来使用 if 语句，使用`-f`标志来确定文件是否存在。

```
for folder in "site/css" "site/images" "site/javascripts"
do mkdir -p $folder
done
if [ ! -f site/index.html ]
then cat > site/index.html <<HTML <!DOCTYPE HTML>
<html>
  <head>
    My Amazing HTML document
  </head>
  <body>
    <h1>My Amazing HTML document</h1>
  </body>
</html> HTML fi 
```

最后，我们假设我们总是想要创建一个`site`文件夹。如果我们可以将它参数化，允许我们命名有问题的子文件夹，那么这个脚本会更加有用。Bash 脚本接受参数，这些参数在命令行上用空格分隔。每个参数由美元符号和列表中要使用的参数的数字索引分隔。如果您尝试在您的 shell 中键入以下内容，您会看到$1 变成 hello，而$2 变成那里:

T2`echo $1 $2 hello there`

参数是零索引的，这意味着还有一个参数`$0`，它表示您调用的程序，在本例中是`echo`。

因此，我们可以用它来使我们的`site`文件夹更加动态:

```
#!/bin/bash
for folder in "$1/css" "$1/images" "$1/javascripts"
do mkdir -p $folder
done
if [ ! -f $1/index.html ]
then cat > $1/index.html <<HTML <!DOCTYPE HTML>
<html>
  <head>
    My Amazing HTML document
  </head>
  <body>
    <h1>My Amazing HTML document</h1>
  </body>
</html> HTML fi 
```

我们现在可以使用`./new_html_project my_amazing_webpage`生成多个项目，替换每个文件夹的参数。但是有一个 bug！如果我们试图调用这个程序，但是忘记了传入一个参数，那么`$1`的值将为空，剩下一个 shell 脚本，理论上看起来像这样:

```
#!/bin/bash
for folder in "/css" "/images" "/javascripts"
do mkdir -p $folder
done
if [ ! -f /index.html ]
then cat > /index.html <<HTML <!DOCTYPE HTML>
<html>
  <head>
    My Amazing HTML document
  </head>
  <body>
    <h1>My Amazing HTML document</h1>
  </body>
</html> HTML fi 
```

您可能已经注意到，如果没有我们的动态站点文件夹名称，我们现在引用以`/`开头的路径，这意味着整个文件系统的根。这个文件夹是保护普通用户的(部分原因！)，所以除非您有 root 权限，否则您可能会看到权限错误。

为了解决这个问题，我们可以使用`$#`操作符来提供给定参数的数量，如果我们弄错了，就显示一条消息。假设程序名算作一个参数，我们希望结果正好是两个。

```
#!/bin/bash
if [ ! $# == 2 ]; then echo "Usage: $0 <folder_name>"
  exit fi

for folder in "$1/css" "$1/images" "$1/javascripts"
do mkdir -p $folder
done

if [ ! -f $1/index.html ]
then cat > $1/index.html <<HTML <!DOCTYPE HTML>
<html>
  <head>
    My Amazing HTML document
  </head>

  <body>
    <h1>My Amazing HTML document</h1>
  </body>
</html> HTML fi 
```

我认为这个例子包含了足够的概念，可以在其他语言中进一步学习。在本课程中，我们将查看更多基本 bash 命令的示例，我将留给您一个练习来构建一个目录结构，以支持 bootcamp 教学大纲。

我欢迎你的任何反馈，无论是初学者还是专业人士，来进一步发展这个课程。如果对它有热情，我可以更进一步，探索更多的概念，像如何列出和删除进程，一些管理，后台任务，网络等等；但是现在，我认为对于任何想参加培训课程或训练营的人来说，这已经足够好了，可以帮助他们做好准备并渴望出发。报名链接是[这里是](https://www.dangarland.co.uk/courses/introduction-to-shell/lessons/command-line-basics)。