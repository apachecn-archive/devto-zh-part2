# 在 macOS Sierra 上安装 Python3、Pip 和 AWS CLI

> 原文：<https://dev.to/stevoperisic/installing-python3-pip-and-aws-cli-on-macos-sierra-3pea>

在 Mac OS Sierra 上安装 Python v3、Pip 包管理器和 AWS CLI 的快速指南。这篇文章假设您只有预装在 Mac 上的 Python 2.7，并且安装了 Homebrew。如果你需要自制软件，请访问[https://brew.sh/](https://brew.sh/)并获得它，然后回到这里阅读指南的其余部分。

首先让我们通过运行来安装 Python 3(如果你安装了 Homebrew，它可能会在你调用 install 命令时更新，就像我一样。您可能需要重新运行下面的安装命令。):

T2`~ brew install python3`

至此，您已经有了系统 Python 2.7 和 Python 3 的自制版本。分别使用`~ python`和`~ python3`命令启动系统 Python 2.7 和 Pyhton 3。

您将能够使用`~ pip3`命令启动 Pip 3。

现在让我们安装 AWS CLI。

T2`~ pip3 install awscli --upgrade --user`

使用以下命令验证安装:

T2`~ aws --version`

在我的例子中，我必须将 Python 3 的位置添加到$PATH 变量中，您可能也必须这样做。方法如下:

T2`~ export PATH=~/Library/Python/3.6/bin:$PATH`

再次检查 aws 安装:

T2`~ aws --version
aws-cli/1.11.185 Python/3.6.3 Darwin/16.7.0 botocore/1.7.43`

现在你应该都准备好了:)

您必须在 AWS CLI 中设置凭证，您可以从 Amazon dashboard 中您的用户名下的“我的安全凭证”菜单链接中获取这些值。

—

以防你需要它们，这里有一些参考资料:

[在 Mac OS X 上安装 Python 3——Python 的搭便车指南](docs.python-guide.org)

[在 macOS - AWS 命令行接口上安装 AWS 命令行接口](docs.aws.amazon.com)