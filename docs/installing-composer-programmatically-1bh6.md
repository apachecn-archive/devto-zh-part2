# 以编程方式安装编写器

> 原文：<https://dev.to/czarpino/installing-composer-programmatically-1bh6>

我发现人们通常不正确地设置编程式 Composer 安装是非常令人担忧的。我觉得既然 Composer 的下载页面用脚本描述了下载过程，人们就把它当成了程序性的安装方式。当然，这是一个不正确的假设，可以从示例脚本中使用的硬编码安装程序 SHA 中推断出来。

在最近处理了由过时的安装程序哈希导致的构建失败后，我想正确地解决这个问题，而不是简单地将它一脚踢开。

### 更新安装程序哈希

在谈论正确的解决方案之前，我想强调一下不正确但快速的解决方案:更新安装程序哈希。如果您迫切需要完成某项工作，并且希望单独解决安装程序问题，那么简单地更新安装程序 SHA 散列在短期内是有帮助的。

```
# Replace "93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8"
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');" 
```

如果您访问 [Composer 的下载页面](https://getcomposer.org/download/)，您将很快看到示例脚本中的最新散列。或者，您也可以通过命令行进行检查:

```
# via wget
wget -q -O - https://composer.github.io/installer.sig

# or via cURL
curl https://composer.github.io/installer.sig 
```

### 程序化安装

以编程方式安装 Composer 的正确方法是*而不是*依赖于硬编码散列。仔细看，Composer 下载文档实际上链接到了安装的[编程方式。](https://getcomposer.org/doc/faqs/how-to-install-composer-programmatically.md)

```
#!/bin/sh

EXPECTED_SIGNATURE="$(wget -q -O - https://composer.github.io/installer.sig)"
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
ACTUAL_SIGNATURE="$(php -r "echo hash_file('SHA384', 'composer-setup.php');")"

if [ "$EXPECTED_SIGNATURE" != "$ACTUAL_SIGNATURE" ]
then
    >&2 echo 'ERROR: Invalid installer signature'
    rm composer-setup.php
    exit 1
fi php composer-setup.php --quiet
RESULT=$?
rm composer-setup.php
exit $RESULT 
```

该脚本下载安装程序，验证哈希，然后安装 Composer。它根据从[https://composer.github.io/installer.sig](https://composer.github.io/installer.sig)获得的最新散列来检查安装程序 SHA，该散列的值在发布新版本时会自动更新。这样，在脚本中就不会使用硬编码的散列，您可以自动获得最新版本的 Composer。

现在，您已经安装了面向未来的编程式 Composer！

*最初发布于[https://plog . czar Pino . com/install-composer-programmably/](https://plog.czarpino.com/install-composer-programmatically/)T3】*