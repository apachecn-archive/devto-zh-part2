# Git 的一些关键概念

> 原文：<https://dev.to/franiglesias/some-key-concepts-on-git-3e>

解释了一些关键概念，以便了解使用该 VCS 时会发生什么。

### 储存库

你存放作品的地方。一开始，它是你电脑上的一个文件夹。当您添加版本控制时，该文件夹就变成了一个存储库，可以跟踪您所做的**变更**。

您可以通过移动到本地存储库并告诉 git 进行初始化来创建本地存储库。

```
mkdir myProject
cd myProject

git init 
```

Enter fullscreen mode Exit fullscreen mode

稍后将详细介绍。

### 变化

变更是对**存储库**内容的修改。这些可以有三种类型:

*   添加文件
*   文件删除
*   文件的修改

空文件夹不算，直到他们得到一些文件(或丢失所有文件)。

但是改变本身是不够的。你需要告诉 git 盯着他们，追踪文件。你可以这样做:

```
git add <replace this with the files changed> 
```

Enter fullscreen mode Exit fullscreen mode

这将使变化分阶段进行。

下面是一些例子:

```
git add readme.md // Will add the readme.md file

git add src/MyClass  // Will add all changes under folder src/MyClass

git add -all // Will add all the changes detected
git add . // Will add all the changes detected 
```

Enter fullscreen mode Exit fullscreen mode

每当一个文件改变时，它将被认为是*改变了*，但是直到你进行了改变，它才会被考虑进来，以便“记住”它。

事实上，如果您在添加后继续修改具体文件，新内容将不会成为下一次提交的一部分，您必须再次添加它们以注册新的更改。

所以，可以说，变化是暂时的。要修正故事中的某一点，你必须**提交**你所做的修改。

### 提交

一个**提交**是一种在版本系统中注册一组变更的方法。它可以从一个更改到几十个影响几个文件的更改。提交使这些更改永久化。

那么，*提交是存储库*故事中的一个点。它有一个唯一标识的名称或 id，并且可以有一条描述更改含义的消息。

您通过向阶段添加更改来决定*提交什么*。

你通过这样做来决定*何时*提交:

```
git commit -m "Commit message" 
```

Enter fullscreen mode Exit fullscreen mode

提交消息是对您想要注册的更改的人性化的可读描述。建议以命令模式编写，理想情况下，提交的更改应该可以在一个步骤中撤销(即:从历史中删除提交)。

### 遥控

存储库是一个本地文件夹，它包含您的工作，并在您每次提交时注册它的更改。

但是，如果你想和别人分享你的工作呢？或者你想从几台电脑上访问它？

那么你需要的是一个与本地同步的**远程**存储库，这样你在本地所做的更改将会在远程得到反映，你或者其他人可以从远程得到应用到本地的更改。

