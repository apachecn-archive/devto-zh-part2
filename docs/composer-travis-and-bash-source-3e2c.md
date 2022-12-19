# 作曲家，特拉维斯和巴什来源

> 原文：<https://dev.to/kasparsd/composer-travis-and-bash-source-3e2c>

今天我了解到 Composer [使用](https://github.com/composer/composer/blob/ea78712822e2efecf177cdd01f5c001587b47e8e/src/Composer/Util/ProcessExecutor.php#L65-L68)的 [Symphony 进程组件](https://github.com/symfony/process)来执行定义为 [Composer 脚本](https://getcomposer.org/doc/articles/scripts.md)的 shell 命令。这个[内部调用](https://github.com/symfony/process/blob/4f10286a128df6b60b3706752e11f6227fcac844/Process.php#L304) `proc_open()` [，后者](https://www.daemon-systems.org/man/popen.3.html)“通过创建双向管道、分叉和调用 shell 来打开一个进程”。

我在使用 Ubuntu 14.04.5 LTS 版和 PHP 5.6 版的 Travis 版本上失败了

```
{  "scripts":  {  "test":  "source scripts/test.sh"  }  } 
```

出现以下错误:

```
sh: 1: source: not found 
```

原来 [Ubuntu Trusty 上的`/bin/sh`是](https://travis-ci.org/xwp/wp-dev-lib/jobs/385635734#L1011)到`/bin/dash` :
的符号链接

```
$ ls -lah /bin/sh lrwxrwxrwx 1 root root 4 Feb 19 2014 /bin/sh -\> dash 
```

而`source`命令是[一个 dash 内置的](https://ss64.com/bash/source.html)，这在 dash 中是完全没有的。解决方案是用`. something.sh`替换`source something.sh`的所有实例(注意点和脚本文件名之间的空格)。