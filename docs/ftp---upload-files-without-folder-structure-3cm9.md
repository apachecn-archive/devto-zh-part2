# FTP -上传没有文件夹结构的文件

> 原文：<https://dev.to/brokylabs/ftp---upload-files-without-folder-structure-3cm9>

希尔

如果我的问题与编程无关，请原谅。

我有一些文件。全部分成几个月文件夹。
我计划通过 FTP 上传文件，但没有文件夹结构。FPT 文件夹中的文件将只存在于一些本地文件夹中。

src:

# 我的文件夹

```
# Jan
    - File Jan 1
    - File Jan 2
# Feb
    - File Feb 1
    - File Feb 2
# Mar
    - File Mar 1
    - File mar 2
# Apr
    - File Apr 1
    - File Arp 2 
```

Enter fullscreen mode Exit fullscreen mode

Dest / FTP Folder:

# 我的文件夹

```
- File Jan 1
- File Jan 2
- File Feb 1
- File Feb 2
- File Mar 1
- File mar 2
- File Apr 1
- File Arp 2 
```

Enter fullscreen mode Exit fullscreen mode

有没有可以做到这一点的 FTP 应用程序？