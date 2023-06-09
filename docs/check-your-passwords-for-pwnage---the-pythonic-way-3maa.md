# 用 Pythonic 的方式检查你的密码

> 原文：<https://dev.to/nikoheikkila/check-your-passwords-for-pwnage---the-pythonic-way-3maa>

作为软件开发人员，我们人类很少进行足够的实践。因此，作为一种实践，几天前我决定推出我的第一个公共 Python 包。它叫做 [**pwnedapi**](https://pypi.org/project/pwnedapi/) ，它可以帮助你保持对密码的警觉。

对于那些不熟悉特洛伊·亨特的《T2》的人来说，简而言之，这是一个很棒的 REST 服务，可以搜索你的用户数据是否在一个或多个安全漏洞中被泄露，该服务会持续跟踪这些漏洞。对于最简单的用例，继续在他们的[主页](https://haveibeenpwned.com/)上输入你的电子邮件地址，看看它是否已经被 *pwned* 。

好吧，回到蟒蛇的巢穴。我创建的包(版本 0.3.0)有两个主要的实现。

**a)** 使用 API 版本 2 范围搜索和*k-匿名*模型检查单个密码是否被 pwned】

```
>>> from pwnedapi import Password
>>> password = Password("mysupersecretpassword")
>>>
>>> if password.is_pwned():
...     print(f"Your password has been pwned {password.pwned_count} times.")
...
Your password has been pwned 2 times.
>>> 
```

Enter fullscreen mode Exit fullscreen mode

**b)** 扫描一个密码列表，并以**肯尼斯·雷茨的**精巧的[T5】tablibT7】库提供的任何格式报告它们的泄露计数:](http://docs.python-tablib.org/en/master/) 

```
>>> from pwnedapi import Scanner
>>> scanner = Scanner()
>>> scanner.scan("passwords.txt")
>>> scanner.export_as("leaked.json")
>>> open("leaked.json").read()
'[{"Password": "dog", "Leak Count": 28348}, {"Password": "cat", "Leak Count": 26354}, {"Password": "somepass", "Leak Count": 657}]' 
```

Enter fullscreen mode Exit fullscreen mode

这个实现的灵感来自于这篇出色的文章中提到的 Phil Nash 的 Ruby 实现。

[![philnash image](img/b650ec198faa986f9f11da3a62066a49.png)](/philnash) [## 使用 Pwned 密码 API 在 Ruby 应用程序中使用更好的密码

### 菲尔纳什 4 月 4 日 186 分钟阅读

#ruby #rails #passwords #security](/twilio/better-passwords-in-ruby-applications-with-the-pwned-passwords-api-4o9f)

很容易使用这个包来满足标准库的需求，或者创建一个 CLI 工具来进行系统管理——事实上，我在工作中创建了一个。

正如在包 *README* 和这篇文章开头所提到的，这是我的第一个公共包。热烈欢迎拉请求和反馈。

[**下载**](https://pypi.org/project/pwnedapi/) 吧或者 [**叉**](https://github.com/nikoheikkila/pwnedapi) 吧。