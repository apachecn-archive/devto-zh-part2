# 用 CircleCI 计算素数

> 原文：<https://dev.to/nicklavrov/calculating-prime-numbers-with-circleci-243e>

如果你在寻找一列素数，[你](https://en.wikipedia.org/wiki/List_of_prime_numbers) [别](https://www.mathsisfun.com/numbers/prime-numbers-to-10k.html) [有](http://math.info/Arithmetic/Prime_Numbers_List/) [到](https://primes.utm.edu/lists/small/10000.txt) [看起来](http://compoasso.free.fr/primelistweb/page/prime/liste_online_en.php) [很](http://www.primos.mat.br/indexen.html) [远](http://www.tsm-resources.com/alists/prim.html)。然而，这里有我的小的，完全有用的方法来添加到这个列表中。

我决定最好的方法是在 Github repo 中创建一个持续增长的质数列表。我将使用 CircleCI 来计算下一个质数并更新列表。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [尼克拉夫罗夫](https://github.com/NickLavrov) / [圆周率素数](https://github.com/NickLavrov/circleci-primes)

### 使用 CircleCI 创建一个质数列表

<article class="markdown-body entry-content container-lg" itemprop="text">

# 圆素数

[![CircleCI](img/0c0779cab6ac862c996dd3ccf1f69d45.png)T2】](https://circleci.com/gh/NickLavrov/circleci-primes)

使用 CircleCI 创建一个质数列表！

这个回购保持一个素数的运行列表，在`primes.txt`中找到。对主分支的提交触发 CircleCI 上的构建，计算下一个素数，将其添加到列表中，将新列表提交到主分支，触发 CircleCI 上的构建，计算下一个素数，将其添加到列表中...你明白了。

在此观看构建！

</article>

[View on GitHub](https://github.com/NickLavrov/circleci-primes)

回购的结构非常简单。有一个素数列表，用 2 和 3 初始化；一个名为 nextprime.sh 的脚本，不出所料，它找到了下一个素数；和一个 CircleCI 配置文件，该文件将下一个素数附加到列表中并提交新列表。

下面是有趣的部分 CircleCI 上的构建是由提交到主分支触发的，由于构建向主分支提交了新的素数列表，新的构建将计算下一个素数。在这里实时观看构建！这将会发生，直到我在提交之前取消一个构建，或者直到 CircleCI 的好心人决定适可而止。

有没有更好、更快的方法来做到这一点？当然了。这完全是浪费时间吗？值得商榷。当你把自己局限于某一套工具时，你就被迫想出创造性的解决方案。因此，我学到了一些关于 shell 脚本和 CircleCI 如何处理 SSH 和 Github 通信的新知识。

如果你感兴趣，下面是我面临的一些挑战:

### ·用 shell 脚本做数学运算

我决定使用 shell 脚本有两个主要原因。首先，我想在我的构建中使用小小的 [Docker alpine image](https://hub.docker.com/_/alpine/) 。我不想要任何多余的东西！(尽管你会在下面看到我需要添加`bc`(基本计算器)工具)其次，我喜欢 shell 脚本。是的，我是认真的。

我最初是用`expr`做数学，但是经过反复试验，我意识到`expr`能够处理的数字是有上限的。具体来说，从手册页:

> 算术运算使用带符号整数数学来执行，其范围取决于 C intmax_t 数据类型(可用的最大带符号整数类型)。

所以一旦我开始处理大于 2^63 的数字，就会出现问题。尝试在您的终端中运行以下命令，亲自查看:

`expr 42 + 1`
T1】

因为我完全期望它能计算出超过 2^63 的质数，所以这个极限`expr`是[不能接受的](https://www.youtube.com/watch?v=b-t8JmyCvWg)。

我发现了一个名为`bc`的工具，它没有这个限制，因为它“通过交互式执行语句来支持任意精度的数字。”在我看来，它也更容易使用。在那之后做了一点工作，我的质数生成脚本就完成了。拥有一个质数列表的好处是，在寻找下一个质数时，你有一个很好的除数列表来测试它！

### 使用 CircleCI 推送至 Github repo

用 Circle 从 Github repo 中提取很容易。另一方面，推就有点难了。一种方法是使用您自己的 Github 令牌(当然，作为 env 变量安全地存储在 CircleCI 中！)来推，用这样的东西:

`git push -q https://${GITHUB_TOKEN}@github.com/NickLavrov/circleci-primes.git HEAD`

但我不想走那条路。我想代表 CircleCI 进行推送，而不冒任何暴露我个人 Github 令牌的风险(这一点`-q`非常非常重要)。

因此，我在 Github 上为 repo 创建了一个新的 deploy 密钥，授予它写权限，在 CircleCI 上添加了 SSH 密钥，我认为我已经完成了。不幸的是，当要推的时候，我被击中了:

`/bin/sh: git: not found`

所以我装了`git`。然后我被击中了:

`fatal: You are not currently on a branch.`

所以我把命令改成了`git push origin HEAD:master`，结果被打了:

`fatal: cannot run ssh: No such file or directory`

所以我加了一个安装`openssh`的步骤，被击中了:

`The authenticity of host 'github.com (192.30.253.112)' can't be established.`

这是我发现一个我真的不喜欢的解决方案的地方。我在配置中添加了一个步骤，将 github.com 添加到~/中。ssh/known_hosts 文件如下:

```
echo '
github.com ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAq2A7hRGmdnm9tUDbO9IDSwBK6TbQa+PXYPCPy6rbTrTtw7PHkccKrpp0yVhp5HdEIcKr6pLlVDBfOLX9QUsyCOV0wzfjIJNlGEYsdlLJizHhbn2mUjvSAHQqZETYP81eFzLQNnPHt4EVVUh7VfDESU84KezmD5QlWpXLmvU31/yMf+Se8xhHTvKSCZIFImWwoG6mbUoWf9nzpIoaSjB+weqqUUmpaaasXVal72J+UX2B+2RPW3RcT0eOzQgqlJL3RKrTJvdsjE3JEAvGq3lGHSZXy28G3skua2SmVi/w4yCE6gbODqnTWlg7+wC604ydGXA8VJiS5ap43JXiUFFAaQ==
' >> ~/.ssh/known_hosts 
```

Enter fullscreen mode Exit fullscreen mode

除了 bc、git 和 openssh 之外，我不想安装任何其他工具，所以我坚持使用这个解决方案。如果你能想到一些更优雅的东西，我很想知道！