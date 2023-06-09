# dotenv-vault:无依赖性。环境加密工具

> 原文：<https://dev.to/acro5piano/dotenv-vault-no-dependency-env-encrypt-tool-3h5g>

# TL；博士；医生

dotenv-vault 是一个简单的 dotenv 管理工具，保持令牌的秘密。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[跳马](https://github.com/acro5piano/dotenv-vault)

### 受 yaml_vault 启发的简单 dotenv 加密和解密工具

<article class="markdown-body entry-content container-lg" itemprop="text">

# 使用 git-crypt[https://github.com/AGWA/git-crypt](https://github.com/AGWA/git-crypt)

不再维护。使用其他工具。

[![CircleCI](img/7289d37bf0f7a3b95981b914b72cdcdd.png)T2】](https://circleci.com/gh/acro5piano/dotenv-vault)

# dotenv-金库

[![](img/eaa14e9d32c78cd2c3f6f2ef09d2a064.png)T2】](https://github.com/acro5piano/dotenv-vault/blob/master/demo.gif)

受 yaml_vault 启发的简单 dotenv 加密工具

默认密码是 aes-256-cbc。默认的签名摘要是 SHA256。

# 安装

对于 MacOS:

```
git clone https://github.com/acro5piano/dotenv-vault ~/.dotenv-vault
ln -sfnv ~/.dotenv-vault/bin/dotenv-vault /usr/local/bin/dotenv-vault 
```

对于 Linux:

```
git clone https://github.com/acro5piano/dotenv-vault ~/.dotenv-vault
sudo ln -sfnv ~/.dotenv-vault/bin/dotenv-vault /usr/bin/dotenv-vault 
```

## 要求

dotenv-vault 需要以下内容:

*   Bash >= 2
*   Openssl >= 2
*   Perl >= 5

几乎所有的机器都不需要任何额外的安装过程。

# 使用

## 加密

输入文件(。环境):

```
NODE_ENV=development
API_KEY=123456789 
```

命令:

```
$ dotenv-vault -e API_KEY -k foobarbaz encrypt .env 
```

其中`-e`指定您加密的密钥。

输出:

```
NODE_ENV=development
API_KEY=U2FsdGVkX186T6zdupR27pXHO0Hdnz9rqZfVdgqBEqk= 
```

## 解释

输入文件(. env.encrypted):

```
NODE_ENV=development
API_KEY=U2FsdGVkX186T6zdupR27pXHO0Hdnz9rqZfVdgqBEqk= 
```

命令:

```
$ dotenv-vault -e API_KEY -k foobarbaz decrypt .env 
```

输出:

```
NODE_ENV=development
API_KEY=123456789 
```

## 创建加密环境

`dotenv-vault create`命令方便创建新条目:

```
$ bin/dotenv-vault -k foobarbaz create 'SOME_KEY=123456'
# => SOME_KEY=U2FsdGVkX18tEclKImEV30HSG0b7IOu3dyO3MpceCd4= 
```

您可以粘贴或重定向以注册新条目，如下所示:

```
$ bin/dotenv-vault
```

…</article>

[View on GitHub](https://github.com/acro5piano/dotenv-vault)

# 为什么

您如何管理存储库中的`.env`文件？

该文件可能包含一些秘密令牌。在开放源码软件中，这些信息不得公开。即使在私有存储库中，我们也不应该在 Git 存储库中包含秘密信息，因为很多利益相关者，包括外包开发者都会提交代码。

然而，应用程序中需要秘密信息，所以我们可能希望将它们包含到存储库中。

根据这篇文章，主要选择是使用密钥管理服务 KMS。主要的云服务如 AWS 和 GCP 提供该服务。

[https://www . Reddit . com/r/devo PS/comments/52pl5c/how _ do _ you _ manage _ env _ dotenv _ files/](https://www.reddit.com/r/devops/comments/52pl5c/how_do_you_manage_env_dotenv_files/)

然而，我想管理他们更简单的方式没有任何插件和依赖。

# 选项

yaml_vault 是一个让 yaml 文件保密的好工具。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[joker 1007](https://github.com/joker1007)/[YAML _ vault](https://github.com/joker1007/yaml_vault)

### Yaml 文件加密/解密助手。

<article class="markdown-body entry-content container-lg" itemprop="text">

# YamlVault

[![Gem Version](img/8c7f1278e70de5da65d9bd0b57f7d515.png) ](https://badge.fury.io/rb/yaml_vault) [ ![Build Status](img/1558425fdfe2b4fba97f2da38e757be2.png)](https://travis-ci.org/joker1007/yaml_vault)

Yaml 文件加密/解密助手。

## 从 0.x 到 1.0 的突破性变化

*   输出 YAML 文件保持别名&锚语法&标签信息。(但空行被修剪)
*   `--key`格式改变。(首先需要`<article class="markdown-body entry-content container-lg" itemprop="text"作为根文档)
*   `--key`支持新格式。(根文档、通配符、正则表达式、引号)

## 编码算法

yaml_vault 使用 active support::message encryptor。

默认密码是`aes-256-cbc`默认签名摘要是`SHA256`。

## 装置

将这一行添加到应用程序的 Gemfile 中:

```
gem 'yaml_vault'
```

Enter fullscreen mode Exit fullscreen mode

然后执行:

```
$ bundle 
```

或者自己安装为:

```
$ gem install yaml_vault 
```

## 使用

### 加密

```
# secrets.yml
default: &default
  hoge: fuga
  aaa: true
  bbb: 2
foo: bar
complicated:
  - 1
  - ["hoge", "fuga"]
  - [{key1: val1, key2: val2}, {key3: val3}]
  - a:
      b:
        c: d
        e: !ruby/range 1..10

test:
  <<: *default
  hoge:
    - 1
    - 2
    - 3

vault:
  secret_data: 
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/joker1007/yaml_vault)

它是用红宝石写的。Ruby on Rails 使用 YAML 文件进行配置。因此该工具在 Ruby on Rails 应用程序中很受欢迎。

默认情况下，Rails 5.2 具有类似的功能。如果我们运行下面的命令，文件将被解密，我们的编辑器将运行。编辑文件后，文件再次加密。

```
bin/rails credentials:edit 
```

Enter fullscreen mode Exit fullscreen mode

然而，我的项目既不使用 Ruby on Rails 也不使用 YAML。只是`.env`和`.env.local`，`.env.production`文件存在。

于是我创造了 [dotenv-vault](https://github.com/acro5piano/dotenv-vault) (显然是受到 yaml_vault 的启发)。

# 哲学

*   较少依赖。如果我们在 CI 中运行，这很方便。
*   简单。设置简单，没有 DSL，零学习成本。
*   富有成效。尽可能自动化。

更少的依赖性真的很重要，因为我们可以在任何环境、任何项目中使用这个工具，而且成本为零。目前，dotenv-vault 依赖于以下三项:

*   尝试
*   Openssl
*   Perl 语言

我选择 Perl 来解析 dotenv 文件，因为 Perl 不改变操作系统之间的命令语法。`sed`BSD 和 GNU 不同。
各大 OS，如 macOS、Ubuntu，默认都有 Perl。

# 如何使用

## 手动

安装后(检查回购)，在要加密的行的末尾添加`# encrypt-me`注释:

```
# .env

APP_ENV=production
APP_KEY=super_secret_value # encrypt-me 
```

Enter fullscreen mode Exit fullscreen mode

然后运行以下命令:

```
$ dotenv-vault encrypt .env 
```

Enter fullscreen mode Exit fullscreen mode

您可能会被要求输入加密密码，然后按回车键:

```
Please input password and press Return: 
```

Enter fullscreen mode Exit fullscreen mode

它会将加密文件输出为标准输出，如下所示:

```
APP_ENV=production
APP_KEY=U2FsdGVkX19VF+rLm7ypGQFl76Sq7QUEaU5uM+AlQmRxcKMTTj63R81K4U0WYZzy # decrypt-me 
```

Enter fullscreen mode Exit fullscreen mode

因此，您可以将输出重定向到一个文件:

```
$ dotenv-vault encrypt .env > .env.production 
```

Enter fullscreen mode Exit fullscreen mode

要解密文件，请运行:

```
$ dotenv-vault decrypt .env.production 
```

Enter fullscreen mode Exit fullscreen mode

然后输入密码，解密后的输出将会显示。

## [词中的](#in-ci)

在 CI 中，我们不能手动输入密码，所以在您的 CI 服务中设置`DOTENV_PASSWORD`环境变量。

```
$ env DOTENV_PASSWORD=foo dotenv-vault decrypt .env.production > .env 
```

Enter fullscreen mode Exit fullscreen mode

(实际上不需要`env DOTENV_PASSWORD=foo`

## 添加令牌

如果我们每次添加新令牌时都要解密和加密，这是不方便的。

`dotenv-vault generate`简单生成加密令牌。

```
$ dotenv-vault generate 'FIREBASE_TOKEN=new_secret_token' 
```

Enter fullscreen mode Exit fullscreen mode

命令输出将是这样的:

```
FIREBASE_TOKEN=U2FsdGVkX19NWIIPCamKJOFdJktBSETdFNV6BIwBlkjBNhCRQ18z2zDtVRu3fO79 # decrypt-me 
```

Enter fullscreen mode Exit fullscreen mode

所以我们可以重定向输出来添加新的令牌。

```
$ dotenv-vault generate 'FIREBASE_TOKEN=new_secret_token' >> .env.production 
```

Enter fullscreen mode Exit fullscreen mode

# 感谢

我使用 Bashtub 来运行 bash 脚本的单元测试。Bashtub 确实是一个简单的好工具。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[【ueokonde】](https://github.com/ueokande)/[【Bashi tub】](https://github.com/ueokande/bashtub)

### Bash 的微型单元测试框架。

<article class="markdown-body entry-content container-lg" itemprop="text">

# Bashtub <g-emoji class="g-emoji" alias="bathtub" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6c1.png">🛁</g-emoji>-Bash 的单元测试框架

[![Build Status](img/7cc0c874601b84ab171bebf1f5f8bf77.png)T2】](https://travis-ci.org/ueokande/bashtub)

Bashtub 是一个用于 Bash 的小型单元测试框架，你可以指定测试用例，因为基于断言的测试用例，如 xUnit Bashtub，在测试通过时会彩色输出测试结果

[![success](img/e37110aa80023d3a813f9831e442a804.png)T2】](https://raw.githubusercontent.com/ueokande/bashtub/master/success.png)

当测试失败时，它输出失败信息，

[![failure](img/9fa13a8f64b16b9474dfe890f14a0ffb.png)T2】](https://raw.githubusercontent.com/ueokande/bashtub/master/failure.png)

## 要求

*   Bash 3.2+版

## 装置

```
curl -o ~/bin/bashtub https://raw.githubusercontent.com/ueokande/bashtub/v0.2/bin/bashtub
chmod +x ~/bin/bashtub
```

Enter fullscreen mode Exit fullscreen mode

其中路径`~/bin`必须包含在`$PATH`中。然后测试运行 bashtub。

```
bashtub   # output 0 examples
```

Enter fullscreen mode Exit fullscreen mode

## 写作测试

测试用例在以`testcase_`开始的函数中声明。Bashtub 自动在加载的文件中找到测试用例。测试被写成基于断言的测试用例，比如 xUnit。

### 最小测试

第一个简单的例子如下:

```
# examples/first_test_case.sh
lorem_ipsum='lorem ipsum dolor sit amet'
testcase_first_word() {
  first_word=$(echo $lorem_ipsum | cut -f1
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/ueokande/bashtub)

# 结论

在我的用例中，dotenv-vault 提供了有用的特性。如果有什么意见，请告诉我！

谢了。