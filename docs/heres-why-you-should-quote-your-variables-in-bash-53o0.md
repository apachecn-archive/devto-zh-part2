# 这就是为什么应该在 Bash 中引用变量的原因

> 原文：<https://dev.to/nickjj/heres-why-you-should-quote-your-variables-in-bash-53o0>

**本文最初发布于 2018 年 10 月 2 日:[https://nickjanetakis . com/blog/here-is-why-you-should-quote-your-variables-in-bash](https://nickjanetakis.com/blog/here-is-why-you-should-quote-your-variables-in-bash)**

* * *

在准备完成下一门课程时，我正在完成一个使用 acme.sh 发布 SSL 证书的 [Ansible 角色。](https://github.com/nickjj/ansible-acme-sh)

该角色支持使用 Let's Encrypt 的 v2 API 颁发单域、多域和通配符证书。所有这些都由 [acme.sh](https://github.com/Neilpang/acme.sh) 处理，但是我做了一个判断调用，在磁盘上创建证书的文件名，以匹配传递给 acme.sh 的第一个证书。

用简单的术语来说，它看起来像这样:

```
acme_sh_domain:
  - domains: ["*.example.com", "example.com"] 
```

当一切运行完毕，你将得到`*.example.com.key`和`*.example.com.pem`证书，用于 nginx 或任何读取你证书的东西。

**我通常不会遇到以`*`开头的文件，所以这会带来一些乐趣**。我坚持不懈地引用变量，所以我没有陷入太多的麻烦，但是当我在终端上玩的时候，在测试角色的时候，我忘记引用一次`mv`命令，现在我们在这里。

## 论证问题

如果您有一个类似 Unix 的环境来测试这一点(MacOS、Linux 或 [WSL](https://nickjanetakis.com/blog/using-wsl-and-mobaxterm-to-create-a-linux-dev-environment-on-windows) 应该可以工作)，那么请随意跟随:

##### 创建目录的顺序很重要:

```
mkdir -p /tmp/demoproblem
cd /tmp/demoproblem

mkdir test.example.com
mkdir *.example.com
# mkdir: cannot create directory 'test.example.com': File exists

rm -rf *

mkdir *.example.com
mkdir test.example.com

ls -la
# drwxr-xr-x 1 nick nick 4096 Sep 29 15:15 *.example.com
# drwxr-xr-x 1 nick nick 4096 Sep 29 15:15 test.example.com 
```

在 WSL 上，`ls`命令去掉了`*.example.com`周围的单引号。在一个真正的类 Unix 系统上，你最终会看到`'*.example.com'`，但这只是与`ls`在表现上的不同。

##### 意外删除意外目录:

```
rm -rf *.example.com

ls -la
# drwxr-xr-x 1 nick nick 4096 Sep 29 15:19 .
# drwxrwxrwt 1 root root 4096 Sep 29 14:59 .. 
```

如果你只想删除`*.example.com`(单个文件夹)，那么你就陷入了痛苦的世界，因为它删除了所有符合模式的目录。

##### 防止删除匹配星号的所有内容:

```
mkdir *.example.com test.example.com

rm -rf "*.example.com"

ls -la
drwxr-xr-x 1 nick nick 4096 Sep 29 15:20 .
drwxrwxrwt 1 root root 4096 Sep 29 14:59 ..
drwxr-xr-x 1 nick nick 4096 Sep 29 15:20 test.example.com 
```

我们开始吧，引用一切按预期工作。你也可以用`rm -rf \*.example.com`来代替星号。

##### 再次检查以确保它也能与 mkdir 一起工作:

```
mkdir "*.example.com"

ls -la
drwxr-xr-x 1 nick nick 4096 Sep 29 15:23 *.example.com
drwxr-xr-x 1 nick nick 4096 Sep 29 15:23 test.example.com 
```

正如我们所见，引用还让我们创建了`*.example.com`,即使`test.example.com`已经存在。

## 那么为什么要引用你的变量呢？

想象一下，如果这个目录名在一个变量中，并且没有加引号，那么您运行一个脚本来删除一个证书。您将删除该证书以及该目录中的所有其他证书。

这可能会影响多个项目，并且如果您以前从未颁发过通配符证书，它可能会很容易地悄悄进入生产环境而不被发现，因为您在其他 20 次颁发证书时都运行良好，所以您认为自己是清白的。

##### 下面是一个由于缺少引号而无法正常运行的脚本示例:

```
DOMAIN_DIR=*.example.com
[ -d $DOMAIN_DIR ] && echo "Directory found" || echo "Directory not found"
# bash: [: *.example.com: binary operator expected
# Directory not found 
```

这是多么疯狂。此时我们的目录中已经有了`*.example.com`和
T1，但是现在运行`mkdir aaa.example.com`来
创建第三个目录。

```
DOMAIN_DIR=*.example.com
[ -d $DOMAIN_DIR ] && echo "Directory found" || echo "Directory not found"
# bash: [: too many arguments
# Directory not found 
```

现在我们得到了一个不同的错误。

##### 这里是相同的设置，但在赋值时引用 DOMAIN_DIR:

```
DOMAIN_DIR="*.example.com"
[ -d $DOMAIN_DIR ] && echo "Directory found" || echo "Directory not found"
# bash: [: too many arguments
# Directory not found 
```

这对我们还是没有帮助。

##### 这是相同的脚本，但引用的只是变量:

```
DOMAIN_DIR=*.example.com
[ -d "$DOMAIN_DIR" ] && echo "Directory found" || echo "Directory not found"
# Directory found 
```

这很有效，但为了安全起见，我们还是应该引用作业。

##### 现在，让我们引用赋值和变量，并使用美元花体:

```
DOMAIN_DIR="*.example.com"
[ -d "${DOMAIN_DIR}" ] && echo "Directory found" || echo "Directory not found"
# Directory found 
```

当有疑问时，引用你的作业，并在引用变量时使用引号+美元曲线。是的，它更冗长，但是多几个字符比醒来发现一个 cron 作业要好得多，它会在半夜删除服务器上的每个证书，或者在脚本中引起一些其他奇怪的副作用。

##### 不要只使用美元花体(即使是带引号的赋值):

```
DOMAIN_DIR="*.example.com"
[ -d ${DOMAIN_DIR} ] && echo "Directory found" || echo "Directory not found"
# bash: [: too many arguments
# Directory not found 
```

这还不够。

## 利用工具帮助我们记住我们的名言

即使有丰富的经验，你也可能偶尔会忘记使用引号。幸运的是，有工具可以帮助防止这种情况发生。

我强烈推荐你安装 [shellcheck](https://github.com/koalaman/shellcheck) 。这是一个优秀的 shell 脚本林挺工具，可以捕捉大量潜在问题(包括缺少引号)并发出警告。它甚至还有一个 [VSCode 扩展](https://github.com/timonwong/vscode-shellcheck)。

你曾经被与缺少 Bash 引用相关的错误击中过吗？下面让我知道！