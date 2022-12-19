# 检查目录和文件大小的有用命令

> 原文：<https://dev.to/pavlosisaris/useful-commands-to-check-directory-size-1gkj>

通常情况下，需要检查项目中目录或文件的大小，或者评估目录在 live/development 服务器或本地机器上占用的空间。

这里有一个有用的命令列表，你可以利用它来确保你的机器的可用磁盘空间是正常的，并且你的项目的巨大的`node_modules`目录大小没有变得疯狂:D

### 我们将使用 [du 命令](https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/)，解释如下:

```
du (disc usage) command estimates file_path space usage

The options -sh are (from man du):

  -s, --summarize
         display only a total for each argument

  -h, --human-readable
         print sizes in human readable format (e.g., 1K 234M 2G) 
```

要检查多个目录并查看总数，请使用 du -sch:

```
 -c, --total
         produce a grand total 
```

### 那么，让我们来看看基本命令:

以可读格式显示目录的大小:

```
$ du -sh directory/ 
```

显示所有目录及其大小的列表，按大小排序:

```
$ du -sh * | sort -h 
```

我的示例颤振项目中的示例:

```
paul@paul-Inspiron-N5110:~/projects/flutter_test$ du -sh * | sort -h
4,0K    android.iml
4,0K    flutter_test_android.iml
4,0K    flutter_test.iml
4,0K    pubspec.yaml
4,0K    README.md
8,0K    test
12K lib
200K    android
212K    ios 
```

另外，如果你想包含隐藏的文件和目录，你可以运行:

```
$ du -sch .[^.]* * |sort -h 
```

### 奖金:

输出系统分区的总大小和可用大小

```
$ df -h 
```

输出您系统中的 [inodes](https://en.wikipedia.org/wiki/Inode) 使用量:

```
$ df -i 
```

如果你有更多有用的命令要分享，请在下面的评论中留下吧！