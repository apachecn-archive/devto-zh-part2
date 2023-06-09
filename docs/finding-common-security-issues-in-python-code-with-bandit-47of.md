# 用 Bandit 查找 Python 代码中的常见安全问题

> 原文：<https://dev.to/alysivji/finding-common-security-issues-in-python-code-with-bandit-47of>

*这篇[帖子](https://alysivji.github.io/find-security-issues-in-python.html)最初发表于 [Siv 脚本](https://alysivji.github.io/)T5*

[Bandit](https://github.com/PyCQA/bandit) 是一个静态分析工具，可以发现 Python 代码中常见的安全问题。命令行实用程序扫描`.py`文件，并生成一份报告，按可信度和严重性详细说明问题。

我在我的几个库上运行了 Bandit，发现我在我的一个副业项目中使用了`md5`来散列密码。这是一个带有`TODO fix`评论的黑客，但是我已经忘记了。感谢 Bandit，我已经把我的密码哈希算法改成了`bcrypt2`。当我在研究[如何加盐和散列密码](https://crackstation.net/hashing-security.htm)时，也了解了[密码库](https://passlib.readthedocs.io/en/stable/lib/passlib.hash.bcrypt.html)。

Rackspace 博客上有一篇关于[开始使用 Bandit](https://developer.rackspace.com/blog/getting-started-with-bandit/) 的文章，我推荐你去看看。