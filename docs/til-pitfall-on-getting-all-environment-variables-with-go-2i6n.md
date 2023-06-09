# TIL:获取 Go 中所有环境变量的陷阱

> 原文：<https://dev.to/rhysd/til-pitfall-on-getting-all-environment-variables-with-go-2i6n>

你好，这是我在 DEV 的第一篇文章。到🐶

为了获得 Go 中所有环境变量的列表，我在标准库中找到了一个函数

*   [T2`os.Environ()`](https://golang.org/pkg/os/#Environ)

> Environ 以“key=value”的形式返回表示环境的字符串的副本。

使用这个函数，我试图编写一个函数来获取`map[string]string`中的所有环境变量键值对。

## 坏榜样😣

我首先编写了以下代码。

```
kv := os.Environ()
m := make(map[string]string, len(kv))
for _, s := range kv {
    if idx := strings.IndexRune(s, '='); idx >= 0 {
        m[s[:idx]] = s[idx+1:]
    }
}

// `m` is what I wanted! 
```

这个例子一开始看起来很好，但是后来我发现在某些情况下它不仅仅在 Windows 上工作。

我发现`m`包含空字符串键`""`，并逐行检查`os.Environ()`的输出。最后我找到了原因。

```
=C:=C:\path\to\current\dir 
```

这是什么？但是环境变量`$=C:`确实存在于系统中。这是一个隐藏的环境变量，没有在`set`命令的输出中列出。

我挖了 Go 标准库，找到了一个评论(谢谢 [@mattn](https://dev.to/mattn) 帮我找出这个！).

[https://github . com/golang/go/blob/50 BD 1 C4 EB 4 fac 8 ddeb 5 f 063 c 099 daccfb 71 b26/src/sys all/env _ windows . go # l58-l60](https://github.com/golang/go/blob/50bd1c4d4eb4fac8ddeb5f063c099daccfb71b26/src/syscall/env_windows.go#L58-L60)

> 环境变量可以从=
> 开始，所以开始寻找 j=1 处的分隔符=。
> [https://blogs . msdn . com/b/old new thing/archive/2010/05/06/10008132 . aspx](https://blogs.msdn.com/b/oldnewthing/archive/2010/05/06/10008132.aspx)

评论里的帖子，[这些奇怪的=C:环境变量是什么？](https://blogs.msdn.com/b/oldnewthing/archive/2010/05/06/10008132.aspx)解释了什么是`$=C:`。

> 好的，那么命令处理器设置了这些东西，但是它们是什么呢？它们是命令处理器试图模仿旧的 MS-DOS 方式处理驱动器和目录时留下的。在 Win32 中，只有一个当前目录，而在 MS-DOS 中，每个驱动器都有一个当前目录。

所以这是一个隐藏的特殊环境变量，我需要在获取所有环境变量时考虑它。

## 好例子😆

幸运的是，修复很容易。`=`是第一个字符，环境变量名决不能为空。所以我们可以忽略第一个字符。

```
kv := os.Environ()
m := make(map[string]string, len(kv))
for _, s := range kv {
    // On Windows, environment variable names may start with '=' (e.g. =C:).
    // To handle the variables properly, skip first character of KEY=VALUE line.
    for i := 1; i < len(s); i++ {
        if s[i] == '=' {
            m[s[:i]] = s[i+1:]
            break
        }
    }
}

// `m` is what I wanted! 
```