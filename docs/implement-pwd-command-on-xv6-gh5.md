# 在 xv6 上执行“pwd”命令

> 原文：<https://dev.to/tyfkda/implement-pwd-command-on-xv6-gh5>

刚开始玩 [xv6](https://github.com/mit-pdos/xv6-public) ，Unix 版本 6 的重新实现。它可以很容易地构建和运行。

我认为实现一些命令是一个很好的实践，所以我决定添加一个简单的命令，`pwd`。它可以在纯用户域中进行，并且不需要额外的系统调用。

### 提取 xv6 上的文件信息

参照 [ls.c](https://github.com/mit-pdos/xv6-public/blob/master/ls.c) ，可以使用 [`stat`](https://github.com/mit-pdos/xv6-public/blob/master/ulib.c#L71) 函数提取文件信息( [struct `stat`](https://github.com/mit-pdos/xv6-public/blob/master/stat.h#L5) )。

也可以用目录路径、 [`read`](https://github.com/mit-pdos/xv6-public/blob/master/user.h#L10) 内容为 [struct `dirent`](https://github.com/mit-pdos/xv6-public/blob/master/fs.h#L53) 来枚举调用 [`open`](https://github.com/mit-pdos/xv6-public/blob/master/user.h#L14) 函数的目录条目。

### 找到绝对路径

我不熟悉 Unix 的概念，所以在找代码，看来 [struct `inode`](https://github.com/mit-pdos/xv6-public/blob/master/file.h#L13) (及其`inum`，inode 号)是文件系统的关键部分。但有趣的是，它没有自己的文件名。要获得文件名，你必须枚举它的目录并搜索`inum`。

要得到绝对路径，你必须递归地得到文件名，直到到达根目录。

### 代码

下面是`main`函数:

```
#include "types.h"
#include "fcntl.h"
#include "fs.h"
#include "stat.h"
#include "user.h" 
#define NULL   ((void*)0)
#define FALSE  (0)
#define TRUE   (1) 
#define PATH_SEPARATOR   "/" 
static int getcwd(char* resultPath);
static char* goUp(int ino, char* ancestorPath, char* resultPath);
static int dirlookup(int fd, int ino, char* p);

int main(int argc, char *argv[]) {
  char resultPath[512];
  if (getcwd(resultPath))
    printf(1, "%s\n", resultPath);
  else
    printf(2, "pwd failed");
  exit();
} 
```

Enter fullscreen mode Exit fullscreen mode

`getcwd`函数将当前工作目录返回给定的缓冲区:

```
static int getcwd(char* resultPath) {
  resultPath[0] = '\0';

  char ancestorPath[512];
  strcpy(ancestorPath, ".");

  struct stat st;
  if (stat(ancestorPath, &st) < 0)
    return FALSE;

  char* p = goUp(st.ino, ancestorPath, resultPath);
  if (p == NULL)
    return FALSE;
  if (resultPath[0] == '\0')
    strcpy(resultPath, PATH_SEPARATOR);
  return TRUE;
} 
```

Enter fullscreen mode Exit fullscreen mode

`ancestorPath`保存祖先的相对路径。就变成了`"."`、`"./.."`、`"./../.."`，...并且用来传递给`stat`函数。

`goUp`函数转到`ancestorPath`的父目录，找到绝对路径，递归:

```
static char* goUp(int ino, char* ancestorPath, char* resultPath) {
  strcpy(ancestorPath + strlen(ancestorPath), PATH_SEPARATOR "..");
  struct stat st;
  if (stat(ancestorPath, &st) < 0)
    return NULL;

  if (st.ino == ino) {
    // No parent directory exists: must be the root.
    return resultPath;
  }

  char* foundPath = NULL;
  int fd = open(ancestorPath, O_RDONLY);
  if (fd >= 0) {
    char* p = goUp(st.ino, ancestorPath, resultPath);
    if (p != NULL) {
      strcpy(p, PATH_SEPARATOR);
      p += sizeof(PATH_SEPARATOR) - 1;

      // Find current directory.
      if (dirlookup(fd, ino, p))
        foundPath = p + strlen(p);
    }
    close(fd);
  }
  return foundPath;
} 
```

Enter fullscreen mode Exit fullscreen mode

即使你在根目录下，你也可以获得`stat`为父目录而不出错(并且你可以`cd ..`)。如果父目录和当前目录的`ino`相同，似乎你必须检测你是在根目录。

`dirlookup`函数从父目录中查找给定索引节点号的文件名:

```
// @param fd   file descriptor for a directory.
// @param ino  target inode number.
// @param p    [out] file name (part of absPath), overwritten by the file name of the ino.
static int dirlookup(int fd, int ino, char* p) {
  struct dirent de;
  while (read(fd, &de, sizeof(de)) == sizeof(de)) {
    if (de.inum == 0)
      continue;
    if (de.inum == ino) {
      memmove(p, de.name, DIRSIZ);
      p[DIRSIZ] = '\0';
      return TRUE;
    }
  }
  return FALSE;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 建立并添加到文件图像

在 xv6 上，可以很容易地将额外命令添加到图像文件中。用命名约定修改 Makefile 中的 [UPROGS，添加`_pwd`到其中。](https://github.com/mit-pdos/xv6-public/blob/master/Makefile#L161)

### 测试

```
$ pwd
/
$ mkdir foo
$ mkdir foo/bar
$ cd foo/bar
$ /pwd
/foo/bar 
```

Enter fullscreen mode Exit fullscreen mode

我在`/foo/bar`用`pwd`的时候误打误撞。xv6 好像没有环境变量，在 shell 中搜索路径:

```
$ pwd  # <= without '/' causes exec error, because cannot find the command.
exec: fail
exec pwd failed 
```

Enter fullscreen mode Exit fullscreen mode

你必须在 xv6 上添加`/`来指出精确的命令路径。