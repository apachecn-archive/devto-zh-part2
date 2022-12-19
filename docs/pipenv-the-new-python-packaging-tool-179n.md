# Pipenv，新的 Python 打包工具

> 原文：<https://dev.to/cindyachieng/pipenv-the-new-python-packaging-tool-179n>

[![](../Images/e66916fc10bca6725ecc781bfe9fb6a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4z_Y4sCE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xu3vw4t3rwhvlvp4h12l.jpg)

很明显，我一直生活在岩石下，但现在不是了！我分享了一篇文章[关于使用 pip 和 virtualenv](https://achiengcindy.com/how-set-django-environment-linux-beginners/1) 和 [M. Floering](https://dev.to/hangtwenty/comment/2m3e) 设置 django 环境，建议我查看一下`pipenv`。你猜对了，是的，我做了，我喜欢它！
`Pipenv`是根据[pipenv.org](https://docs.pipenv.org/)官方推荐的 python 打包工具。这是一个伟大的工具，用于管理你的项目的依赖关系，以及安装和卸载软件包。

看起来 pip 和 virtualenv 做得很好，但是为什么是 pipenv 呢？

1.  可以把`pipenv` 看作是 pip 和 virtualenv 的强大组合。
2.  告别手动创建和管理您的虚拟环境`pipenv` 会为您做到这一点！。
3.  擅长管理项目的依赖性。代替 pip 的`requirements.txt`，`pipenv` 创建了两个文件:`pipfile`用于您直接安装的包，而`pipfile.lock`用于依赖项和基于您的 pip 文件的包的特定版本。

#### pipenv 工作流

1.  如果 virtualenv 不存在，则创建它。
2.  创建`pipfile`来管理您安装的软件包
3.  最后，创建`pipfile.lock`来管理 pipfile 中的包的依赖关系

#### 我们开始吧！

我们使用 `pip`来安装`pipenv`，所以要确保你已经安装了 python 和 pip。
检查是否安装了 pip 运行:

```
$ pip --version 
```

Enter fullscreen mode Exit fullscreen mode

..和 python:

```
$ python --version 
```

Enter fullscreen mode Exit fullscreen mode

**安装管道**

```
$ pip install --user pipenv 
```

Enter fullscreen mode Exit fullscreen mode

> **注意**:我已经使用了用户安装，否则只需运行$ pip install pipenv。

**使用 pipenv**

现在我们已经成功安装了`pipenv`，让我们使用它。创建项目目录并将其命名为(我将把我的命名为 laughing-blog ),然后将目录更改为项目的文件夹。
让我们使用 `install`命令启动`pipenv`。

```
$ mkdir laughing-blog //projectname
$ cd laughing-blog
$ pipenv --three install 
```

Enter fullscreen mode Exit fullscreen mode

> **注意**:使用`$ pipenv --three`指定 python 3 的版本，使用`$ pipenv --two` 指定 python 2 的版本。要使用一个任意的 python 版本，可以做:
> `pipenv --python 3.6.4`或者只需`$ pipenv install`使用默认的 python 版本。

上面的代码为您的项目创建了一个新的虚拟环境(如果它还不存在的话)以及项目目录中的两个神奇文件`Pipfile`和`Pipfile.lock`。

下面是 **pipenv 安装**输出的分解图。

**虚拟人**

```
Creating a virtualenv for this project…
Using /usr/bin/python3 (3.5.2) to create virtualenv
Virtualenv location: /home/cindy/.local/share/virtualenvs/laughing-blog-FpALE3CM 
```

Enter fullscreen mode Exit fullscreen mode

**pipfile**

```
Creating a Pipfile for this project… 
```

Enter fullscreen mode Exit fullscreen mode

最后， **pipfile.lock**

```
Pipfile.lock not found, creating…
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
Updated Pipfile.lock (711973)!
Installing dependencies from Pipfile.lock (711973)…
🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 0/0 — 00:00:00 
```

Enter fullscreen mode Exit fullscreen mode

**激活 Virtualenv**

要激活 virtualenv，只需运行:

```
$ pipenv shell 
```

Enter fullscreen mode Exit fullscreen mode

要退出 virtulalenv，请运行:

```
$ exit 
```

Enter fullscreen mode Exit fullscreen mode

#### 管理软件包

要安装软件包，只需运行:

```
$ pipenv  install <packagename> 
```

Enter fullscreen mode Exit fullscreen mode

...并在 pipfile 运行中删除包:

```
$ pipenv  uninstall <packagename> 
```

Enter fullscreen mode Exit fullscreen mode

#### 了解 pipfile 和 pipfile.lock

**Pipfile**

`Pipfile`管理你安装的软件包，把它看作是`requirements.txt`的升级。

pipfile 为什么优于 requirements.txt

让我们看一个`requirements.txt`文件的例子

> **注意**:只需运行`pip freeze > requirements.txt`即可生成上述文件

```
...
certifi==2018.4.16
chardet==3.0.4
defusedxml==0.5.0
Django==2.0.4
django-allauth==0.35.0
idna==2.6
oauthlib==2.0.7
python3-openid==3.1.0
... 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到`requirements.txt`列出了我们安装的所有包，版本和依赖项。
`pipfile`保存我们安装的包，剩余的交给`pipfile.lock`

**pipfile.lock**

这是一个包含依赖关系的文件，依赖关系基于`Pipfile`中的包，这个包的特定版本可以避免自动升级相互依赖的包和破坏项目依赖树的风险。

如果 pipfile.lock 由于某种原因没有创建，您可以通过运行以下命令来锁定当前安装的软件包:

```
$ pipenv lock 
```

Enter fullscreen mode Exit fullscreen mode

**pipfile 样本**

```
 [packages]
 django = "*"
 django-allauth = "*"

 [requires]
 python_version = "3.5" 
```

Enter fullscreen mode Exit fullscreen mode

**pipfile.lock 示例**

```
 "django-allauth": {

        "hashes": [

            "sha256:7b31526cccd1c46f9f09acf0703068e8a9669337d29eb065f7e8143c2d897339"

        ],

        "index": "pypi",

        "version": "==0.35.0"

    },

    "idna": {

        "hashes": [

            "sha256:2c6a5de3089009e3da7c5dde64a141dbc8551d5b7f6cf4ed7c2568d0cc520a8f",

            "sha256:8c7309c718f94b3a625cb648ace320157ad16ff131ae0af362c9f21b80ef6ec4"

        ],

        "version": "==2.6"

    },

    "oauthlib": {

        "hashes": [

            "sha256:09d438bcac8f004ae348e721e9d8a7792a9e23cd574634e973173344046287f5",

            "sha256:909665297635fa11fe9914c146d875f2ed41c8c2d78e21a529dd71c0ba756508"

        ],

        "version": "==2.0.7"

    },

    "python3-openid": {

        "hashes": [

            "sha256:0086da6b6ef3161cfe50fb1ee5cceaf2cda1700019fda03c2c5c440ca6abe4fa",

            "sha256:628d365d687e12da12d02c6691170f4451db28d6d68d050007e4a40065868502"

        ],

        "version": "==3.1.0"

    }, 
```

Enter fullscreen mode Exit fullscreen mode

##### 
 [T3】
结论](#conclusion) 

现在，您已经了解了 pipenv 的定义、优点和工作原理。以及两个神奇文件 pipfile 和 pipfile.lock 的区别。

这篇文章最初发表在[achiengcindy.com](https://achiengcindy.com/pipenv-new-python-packaging-tool/5/)