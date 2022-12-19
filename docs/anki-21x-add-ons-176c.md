# Anki 2.1.x Add-ons

> 原文：<https://dev.to/chuck_ha/anki-21x-add-ons-176c>

安装 Anki 附加组件还不算太差，但是在 2.0.x 和 2.1.x 版本之间略有不同。

作为背景，我从这本[低调的 Anki](http://massimmersionapproach.com/table-of-contents/anki/low-key-anki/low-key-anki-summary-and-installation/) 指南开始，并想安装其中一个插件(ResetEZ.py ),但它不能开箱即用。

在 OS X，附件可以在`$HOME/Library/Application Support/Anki2/addons21`中找到。做一个目录，我把我的命名为`resetez`。在里面，添加一个名为`__init__.py`的文件。把上面链接找到的`ResetEZ.py`的内容(可能在前面一两页)放入`__init__.py`的内容。阅读说明并进行配置。重启 Anki，你应该会在 Tools 下看到一个新的菜单项！