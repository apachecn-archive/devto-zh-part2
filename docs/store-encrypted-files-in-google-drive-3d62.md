# 将加密文件存储在 Google Drive 中

> 原文：<https://dev.to/petarov/store-encrypted-files-in-google-drive-3d62>

> [![XKCD Opinions on Privacy](img/0b281f9d830d32bc0d314c95e112063b.png)T2】](https://www.xkcd.com/1269/)

# 目录

*   [简介](#intro)
*   [目标](#goal)
*   [要求](#requirements)
    *   [生成 GnuPG 密钥](#setup-gpg-keys)
    *   [管理 GnuPG 密钥](#manage-keys)
    *   [设置同步文件夹](#setup-sync)
*   [用途](#usage)
    *   [Linux 和 macOS](#usage-linux-mac)
    *   [窗户](#usage-windows)
    *   [解密文件](#usage-decrypt)
*   [结论](#conclusion)
*   [附件-元数据](#annex)
*   [参考文献](#refs)

# 简介

到现在为止，我估计你已经知道 Google Drive 免费提供惊人的 15 GB 存储空间。微软的 OneDrive 提供大约 5 GB 的免费 T2 和 Dropbox，该领域的先驱之一，提供大约 2 GB 的 T4。Dropbox 有点落后，在这三个领域中，Google Drive 似乎是明显的赢家，因为它可以免费提供更大的存储空间。

*(描述的存储量是从 2018 年 10 月开始的。)*

不用说，免费的在线存储是储存大量重要数据的好方法——文档、备份，也许还有你在 90 年代下载的大量流行音乐 MP3。别那样看着我！说到音乐偏好，我们都犯了罪。现在，如果你和我一样，将敏感文档、数据备份或 SQL 导出文件*保存在云端不受保护*看起来并不舒服。谷歌确实通过使用两步验证过程[【4】](#refs)提供了扩展的安全性，这使得你的账户很难被攻破。然而，我们必须补充的是，安全中的人的因素不能被丢弃。

此外，可以说我多疑，但谷歌服务条款中的以下文字让我感到不安:

> 我们的自动化系统会分析您的内容(包括电子邮件),为您提供与个人相关的产品功能，例如定制的搜索结果、定制的广告以及垃圾邮件和恶意软件检测。这种分析在内容发送、接收和存储时进行。

看到了吗？我的妄想症现在更有理由了。所以问题是，我们能否以加密的方式将我们的数据存储在 Google Drive 上，以及实现自动化的难度有多大。

# 目标

好吧，举个例子——我们的目标是建立一个流程，将服务器或桌面上的文件加密并上传到 Google Drive 文件夹。同步将是**单向的**，这意味着文件将只从服务器或台式机上传到指定的 Google Drive 文件夹。

我们需要一种收集、加密和同步文件的方法。收集文件可以通过 shell 脚本实现，如 Un*x 上的 Bash 或 Windows 上的 Powershell。

为了加密我们*珍贵的*数据，我们将使用 GnuPG[【7】](#refs)，版本 1.4 或 2.1。应该使用你的操作系统的软件包管理器来下载，例如 Aptitute 或 Brew，或者从 [GnuPG 的网站](https://www.gnupg.org/download/index.en.html)下载。

对于同步，有 g Drive[【8】](#refs)，Google Drive CLI Client 的简称。我喜欢这个简短的名字。怎么了 G！它是用 *Go* 编写的，并且是开源的。它带有预建的[二进制文件](https://github.com/prasmussen/gdrive#downloads)，适用于一系列令人印象深刻的平台。

哦，这里有一个我之前画的工作流程图，让这篇文章看起来不那么无聊。看哪！

[![Action Diagram](img/c64dc72559c795cc6566414d5ba6cb7f.png)T2】](http://i.imgur.com/UUweMo1.png)

# 要求

## 生成 GnuPG 密钥

如果您已经有了想要用于加密的 GnuPG 密钥对，您可以完全跳过这一步。但你真的不应该，因为前面有一堆蹩脚的笑话。

现在，我将首先提出一个合理的警告，与 GnuPG 打交道有时可能会有些令人恼火。密码学通常很复杂，不幸的是使用 GPG 可能有点麻烦，但是我们要做的步骤很少，所以应该没问题。深呼吸。准备好了吗？

首先，你需要一个地方来存放你的 GPG 钥匙圈。钥匙圈包含一个或多个公钥或私钥。默认情况下，这将是您的主目录中的一个位置，即`/home/user/.gnupg`。我建议使用另一个单独的目录进行安装。这样，我们只能在那里存储同步密钥。

```
$ cd /var/local
$ mkdir mygpgkeys && chmod 700 mygpgkeys
$ export GNUPGHOME="/var/local/mygpgkeys" 
```

拉下窗帘，戴上墨镜，接下来我们将生成 GnuPG 密钥对。

```
$ cd mygpgkeys
$ gpg --default-new-key-algo rsa4096 --gen-key 
```

默认情况下，gpg 生成 2048 位密钥，所以我们添加了一个额外的参数来启动 4096 位密钥的生成。填写姓名和电子邮件地址。虽然建议使用电子邮件地址，但它不必是真实的。然后应该询问您一个密码来保护您的私钥。不用说，选择一个好的，长的密码。是啊，你的猫的名字不算，除非它是一堆 Lovecraft 的角色名字的串联。生成密钥可能需要一段时间，这取决于系统上可用的熵[【9】](#refs)。

```
gpg (GnuPG) 2.1.21; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: keybox '/var/local/mygpgkeys/pubring.kbx' created
Note: Use "gpg2 --full-generate-key" for a full featured key generation dialog.

GnuPG needs to construct a user ID to identify your key.

Real name: Max Mustermann
Email address: maxmustermann@example.org
You selected this USER-ID:              
    "Max Mustermann <maxmustermann@example.org>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? o
...
gpg: /var/local/mygpgkeys/trustdb.gpg: trustdb created
gpg: key 1A2B04F0E994DAF4 marked as ultimately trusted
gpg: directory '/var/local/mygpgkeys/openpgp-revocs.d' created
gpg: revocation certificate stored as '/var/local/mygpgkeys/openpgp-revocs.d/31208B5D45320FAE3D7E7FE21A2B04F0E994DAF4.rev'
public and secret key created and signed.

pub   rsa2048 2017-05-25 [SC] [expires: 2019-05-25]
        31208B5D45320FAE3D7E7FE21A2B04F0E994DAF4
        31208B5D45320FAE3D7E7FE21A2B04F0E994DAF4
uid                      Max Mustermann <maxmustermann@example.org>
sub   rsa2048 2017-05-25 [E] [expires: 2019-05-25] 
```

GnuPG 将生成一个名为`pubring.gpg`和`secring.gpg`的公钥和私钥环文件。(macOS 上的文件名可能不同！谢谢蒂姆·库克。)您可以通过运行以下命令来查看 GPG 钥匙圈中的可用钥匙:

```
$ gpg --list-keys

-------------------------------------------------------------------
pub   rsa2048 2017-05-25 [SC] [expires: 2019-05-25]
        31208B5D45320FAE3D7E7FE21A2B04F0E994DAF4
uid           [ultimate] Max Mustermann <maxmustermann@example.org>
sub   rsa2048 2017-05-25 [E] [expires: 2019-05-25] 
```

我们拿到了密钥对，接下来让我们登上同步列车。哦，顺便说一句，你现在可以摘下墨镜了。

## 管理 GnuPG 键

这一次我不得不采取大胆的措施，所以我来了- **不要弄丢你的 GPG 钥匙对**！我相信你已经想到了这一点，但是要好好保管你刚刚生成的密钥。如果数据丢失，您将无法解密数据！

一个可怜的解决方案可能是把钥匙备份在一个专用的闪存驱动器上，然后藏在你的枕头下面。如果你不是一个偏执狂，那么你可以把你的钥匙放在一个证书管理器中，比如`Kleopatra`[【10】](#refs)。雷鸟用户只需安装并使用`Enigmail`[【11】](#refs)扩展即可。

## 设置同步文件夹

第一个要求是一个 Google Drive 文件夹，你要把加密文件上传到这个文件夹。请注意，浏览器 url 中给出了文件夹的唯一 id。

[![Sync Folder](img/786e0595511480d54e4a7ad380d275f1.png)T2】](http://i.imgur.com/kDTA2an.png)

接下来，您需要初始化 gdrive 并在硬盘上配置一个 sync 文件夹。

要创建同步文件夹，请运行:

```
$ mkdir mysyncfolder && cd mysyncfolder 
```

要初始化 gdrive，请使用从 Google Drive 获得的文件夹 id 运行以下命令。您需要在浏览器中手动复制并粘贴授权 url，并按照步骤授权 gdrive 代表您上传文件。

```
$ gdrive sync upload . 0BxNabiLkX8lpSGN3UVRmWEVQWWM -c /Users/max/.gdrive-test-sync

Authentication needed
Go to the following url in your browser:
https://accounts.google.com/o/oauth2/auth?access_type=offline&client_id=someverylongstringeg.apps.googleusercontent.com&redirect_uri=...

Enter verification code: 7/TWTDPBq32xYiRqqAlWp4tkcVBYgL3n5qsD_bfzXr8B0
Starting sync...
Collecting local and remote file information...
Found 0 local files and 0 remote files
Sync finished in 2.095311029s 
```

请注意，gdrive 访问令牌参数保存到手动指定的路径中，即保存在`/Users/max/.gdrive-test-sync`中。这通常应该是只允许您的用户访问的位置。chmod 600 有人吗？

# 用法

## Linux 和 macOS

让我们编写一个 Bash 脚本，它将加密硬盘上给定文件夹中的所有文件，然后将它们上传到指定的 Google Drive 文件夹。我们就称之为`sync.sh`，因为我已经没有原创的想法了。

```
$ touch sync.sh && chmod +x sync.sh 
```

在编辑器中打开该文件，并添加以下内容。检查注释，根据需要调整源路径和目标路径。

```
#!/bin/sh

GPG=$(which gpg)
GDRIVE=$(which gdrive)

# checks if the required tools are available 
if [ "XY$GPG" = "XY" ]; then echo "GPG not found!"
    exit 1
fi
if [ "XY$GDRIVE" = "XY" ]; then echo "gdrive not found!"
    exit 2
fi

####################################
## configuraitons
####################################

##### !IMPORTANT! #####
## Make sure to adjust the paths to your system
#######################

# key to encrypt with (the one we generated earlier)
GPG_KEY="maxmustermann@example.org"
export GNUPGHOME="/var/local/mygpgkeys"
# destination folder id in gdrive
GDRIVE_DEST="0BxNabiLkX8lpSGN3UVRmWEVQWWM"
# local sync folder path
GDRIVE_SYNC_DIR="/var/local/mysyncfolder"
# gdrive config
GDRIVE_CONFIG_PATH="/Users/max/.gdrive-test-sync"

####################################
## encrypt source files
####################################
echo FOLDER="/<PATH-TO-MY-IMPORTANT-FILES-OR-BACKUPS>"

for f in $FOLDER/*; do echo "Encrypting $f ..."
    echo y | $GPG --recipient="$GPG_KEY" -e $f
    GPG_FILE="$f.gpg"

    BASE_NAME=$(basename $GPG_FILE)
    echo "Moving to $GDRIVE_SYNC_DIR/$BASE_NAME ..."
    mv $GPG_FILE $GDRIVE_SYNC_DIR/$BASE_NAME
done

## ...you may add more folders here

####################################
## sync everything
####################################

echo
echo "Sync files with Google Drive ..."

$GDRIVE sync upload $GDRIVE_SYNC_DIR $GDRIVE_DEST -c $GDRIVE_CONFIG_PATH

####################################
## cleanup
####################################

echo if [ "XZ$GDRIVE_SYNC_DIR" != "XZ" ]; then echo "Removing encrypted files from $GDRIVE_SYNC_DIR ..."
    rm -f $GDRIVE_SYNC_DIR/*
fi 
```

这是一个非常简单的脚本，可以进一步扩展。例如，遍历文件夹树或生成并加密由许多小文件组成的`tar`档案。我让您根据需要进行调整。

好的，让我们运行`sync.sh`脚本。

```
$ ./sync.sh 

Encrypting LICENSE ...
Moving to /var/local/mysyncfolder/LICENSE.gpg ...
Encrypting Makefile ...
Moving to /var/local/mysyncfolder/Makefile.gpg ...
Encrypting MobileDevice.h ...
Moving to /var/local/mysyncfolder/MobileDevice.h.gpg ...
Encrypting README.md ...
Moving to /var/local/mysyncfolder/README.md.gpg ...

Sync files with Google Drive ...
Starting sync...
Collecting local and remote file information...
Found 4 local files and 4 remote files

4 local files has changed
[0001/0006] Updating LICENSE.gpg -> TestSync/LICENSE.gpg
[0002/0006] Updating Makefile.gpg -> TestSync/Makefile.gpg
[0003/0006] Updating MobileDevice.h.gpg -> TestSync/MobileDevice.h.gpg
[0004/0006] Updating README.md.gpg -> TestSync/README.md.gpg
Sync finished in 7.074881652s

Removing encrypted files from /var/local/mysyncfolder ... 
```

如果你现在看一下 Google Drive，你会发现所有的加密文件都放在目标文件夹中。

[![Synchronized files](img/cc08a3fe4a4842e523546f37ed66dac0.png)](http://i.imgur.com/cvgiMsv.png)

那么，如果你需要定期这样做呢？我是不是听到你说了一份工作？这里有一个每天 00:30 调用`sync.sh`脚本的:

```
# m h  dom mon dow   command
30 0 * * * /var/local/sync.sh >> /var/log/sync.log 
```

## 视窗

Windows 系统上的同步工作流和脚本非常相似。当然，你需要 GnuPG - [Gpg4win](https://www.gpg4win.org/get-gpg4win.html) 的 Windows 版本和 gdrive CLI 的 Windows 二进制文件。

```
@echo off
setlocal

REM ################################
REM ## configuraitons
REM ################################
SET RC=0
SET "CURRENT_DIR=%cd%"

SET "GDRIVE=C:\SYNC_TEST\gdrive-windows-x64.exe"

REM # key to encrypt with (the one we generated earlier)
SET "GPG_KEY=maxmustermann@example.org"
SET "GNUPGHOME=C:\SYNC_TEST\mygpgkeys"
REM # destination folder id in gdrive
SET "GDRIVE_DEST=0BxNabiLkX8lpSGN3UVRmWEVQWWM"
REM # local sync folder path
SET "GDRIVE_SYNC_DIR=C:\SYNC_TEST\mysyncfolder"
REM # gdrive config
SET "GDRIVE_CONFIG_PATH=C:\Users\<MY_USERNAME>\AppData\Local\gdrive-test"

REM ####################################
REM ## encrypt source files
REM ####################################

SET "FOLDER=C:\SYNC_TEST\sourcefiles"

PUSHD
CD %FOLDER%

for %%f in (*) do ( 
    echo Encrypting %%f ...
    gpg --recipient="%GPG_KEY%" -e %%f
    if errorlevel 1 (
        goto error_encrypt
    )

    echo Moving %%f.gpg to %GDRIVE_SYNC_DIR% ...
    move %%f.gpg %GDRIVE_SYNC_DIR%
)
POPD

REM ####################################
REM ## sync everything
REM ####################################

echo Sync files with Google Drive ...

PUSHD
CD %GDRIVE_SYNC_DIR%
%GDRIVE% sync upload %GDRIVE_SYNC_DIR% %GDRIVE_DEST% -c %GDRIVE_CONFIG_PATH%
POPD

REM ####################################
REM ## cleanup
REM ####################################
:cleanup

PUSHD
cd %GDRIVE_SYNC_DIR%
echo Removing encrypted files from %GDRIVE_SYNC_DIR% ...
del /F /Q *
POPD

goto end

:error_encrypt
echo Error encrypting file.
goto end

:end 
```

运行`sync.cmd`脚本将产生以下结果:

```
> sync.cmd
Encrypting LICENSE ...
Moving LICENSE.gpg to C:\SYNC_TEST\mysyncfolder ...
        1 file(s) moved.
Encrypting Makefile ...
Moving Makefile.gpg to C:\SYNC_TEST\mysyncfolder ...
        1 file(s) moved.
Encrypting MobileDevice.h ...
Moving MobileDevice.h.gpg to C:\SYNC_TEST\mysyncfolder ...
        1 file(s) moved.
Encrypting README.md ...
Moving README.md.gpg to C:\SYNC_TEST\mysyncfolder ...
        1 file(s) moved.
Sync files with Google Drive ...
Starting sync...
Collecting local and remote file information...
Found 4 local files and 6 remote files

4 local files has changed
[0001/0004] Updating LICENSE.gpg -> TestSync\LICENSE.gpg
[0002/0004] Updating Makefile.gpg -> TestSync\Makefile.gpg
[0003/0004] Updating MobileDevice.h.gpg -> TestSync\MobileDevice.h.gpg
[0004/0004] Updating README.md.gpg -> TestSync\README.md.gpg
Sync finished in 4.2520023s
Removing encrypted files from C:\SYNC_TEST\mysyncfolder ... 
```

您可以使用 Windows 任务计划程序[【12】](#refs)在所需的时间或间隔运行`sync.cmd`脚本。

## 解密文件

因此，加密和同步工作流已经准备好了，这是很好的，但如果他们需要它，如何取回他们的内容呢？从 Google Drive 下载文件不成问题，但是解密内容呢？还记得我收集的所有 90 年代 mp3 文件吗？这些都是我希望我的孙辈们拥有的重要遗产。

下面是一个简单的 bash 脚本，它解密一个目录中所有加密的`gpg`文件。

```
#!/bin/sh

if [ ! -d "$1" ]; then echo "No input folder specified!"
    exit 1
fi GPG=$(which gpg)
if [ "XY$GPG" = "XY" ]; then echo "GPG not found!"
    exit 1
fi FOLDER=$1

for f in $FOLDER/*.gpg; do echo "Decrypt $f ..."
    echo "<your-gpg-key-password>" | gpg --passphrase-fd 0 --batch --yes $f
done 
```

只需传递所有加密文件所在的目录路径，并运行脚本将它们全部解密。

```
$ cd my-gdrive-downloaded-files && ./decrypt .

Decrypt ./LICENSE.gpg ...
gpg: encrypted with 2048-bit RSA key, ID E31A85E6729D5490, created 2017-05-25
    "Max Mustermann <maxmustermann@example.org>"
Decrypt ./Makefile.gpg ...
gpg: encrypted with 2048-bit RSA key, ID E31A85E6729D5490, created 2017-05-25
    "Max Mustermann <maxmustermann@example.org>"
Decrypt ./MobileDevice.h.gpg ...
gpg: encrypted with 2048-bit RSA key, ID E31A85E6729D5490, created 2017-05-25
    "Max Mustermann <maxmustermann@example.org>"
Decrypt ./README.md.gpg ...
gpg: encrypted with 2048-bit RSA key, ID E31A85E6729D5490, created 2017-05-25
    "Max Mustermann <maxmustermann@example.org>" 
```

所以，就这样了。你所有的数据都加密了。桑德尔·皮帅再也不会笑了。

# 结论

最后，这一切有什么好处呢？(我大概应该把那个问题放在文章开头。)

我个人用它来保持服务器备份安全地存储在云上。大多是二进制文件，pdf，存档等。使用这种方法存储频繁变化的文档(如文本或 Word 文件)可能没有意义，尽管没有什么可以阻止你。

# 附件-元数据

我在写这篇文章时没有考虑的一个话题是元数据[【13】](#refs)。您可能已经注意到，当上传到 Google Drive 时，上面脚本中的原始文件名总是会保留下来。对于复杂的算法来说，文件的名称和大小可能已经足够提取大量有意义的信息了，这些信息是关于该文件在您的用户上下文中的用途和内容。可以使用简单的计数器机制来产生每个文件名的散列值，从而至少防止文件名元数据提取。

下面是对 Linux/macOS 加密脚本的修改，它使用任意的 salt 值对文件名进行哈希运算，并生成所有加密文件的 CSV 文件索引。

```
# a registry of hashed files
INDEX="/var/local/gdrive_index.csv"
SALT="someveryveryveryveryveryveryverylongstring"

## reset index contents
echo "FILENAME;HASHED NAME" > $INDEX

####################################
## Encrypt source files
####################################
echo FOLDER="/<PATH-TO-MY-IMPORTANT-FILES-OR-BACKUPS>"

for f in $FOLDER/*; do echo "Encrypting $f ..."
    echo y | $GPG --recipient="$GPG_KEY" -e $f

    BASE_NAME=$(basename $f)
    HASHED=$(echo $BASE_NAME.$SALT | openssl dgst -sha256)
    echo "$BASE_NAME;$HASHED" >> $INDEX

    GPG_FILE="$f.gpg"
    HASHED_GPG_FILE="$HASHED.gpg"

    echo "Moving to $GDRIVE_SYNC_DIR/$HASHED_GPG_FILE ..."
    mv $GPG_FILE $GDRIVE_SYNC_DIR/$HASHED_GPG_FILE
done 
```

# 参考文献

1.  GDrive 定价指南-[google.com/drive/pricing/](https://www.google.com/drive/using-drive)
2.  微软 OneDrive 计划-[onedrive.live.com/about/en-us/plans](https://onedrive.live.com/about/en-us/plans)
3.  Dropbox 多少钱？——[dropbox.com/help/billing/cost](https://www.dropbox.com/help/billing/cost)
4.  谷歌两步验证-[google.com/landing/2step](https://www.google.com/landing/2step)
5.  人的因素是良好安全的关键-[computer weekly . com/opinion/人的因素是良好安全的关键](http://www.computerweekly.com/opinion/The-human-factor-is-key-to-good-security)
6.  谷歌服务条款-[google.com/intl/en/policies/terms](https://www.google.com/intl/en/policies/terms)
7.  GnuPG 是什么？——[gnupg.org/faq/gnupg-faq.html#whats_gnupg](https://gnupg.org/faq/gnupg-faq.html#whats_gnupg)
8.  Google Drive CLI 客户端-[github.com/prasmussen/gdrive](https://github.com/prasmussen/gdrive)
9.  serverfault.com/q/214605 没有足够的熵-[GPG](https://serverfault.com/q/214605)
10.  克利奥帕特拉-[openpgp.org/software/kleopatra](https://www.openpgp.org/software/kleopatra/)
11.  谜-[enigmail.net/index.php/en](https://www.enigmail.net/index.php/en/)
12.  计划任务-[TechNet . Microsoft . com/en-us/library/cc 748993(v = ws . 11)。aspx](https://technet.microsoft.com/en-us/library/cc748993(v=ws.11).aspx)
13.  元数据-[en.wikipedia.org/wiki/Metadata](https://en.wikipedia.org/wiki/Metadata)