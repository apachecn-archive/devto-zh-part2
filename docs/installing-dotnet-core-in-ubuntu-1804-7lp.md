# 在 Ubuntu 18.04 中安装 DotNet 核心

> 原文：<https://dev.to/carlos487/installing-dotnet-core-in-ubuntu-1804-7lp>

* * *

# 更新

自从这篇文章发表以来，微软已经发布了安装 DotNet Core 的官方方法，你可以在 [DotNet Ubuntu 18.04 下载](https://www.microsoft.com/net/download/linux-package-manager/ubuntu18-04/sdk-current)中找到它们

* * *

随着 Ubuntu 18.04(流行操作系统的新 LTS 版本)的发布，人们可能会渴望在这个平台上尝试一下 **dotnet** 。

遗憾的是，在写这篇文章的时候，无论是页面获取状态页面[还是下载页面](https://www.microsoft.com/net/learn/get-started/linux)[都没有列出与这个操作系统兼容的包。](https://www.microsoft.com/net/download/linux)

事实上，如果您试图按照最近的版本，即 17.04[Ubuntu 17.04](https://www.microsoft.com/net/download/linux)的说明进行安装，您会得到一个缺少依赖项的错误。

为了进行安装，您必须按照本 [GitHub 问题](https://github.com/dotnet/core/issues/1400)中的说明执行以下步骤。

## 注册微软密钥和订阅源

有必要将密钥和提要添加到系统中，因此您必须在终端中键入以下命令。

```
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF

sudo apt-key adv --keyserver packages.microsoft.com --recv-keys 52E16F86FEE04B979B07E28DB02C46DF417A0893 
```

Enter fullscreen mode Exit fullscreen mode

要添加提要，您需要键入:

```
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-bionic-prod bionic main" > /etc/apt/sources.list.d/dotnetdev.list' 
```

Enter fullscreen mode Exit fullscreen mode

并使用更新包列表

```
sudo apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

## 安装点网核心

要安装 dotnet，您可以将稳定版本与:

```
sudo apt-get install dotnet-sdk-2.1.105 
```

Enter fullscreen mode Exit fullscreen mode

或最新的预览版本:

```
sudo apt install dotnet-sdk-2.1.300-preview2-008533 
```

Enter fullscreen mode Exit fullscreen mode

请记住，您可以同时安装这两个版本，较新的 dotnet 具有**dot net-list-SDK**和 **dotnet - list-runtimes** ，尽管最新的版本如果没有被 **global.json 文件**覆盖，将会被使用。你可以在斯科特·汉瑟曼的帖子中找到更多信息。也

## 测试安装

在文件夹中，使用以下内容创建新的控制台项目:

```
dotnet new console 
```

Enter fullscreen mode Exit fullscreen mode

要运行它，您需要使用:

```
dotnet run 
```

Enter fullscreen mode Exit fullscreen mode

一切正常 qou 将能够在您的终端上看到 Hello World 消息:

```
Hello World! 
```

Enter fullscreen mode Exit fullscreen mode