# Python 与 comandos 的接口

> 原文：<https://dev.to/yorodm/interfaces-de-comandos-con-python-fgb>

# python 的简单命令界面。

我使用 [Python](https://python.org) 的很多东西中，每天都在编写帮助我的小命令行工具(尤其是在接触**devotes**时)。虽然在标准图书馆里有`argparse`，很多人对此感到高兴，但我个人喜欢让我的生活更轻松的选择。

## Docopt。

docopt 是(在我看来)创建现有命令行工具的最佳框架之一，而且其优点不是 **Python** 独有的，要使用它，只需记录将要获取命令行参数的模块或函数即可下面是一个用于订阅的工具示例，在创建存储库时无需订阅一组用户。

```
"""Subscriber.

Usage:
  subscriber.py repo <reponame> group <groupname>

"""

def main(arguments):
    repo_name = arguments.get('<reponame>')
    group_name = arguments.get('<groupname>')
    # El resto va aqui 
if __name__ == ' __main__':
    arguments = docopt( __doc__ )
    main(arguments) 
```

Enter fullscreen mode Exit fullscreen mode

调用不带参数的工具时，我们得到以下帮助消息:

```
$ python subscriber.py
Usage:
  subscriber.py repo <reponame> group <groupname> 
```

Enter fullscreen mode Exit fullscreen mode

要使用它，我们只需

```
$ python subscriber.py repo https://my.repo.cu group developers 
```

Enter fullscreen mode Exit fullscreen mode

优点:

1.  额外的代码很少(几乎所有都是文档)。
2.  你得到免费的文件。
3.  cli 的 minilenguaje 功能极为强大。

Desventajas:

1.  你要学会迷你语言。

## Lazycli。

[lazy Li](https://github.com/ninjaaron/lazycli)是一个很新的框架**，但它简单明了，让人很想使用。我们用装饰品代替文档来申报我们的**【CLI】**。作者以`cp`T7 的克隆为例向我们展示**

```
#!/usr/bin/env python3
import lazycli
import shutil
import sys

@lazycli.script
def cp(*src, dst, recursive=False):
    """copy around files"""
    for path in src:
        try:
            shutil.copy2(path, dst)
        except IsADirectoryError as err:
            if recursive:
                shutil.copytree(path, dst)
            else:
                print(err, file=sys.stderr)

if __name__ == ' __main__':
    cp.run() 
```

Enter fullscreen mode Exit fullscreen mode

如果调用该工具，则得到如下结果:

```
$ python cp.py -h
usage: cp.py [-h] [-r] [src [src ...]] dst

copy around files

positional arguments:
  src
  dst

optional arguments:
  -h, --help show this help message and exit
  -r, --recursive 
```

Enter fullscreen mode Exit fullscreen mode

优点:

1.  接近 0 个额外代码(执行装饰器返回的对象)。
2.  《组态公约》。

Desventajas:

1.  不如`docopt`多用途。**