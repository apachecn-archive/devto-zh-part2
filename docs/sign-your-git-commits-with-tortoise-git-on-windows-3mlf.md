# 用 windows 上的乌龟 git 签署你的 git 提交

> 原文：<https://dev.to/c33s/sign-your-git-commits-with-tortoise-git-on-windows-3mlf>

在阅读了 agrinman 关于欺骗 git 提交的这篇好文章后，我决定看看如何签署 git 提交。

这比我想象的要复杂一点，但仍然是一个相当简单的任务。

首先生成一个密钥对(`--allow-freeform-uid`是为了克服`Name must be at least 5 characters long`错误):

```
λ gpg --full-generate-key --allow-freeform-uid
gpg (GnuPG) 2.2.8; Copyright (C) 2018 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 5y
Key expires at 08/09/23 18:32:52 W. Europe Daylight Time
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: devto-example
Email address: devto@example.com
Comment:
You selected this USER-ID:
    "devto-example <devto@example.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key 8493DA39DD68724D marked as ultimately trusted
gpg: revocation certificate stored as '<REMOVED>'
public and secret key created and signed.

pub   rsa4096 2018-08-10 [SC] [expires: 2023-08-09]
      F0FB9F40A82A7F2502B652348493DA39DD68724D
uid                      devto-example <devto@example.com>
sub   rsa4096 2018-08-10 [E] [expires: 2023-08-09] 
```

Enter fullscreen mode Exit fullscreen mode

导出您的公钥

```
λ gpg --armor --export devto@example.com
----------BEGIN PGP PUBLIC KEY BLOCK-----

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
1UOjABr0Qt9w
=sdjc
----------END PGP PUBLIC KEY BLOCK----- 
```

Enter fullscreen mode Exit fullscreen mode

并将其添加到[https://gitlab.com/profile/gpg_keys](https://gitlab.com/profile/gpg_keys)和[https://github.com/settings/gpg/new](https://github.com/settings/gpg/new)

然后，您必须通过将`key id`添加到 git 配置中来配置 git 使用这个密钥。

要获得密钥 id，请运行:

```
λ gpg --list-keys --keyid-format LONG devto@example.com
pub   rsa4096/8493DA39DD68724D 2018-08-10 [SC] [expires: 2023-08-09]
      F0FB9F40A82A7F2502B652348493DA39DD68724D
uid                 [ultimate] devto-example <devto@example.com>
sub   rsa4096/75928CD4BDEAE0D7 2018-08-10 [E] [expires: 2023-08-09] 
```

Enter fullscreen mode Exit fullscreen mode

id 的排序版本是在`pub rsa4096/` - > `8493DA39DD68724D`之后的部分，长版本在第二行`F0FB9F40A82A7F2502B652348493DA39DD68724D`因为[键冲突](https://evil32.com/)我建议你使用长键格式。

现在将以下内容添加/合并到您的【全局】`.gitconfig`

```
[user]
    signingkey = F0FB9F40A82A7F2502B652348493DA39DD68724D

[commit]
    gpgsign = true
[gpg]
    program = "C:/Program Files (x86)/GNU/GnuPG/gpg2.exe" 
```

Enter fullscreen mode Exit fullscreen mode

你需要安装 [gpg](https://www.gpg4win.org/) 并根据你的安装路径修改`program`路径。签名密钥是您的长密钥 id。要自动签署您的提交，请添加`commit.gpgsign=true`。

现在，如果您提交，提交将被签名。您可以轻松验证:

```
λ git verify-commit HEAD
gpg: Signature made 08/10/18 19:37:21 W. Europe Daylight Time
gpg:                using RSA key F0FB9F40A82A7F2502B652348493DA39DD68724D
gpg: Good signature from "devto-example<devto@example.com>" [ultimate] 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
git log --show-signature 
```

Enter fullscreen mode Exit fullscreen mode

*   封面图片作者:[https://pix abay . com/en/writing-pen-man-ink-paper-pencils-1149962/](https://pixabay.com/en/writing-pen-man-ink-paper-pencils-1149962/)
*   另请参见:
    *   [https://git lab . com/tortoisegit/tortoisegit/issues/1494 # note _ 14971615](https://gitlab.com/tortoisegit/tortoisegit/issues/1494#note_14971615)
    *   [https://git lab . com/tortoisegit/tortoisegit/issues/1494 # note _ 62610134](https://gitlab.com/tortoisegit/tortoisegit/issues/1494#note_62610134)
    *   [https://jamesmckay . net/2016/02/signing-git-commits-with-gpg-on-windows/](https://jamesmckay.net/2016/02/signing-git-commits-with-gpg-on-windows/)
    *   [https://mikegerwitz.com/papers/git-horror-story](https://mikegerwitz.com/papers/git-horror-story)
    *   [https://help.github.com/articles/signing-commits-using-gpg/](https://help.github.com/articles/signing-commits-using-gpg/)
    *   [https://security . stack exchange . com/questions/84280/short-open PGP-key-ids-are-unsecured-how-to-configure-GnuPG-to-use-long-key-ids-I](https://security.stackexchange.com/questions/84280/short-openpgp-key-ids-are-insecure-how-to-configure-gnupg-to-use-long-key-ids-i)
    *   [https://www . GnuPG . org/documentation/manuals/GnuPG/Unattended-GPG-key-generation . html](https://www.gnupg.org/documentation/manuals/gnupg/Unattended-GPG-key-generation.html)
    *   [https://help.github.com/articles/generating-a-new-gpg-key/](https://help.github.com/articles/generating-a-new-gpg-key/)