存储远程库最流行的地方是 Github，但这不是唯一的地方。事实上，你可以在[本地网络](https://git-scm.com/book/it/v2/Git-on-the-Server-Setting-Up-the-Server)上分享你的回购。

你可以从命令行操作系统设置一个项目到 github，只需进入 [Github](http://github.com) 页面。

一旦您的本地存储库和远程存储库同步，您就可以共享并获得更改。

通常，默认情况下，remote 被命名为 **origin** 。

提交后，您可以使用 push 与远程设备共享更改。

```
git push origin 
```

Enter fullscreen mode Exit fullscreen mode

如果您想要用最新发布的变更来更新您的本地存储库，您可以提取它们。

```
git pull 
```

Enter fullscreen mode Exit fullscreen mode

### 主人

连续的提交是你的项目的历史。从一开始，这段历史就发生在一个叫做**主**的分支中。每次执行 commit 时，历史中的一个新的点会被设置一个 id 来命名。

如果你想或需要，你可以倒回到历史的某一点。指向当前提交或历史中的点的指针称为 HEAD。

但是你也不必把自己局限在只有一个**分支**。

### 树枝

很多时候，你会想探索一些事情，或者在你的项目中工作，而不去碰你已经完成的工作，直到那一刻。

在这种情况下，最好创建一个**分支**。

一根树枝是对师父所反映的历史进程的偏离。主分支和新分支可以从同一提交开始不同地发展。在将来的某个时候，你可以再次合并它们，但是现在，你可以在不影响其他分支的情况下改变其中一个分支的内容。

假设您有一系列这样的提交:

```
m1---m2---m3--m4 
```

Enter fullscreen mode Exit fullscreen mode

您决定在 m4 提交时开始一个新的分支，这样您就可以创建一个新的特性，而不会影响或修改 master 中的当前代码。

```
m1--m2--m3--m4
             |
            m4 
```

Enter fullscreen mode Exit fullscreen mode

然后，您将更改添加到这个分支:

```
m1--m2--m3--m4
             |
            m4--b1--b2--b3 
```

Enter fullscreen mode Exit fullscreen mode

如果您没有在 master 中执行任何更改，您可以很容易地将 b 分支合并到 master 中，将 b*提交应用到 master 中。

但是有可能在 master 分支工作，在不影响 b 分支工作的情况下再做一个改动。

```
m1--m2--m3--m4--m5--m6
             |
            m4--b1--b2--b3 
```

Enter fullscreen mode Exit fullscreen mode

问题随后就来了，当你想把 b 分支合并成 master 的时候。也许有些变化会以不同的方式产生影响，所以我们可能会发现**冲突**。

### 冲突

当不同的更改应用于相同的对象时，会出现冲突。

考虑前面的例子:

```
m1--m2--m3--m4--m5--m6
             |
            m4--b1--b2--b3 
```

Enter fullscreen mode Exit fullscreen mode

假设提交 **m5** 改变一个名为【User.php】T2 的文件，提交 **b2** 改变同一个文件。如果影响到相同的代码行，这些更改可能会发生冲突。在 b 部门工作时，你不知道那些变化已经发生，所以你没有考虑到它们。

当您想要将您自己的更改应用到主分支时，git 发现它无法合并文件 User.php 中的更改，因为它不知道哪一个应该优先。

有时，git 可以应用来自两个分支的更改，因为它们影响到文件的不同部分。我们说它们不是相互冲突的变化。其余时间，git 需要您的帮助来决定如何执行合并，因此它会通知您在继续之前必须解决冲突。

解决冲突意味着您创建了一组新的更改，定义了新版本的代码，其中您选择了更适合您的程序需求的代码，甚至完全重写了代码。之后，您通过添加 de 变更并照常提交，将冲突标记为已解决。

### 预防冲突

在有几个开发人员处理完全相同的文件的环境中，可能会发生这样的情况，当您在一个分支中工作的同时，主分支也在发展，而其他开发人员正在将变更合并到主分支中。因此，master 中的一些具体变化很容易潜在地影响您在自己的分支中修改的文件。

防止这种情况的一种方法是经常将主文件合并到您的分支中，这样当您继续工作时，代码就可以保持最新的变化。

#### 合并

```
m1--m2--m3--m4--m5--m6
             |
            m4--b1--b2--b3 
```

Enter fullscreen mode Exit fullscreen mode

执行合并

```
git checkout branch
git merge master (or origin master)

>>> Conflict 
```

Enter fullscreen mode Exit fullscreen mode

解决冲突，添加更改并提交:

```
git add .
git commit -m "Conflict between master and branch resolved..." 
```

Enter fullscreen mode Exit fullscreen mode

将分支合并到主节点

```
git checkout master
git merge branch 
```

Enter fullscreen mode Exit fullscreen mode

如果没有对 master 应用其他更改，您就应该完成了。

```
m1--m2--m3--m4------------m5--m6
             |           /
            m4--b1--b2--b3 
```

Enter fullscreen mode Exit fullscreen mode

#### Rebase

Rebase 是一种重建分支历史的方法，它将头倒回到与 master 的最后一次共同提交(历史中创建分支的点)，然后应用 master 中的所有更改，最后应用分支中的更改。

如果在此过程中出现冲突，您可以逐个解决它们，并获得一个干净的分支，同时应用来自主分支的更改。这样，如果你想把你的修改合并到母版中，你将不会发现任何冲突。所有的冲突管理都发生在您的分支机构的环境中。

```
m1--m2--m3--m4--m5--m6
             |
            m4--b1--b2--b3 
```

Enter fullscreen mode Exit fullscreen mode

执行重置基础

```
git checkout branch
git pull --rebase master

>>> Conflict 
```

Enter fullscreen mode Exit fullscreen mode

进行所需的更改并添加它们。

```
git add . 
```

Enter fullscreen mode Exit fullscreen mode

然后，继续重置基础，直到没有冲突，过程结束。

```
git rebase --continue 
```

Enter fullscreen mode Exit fullscreen mode

如果在应用提交后没有要添加的文件，请使用- skip 而不是- continue:

```
git rebase --skip 
```

Enter fullscreen mode Exit fullscreen mode

重置基础后，您可以检出主并合并另一个分支

```
git checkout master
git merge branch 
```

Enter fullscreen mode Exit fullscreen mode

```
m1--m2--m3--m4--m5--m6--b1--b2--b3 
```

Enter fullscreen mode Exit fullscreen mode