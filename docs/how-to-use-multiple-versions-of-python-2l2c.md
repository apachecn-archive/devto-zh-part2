# 如何使用多个版本的 Python

> 原文：<https://dev.to/hardiksondagar/how-to-use-multiple-versions-of-python-2l2c>

我的系统将`2.7.6`作为默认的 python 版本，我的大部分应用程序都在上面运行，但是我正在与 AWS IoT 合作的一个项目需要版本`2.7.9`。

我将展示如何使用 **pyenv** 运行多个版本的 python。

### 先决条件

```
$ sudo apt-get update
$ sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev 
```

Enter fullscreen mode Exit fullscreen mode

### 安装 pyenv

使用自动安装程序提供的一行程序安装 pyenv。

```
$ curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash 
```

Enter fullscreen mode Exit fullscreen mode

在的末尾添加以下行。bashrc 文件并重启终端。

```
export PATH="/root/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)" 
```

Enter fullscreen mode Exit fullscreen mode

**注**:供`zsh`使用。zshrc 文件。

### 用法

安装 python 版本`2.7.9`。

```
$ pyenv install 2.7.9 
```

Enter fullscreen mode Exit fullscreen mode

列出所有已安装的 python 版本。

```
$ pyenv versions
* system (set by /home/ubuntu/.pyenv/version)
  2.7.9 
```

Enter fullscreen mode Exit fullscreen mode

为您的项目使用`2.7.9`版本。

```
$ mkdir aws-iot-mqtt
$ cd aws-iot-mqtt
$ pyenv local 2.7.9 
```

Enter fullscreen mode Exit fullscreen mode

现在，验证当前目录启用了哪个 python 版本。

```
// Inside `aws-iot-mqtt` directory
$ python -V
Python 2.7.9
// Check version in parent directory
$ cd ..
$ python -V
Python 2.7.6 
```

Enter fullscreen mode Exit fullscreen mode

**注** : *在`Ubuntu 16.04`服务器版*上测试