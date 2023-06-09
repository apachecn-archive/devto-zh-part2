# 确保 shell 脚本只运行一次

> 原文：<https://dev.to/rrampage/ensuring-that-a-shell-script-runs-exactly-once-3d3f>

很多时候，我们有 shell 脚本来执行一些重要的事情，比如插入数据库、发送报告等，我们希望只运行一个实例。

### 回车锁定！

一个简单的解决方案是创建一个“锁文件”，并在脚本启动时检查该文件是否存在。如果文件已经创建，这意味着该程序的另一个实例正在运行，所以我们可能会失败，并显示消息“稍后再试！”。一旦脚本完成运行，它将清理并删除锁文件。

```
LOCK_FILE=a.lock
if [ -f "$LOCK_FILE" ]; then
    # Lock file already exists, exit the script
    echo "An instance of this script is already running"
    exit 1
fi
# Create the lock file
echo "Locked" > "$LOCK_FILE"

# Do the normal stuff

# clean-up before exit
rm "$LOCK_FILE" 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很有希望，但这种方法存在一些问题。如果脚本没有正确结束会发生什么，例如，在它到达代码的清理部分之前，由于一些失败而退出？还是用`Ctrl+C`或`kill`命令强制终止？在这两种情况下，创建的锁文件都不会被删除。因此，下次运行脚本时，您总是会得到一个错误，并且必须手动删除该文件。

上面的代码还有另一个更微妙的错误。一个竞赛条件。如果两个脚本实例几乎同时启动，那么它们都有可能通过`if [ -f "$LOCK_FILE" ]`,因为第二个实例可能在第一个实例能够创建锁文件之前到达代码的那个部分。因此，我们有不止一个实例在运行。

### 更好的锁！

有没有办法创建一个锁文件，它对竞争条件和非标准终止(`Ctrl+C`、`kill`命令等)更加健壮？Linux 为`flock`提供了一个从 shell 脚本管理锁的工具。使用`flock`，我们可以将上面的代码片段改写如下:

```
LOCK_FILE=a.lock
exec 99>"$LOCK_FILE"
flock -n 99 || exit 1
# Do stuff and exit! 
```

Enter fullscreen mode Exit fullscreen mode

`exec 99>"$LOCK_FILE"`创建一个编号为 99 的文件描述符，并将其分配给`LOCK_FILE`。[文件描述符(fd)](https://en.wikipedia.org/wiki/File_descriptor) 0、1、2 分别用于`stdin`、`stdout`、`stderr`。我们正在创建一个高编号的新 fd，以确保它不会与脚本稍后打开的编号 FD 冲突。

做两件事。首先，它在引用我们的`LOCK_FILE`的文件描述符 99 上获得一个`exclusive`锁。linux 内核保证这个操作是原子性的。其次，如果它未能获得锁，它将退出并返回代码 1。我们不需要担心任何清理工作。`flock`当脚本退出时会自动释放锁，而不管它是如何终止的。这解决了我们的问题！

如果我想添加更多的信息性消息，而不是在获取锁失败时直接退出，该怎么办？我们可以将行`flock -n 99 || exit 1`更改如下:

```
flock -n 99
RC=$?
if [ "$RC" != 0 ]; then
    # Send message and exit
    echo "Already running script. Try again after sometime"
    exit 1
fi 
```

Enter fullscreen mode Exit fullscreen mode

flock [手册页](http://man7.org/linux/man-pages/man1/flock.1.html)提供了一个例子，您可以使用它在任何 shell 脚本的开头添加一个排他锁:

```
[ "${FLOCKER}" != "$0" ] && exec env FLOCKER="$0" flock -en "$0" "$0" "$@" || : 
```

Enter fullscreen mode Exit fullscreen mode

这个样板文件使用脚本文件本身作为锁。它的工作方式是将环境变量`$FLOCKER`设置为脚本文件名，并在获得锁后使用其原始参数执行脚本。但是，如果失败，它不会打印任何内容，并且会自动退出。

这里代表脚本的名称。`$@`代表调用时传递给脚本的所有参数。

### 为我用例

我的团队使用一台测试机器，在那里我们部署了一个代码库的多个分支。我们需要确保在特定的时间只有一个人在构建项目。部署脚本从`git`中提取指定的代码分支，构建项目，部署主服务并启动辅助服务。脚本需要一些时间来执行。如果有人试图在构建过程中部署另一个分支，两个分支都可能失败。

使用上面的代码片段，多次调用脚本会显示当前正在构建的分支，并以失败退出。

### 进一步阅读:

*   Flock [手册页](http://man7.org/linux/man-pages/man1/flock.1.html)
*   [创建锁文件](http://mywiki.wooledge.org/BashFAQ/045)的陷阱，就像我们最初的代码片段一样