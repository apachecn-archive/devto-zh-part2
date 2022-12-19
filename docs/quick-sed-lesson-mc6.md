# 快速 sed 课程

> 原文：<https://dev.to/k4ml/quick-sed-lesson-mc6>

我在寻找一种在特定时间范围内“grep”日志的方法。我发现的第一个(从那以后一直使用)是使用 [awk](https://en.wikibooks.org/wiki/An_Awk_Primer) :-

```
awk '/run_cmd/ && "00:00:00" < $3 && $3 <= "04:35:00"' /var/log/local1 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我只想要 5 分钟前的日志呢？从某个时间到现在很容易:-

```
awk '/ERROR/ && "10:40:00" < $3 && $3 <= strftime("%H:%M:%s")' /var/log/local1 
```

Enter fullscreen mode Exit fullscreen mode

但是我找不到用 awk 得到“时间前”的方法。当然，我们可以使用`date`命令来生成日期，并将其传递给 awk:-

```
time_ago=$(date --date='10 minutes ago' '+%H:%M') && awk -v time_ago="$time_ago" '/ERROR/ && time_ago < $3 && $3 <= strftime("%H:%M:%s")' /var/log/local1 
```

Enter fullscreen mode Exit fullscreen mode

然而事情开始失控了。照例， [stackoverflow](https://stackoverflow.com/a/26526710/139870) 有更好的答案。不使用 awk，而是使用 sed。

```
sed -n "/^$(date --date='5 minutes ago' '+%b %_d %H:%M')/,\$p" /var/log/local1 
```

Enter fullscreen mode Exit fullscreen mode

虽然它能工作，但我并不真正理解它。该上一些快速的 sed 课了。

基本 sed - `sed -n 10,15p file.txt`打印文件的第 10 - 15 行。

`10,15`是一个地址范围。地址范围可以是行号或正则表达式模式。

`sed -n /^813/p file.txt`这个功能类似于`grep ^813 file.txt`。

哦，`p`还有一个命令，就是打印。另一个常用命令是用于删除的`d`。

记住地址可以是一个范围。

```
sed -n /^813/, \$p file.txt 
```

Enter fullscreen mode Exit fullscreen mode

这里我们指定了一个范围，它只是文件的结尾。因此 sed 将从匹配开始打印到文件结尾。

```
sed -n "/^$(date --date='5 minutes ago' '+%b %_d %H:%M')/,\$p" /var/log/local1 
```

Enter fullscreen mode Exit fullscreen mode

现在回到最初的命令。将返回类似于 Oct 12 10:14 的内容。

所以基本上我们在做:-

```
sed -n "/^Oct 12 10:14/,\$p" /var/log/local1 
```

Enter fullscreen mode Exit fullscreen mode

这意味着从 10 月 12 日 10:14 的比赛打印到文件结尾。

哦，等等，为什么那里有旗子？sed 默认情况下会打印每一行。所以`-n`是安静的，所以我们指示它不要打印任何东西。相反，我们使用`p`只打印匹配。

课程结束；)