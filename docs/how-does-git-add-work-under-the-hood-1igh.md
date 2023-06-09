# “git add”是如何工作的？

> 原文：<https://dev.to/captainsafia/how-does-git-add-work-under-the-hood-1igh>

在过去的几篇博客文章中，我从源代码的角度深入研究了当有人在命令行运行`git commit`时会发生什么。我意识到我可能应该研究一下在提交之前发生了什么。有人在命令行运行`git add`会怎么样？

至此，我已经阅读了足够多的 Git 代码库，知道从哪里开始我的研究。我前往存储所有 Git 命令行可执行文件源代码的目录，在 [builtin/add.c](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/builtin/add.c) 中寻找与`git add`相关的代码。

在这一点上，我感觉自己是这个代码库的专家。我直奔[的定义`cmd_add`函数](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/builtin/add.c#L387)。

这一次，我想尝试用不同的方式构建我的代码阅读过程。我承认以前的代码探索有点无处不在。我从阅读彼此半相关的不同代码块开始。然后，我开始专注于通读与特定问题相关的数据结构，然后通读与之相关的逻辑。

在这篇博文中，我想尝试另一种技巧。我想对我读到的代码进行“简化”。我想把每个代码片段简化成伪代码，解释它的功能。

`cmd_add`函数中的[前几行](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/builtin/add.c#L389-L409)非常简单。它们负责解析传递给`git add`命令的参数，并做出适当的响应。这段代码的很大一部分处理交互和补丁阶段。我不会在这里解释这些是什么，但它们很棒，你应该在这里阅读更多关于它们的[。](https://git-scm.com/book/en/v2/Git-Tools-Interactive-Staging)

因此，`cmd_add`函数中的第一位代码可以用下面的伪代码来描述。

```
if the user provided arguments for interactive staging
    invoke the appropriate interactive staging functions 
```

接下来的几行负责处理可以传递给`git add`函数的不同标志。具体来说，`-A`标志表示应暂存已跟踪和未跟踪的文件，而`-u`标志表示仅应暂存已跟踪的文件。所以接下来几行的伪代码可以写成如下。

```
if the user passed a -A flag
    stage tracked and untracked files
if the user passed a -u flag
    stage only tracked files
if the user passed a -u and -A flag
    freak out because the user should not pass these flags together 
```

我决定把重点放在我在 Git 工作流中最常用的标志上。

下一个有趣的代码块是[下面的几行代码](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/builtin/add.c#L464-L478),主要是准备将文件添加到索引中(也称为暂存区)。在这些代码行中，预期要登台的路径存储在一个临时数据结构中。

```
for each path the user wants to stage
    for each file in path
        if file has untracked changes
            store it in a temporary list 
```

存储在该临时列表中的文件被传递给 [`add_files`](https://github.com/git/git/blob/90bbd502d54fe920356fa9278055dc9c9bfe9a56/builtin/add.c#L364) 函数，该函数负责将每一个具有未跟踪变更的文件实际添加到索引中。这里的 moneymaker 函数是在代码库中的其他地方定义的`add_to_index`函数[。最后，我深入研究了代码库，试图弄清楚当一个未跟踪的更改被添加到索引中时，到底会发生什么。据我所知，这一切都可以追溯到我在](https://github.com/git/git/blob/085f5f95a2723e8f9f4d037c01db5b786355ba49/read-cache.c#L641)[过去的博客文章](https://dev.to/captainsafia/reading-code-late-at-night-and-realizing-that-its-not-a-good-idea-2ai9-temp-slug-9840706)中探索过的 [Git 对象](https://dev.to/captainsafia/whats-inside-the-gitobjects-directory-fg1-temp-slug-524219)。每当将文件添加到索引中时，都会创建一个 blob 对象，并且路径名和 blob 的散列都存储在索引中。

该索引存储在一个二进制文件中，该文件位于由 Git 进行版本控制的存储库中的`.git/index`。这个文件是二进制的，所以删除它不会给你带来什么好处。要读取这个文件，您可以执行以下命令。

```
$ git ls-files --stage
100644 637d210528e696380c88c3beae2a695f574c3c74 0 test.txt 
```

如您所见，它包含文件名、对象散列和对象权限。如果我们检查上面引用的对象，我们可以看到它是一个 blob，包含已暂存文件的内容。

```
$ git cat-file -t 637d210528e696380c88c3beae2a695f574c3c74
blob
$ git cat-file -p 637d210528e696380c88c3beae2a695f574c3c74
Some content. 
```

就这样吧！很明显还有更多细节，但是这给了你一个很好的感觉。它还强化了我在上一篇博文中澄清的关于 Git 如何实现的一般概念。

从本质上讲，文件系统工件持久存储由代码中存储为 C 结构的数据结构所表示的数据。提交文件包括识别文件系统中的变化，并将这些变化映射到 C 通过上述数据结构和文件系统工件识别的表示中。

我敢说，这实际上很有意义，理解/模仿起来也很简单。Git 只是变得不那么可怕了，因为我知道引擎盖下发生了什么。

上面的句子有一种终结感，因为我觉得我已经理解了关于 Git 的大部分有趣的东西。另一个代码库引起了我的兴趣，但我将在下一篇博客文章中深入探讨。在此之前，感谢阅读！