# 在 Arch/Manjaro Linux 上为 GCP 设置 Google Cloud SDK

> 原文：<https://dev.to/nabbisen/setting-up-google-cloud-sdk-of-gcp-on-archmanjaro-linux-19mk>

## [T1】简介](#intro)

基于 [Arch Linux](https://www.archlinux.org/) 的 Manjaro Linux ，是我最喜欢的开发用 Linux 发行版。
这是因为它的滚动发布和 [AUR【拱形用户库】](https://aur.archlinux.org/)——一个相当大的库和有用的包。

[GCP【谷歌云平台】](https://cloud.google.com/)也是云计算的奇葩。
它给了创业公司服务力和成本有效性。

## 总结

为了从命令行管理 GCP，Google 提供了 [Google Cloud SDK](https://cloud.google.com/sdk/) 。

本文展示了如何在 Arch/Manjaro Linux 上设置 GCP 管理环境。

* *注【Linux 官方包在 [Debian/Ubuntu](https://cloud.google.com/sdk/docs/quickstart-debian-ubuntu) 和[red hat/CentOS(Fedora 除外)](https://cloud.google.com/sdk/docs/quickstart-redhat-centos)上有售。*

<center>✿ ✿ ✿</center>

## 第一步:账户激活

首先要有谷歌云账号。
[可免费试用。](https://console.cloud.google.com/freetrial/)
其免费服务列在[这里](https://cloud.google.com/pricing/free/)。

* *注意*:此时我们可以/不必创建项目。

## 第二步:google-cloud-sdk 安装

接下来，我们需要安装“google-cloud-sdk”包。
AUR 套餐可供选择。

* *注*:或者我们可以按照[Google Cloud SDK for Linux 官方快速入门教程](https://cloud.google.com/sdk/docs/quickstart-linux)选择手动安装。工具版本可能以这种方式修复。

打开终端，输入如下:

```
$ git clone https://aur.archlinux.org/google-cloud-sdk.git

$ cd google-cloud-sdk/
$ # pkgbuild is necessary
$ makepkg -si
$ # authenticate for installation if required

$ # remove resource after installation
$ cd ../
$ rm -rf google-cloud-sdk/ 
```

## 第三步:初始化

在这里，只需键入`gcloud init`,终端上的以下几个步骤将带我们完成😉

像这样:

```
$ gcloud init

Welcome! This command will take you through the configuration of gcloud.

Your current configuration has been set to: [default]

You can skip diagnostics next time by using the following flag:
  gcloud init --skip-diagnostics

Network diagnostic detects and fixes local network connection issues.
Checking network connection...done.
The Reachability Check passed.
Network diagnostic (1/1 checks) passed.

You must log in to continue. Would you like to log in (Y/n)?  

...

# authorization via web browser will be required

...

You are logged in as: [{% your@account %}].

Pick cloud project to use: 
 [1] {% your-project if exists %}
 [2] Create a new project
$ Please enter numeric choice or text value (must exactly match list 
item):  1 # or you can create a new project by selecting "2"

Your current project has been set to: [{% your-existing-project %}].

$ Do you want to configure a default Compute Region and Zone? (Y/n)?  n

Created a default .boto configuration file at [/home/{% your-name %}/.boto]. See this file and
[https://cloud.google.com/storage/docs/gsutil/commands/config] for more
information about configuring Google Cloud Storage.
Your Google Cloud SDK is configured and ready to use!

* Commands that require authentication will use {% your@account %} by default
* Commands will reference project `{% your-project %}` by default
Run `gcloud help config` to learn how to change individual settings

This gcloud configuration is called [default]. You can create additional configurations if you work with multiple accounts and/or projects.
Run `gcloud topic configurations` to learn more.

Some things to try next:

* Run `gcloud --help` to see the Cloud Platform services you can interact with. And run `gcloud help COMMAND` to get help on any gcloud command.
* Run `gcloud topic -h` to learn about advanced features of the SDK like arg files and output formatting 
```

<center>✿ ✿ ✿</center>

## 结论

现在命令在我们手中😆

#### 基本命令&引用

让我们使用`gcloud`基本命令:

```
$ # show some info
$ gcloud auth list
$ gcloud config list
$ gcloud info

$ # show help
$ gcloud help 
```

`gcloud`这里引用的是。

然后，让我们继续进行`gsutil` :

```
$ # show some info
$ gsutil ls

$ # show help
$ gsutil help 
```

`gsutil`这里引用的是。

#### 用法举例#1

最简单的命令示例之一可能是管理 vm 实例。

```
$ gcloud compute instances start {% instanc-name %}
$ gcloud compute instances stop {% instanc-name %} 
```

它们的细节在这里:[启动](https://cloud.google.com/sdk/gcloud/reference/compute/instances/start/)和[停止](https://cloud.google.com/sdk/gcloud/reference/compute/instances/stop/)。

#### 用法举例 2

现在我们可以管理项目对象了。
例如，在创建自定义虚拟机映像时，这些命令非常有用:

```
$ # to add resource
$ gsutil cp -a public-read {% local-image.tar.gz %} gs://{% bucket-name %}/{% server-image %}.tar.gz
$ # to remove resource
$ gsutil rm gs://{% bucket-name %}/{% server-image %}.tar.gz

$ # to create vm image
$ gcloud compute --project {% project-name %} images create {% vm-name %} --source-uri gs://{% bucket-name %}/{% server-image %}.tar.gz
$ # to delete vm image
$ gcloud compute --project {% project-name %} images delete {% vm-name %} 
```

他们的详细信息在这里: [gsutil cp](https://cloud.google.com/storage/docs/gsutil/commands/cp) 、 [gsutil rm](https://cloud.google.com/storage/docs/gsutil/commands/rm) 和 [gcloud compute](https://cloud.google.com/sdk/gcloud/reference/compute/) 。

还有一个 [gsutil 快速入门](https://cloud.google.com/storage/docs/quickstart-gsutil/)。

<center>✿ ✿ ✿</center>

非常感谢您的阅读。
快乐编码😃