# Ruby、Python、Node 和 Rust 的版本管理

> 原文：<https://dev.to/booyaa/version-management-for-ruby-python-node-and-rust-o5i>

如果你发现自己需要一个异国版本的 Ruby、Python、Node 或 Rust，这里有一个方便的备忘单。其他版本管理工具也可以用于 ruby、python 和 node，我只是碰巧喜欢这些。

| 行动 | 红宝石 | 计算机编程语言 | 结节 | 锈 |
| --- | --- | --- | --- | --- |
| 列出要安装的可用版本 | `rbenv install --list` | `pyenv install --list` | `nvm ls-remote` | 不适用的 |
| 安装特定版本 | `rbenv install 2.5.1` | `pyenv install 3.6.6` | `nvm install v10.9.0` | `rustup use nightly-2018-08-01` |
| 列出本地安装的版本 | `rbenv versions` | `pyenv versions` | `nvm ls` | `rustup show` |
| 将项目固定到版本 | `rbenv local 2.5.1` | `pyenv local 3.6.6` | `echo v10.9.0 > .nvmrc ; nvm use` | `rustup override nightly-2018-08-01` |
| 设置全局版本 | `rbenv global 2.5.1` | `pyenv global 3.6.6` | 不适用的 | `rustup default nightly-2018-08-01` |

## Python 虚拟环境

这假设您已经将项目绑定到 python 的特定版本。

```
# create an virtual environment
pyenv virtualenv thingy
# activate!
pyenv activate thingy
# do your thang!
pip install pylint black pytest
# exit virtual environment
pyenv deactivate 
```

Enter fullscreen mode Exit fullscreen mode

## 铁锈组件(标准库，RLS，clippy)

将为活动工具链安装组件(稳定版、每晚版、测试版)

安装 RLS: `rustup component add rls-preview rust-analysis rust-src`

要安装 clippy: `rustup component add clippy-preview`

## 铁锈文档

你知道吗，当你安装一个工具链的时候，你总是得到一个 Rust 文档(语言参考，标准库和 Rust book)套件的离线拷贝？打开激活的工具链:`rustup doc`