# 我做了一个在命令行上快速记笔记的工具。

> 原文：<https://dev.to/nasa/i-made-tool-for-taking-notes-fast-on-the-command-line-10og>

你好，我是美国宇航局。我想你看标题就能明白，我做了一个备忘录工具。

我通常在命令行上写笔记。然而，我注意到备忘录的管理很麻烦。所以我决定开发一个工具，让管理备忘录变得简单。

源代码在这里。
[https://github.com/k-nasa/rmemo](https://github.com/k-nasa/rmemo)

[![asciicast](img/18e4037468499b854e8c80deaeeb3cfc.png)T2】](https://asciinema.org/a/F65yWTfdnMyNgiyFhLjGO3mRZ)

## 安装

### macOS 上的

为了向家酿官方发布，我必须收集 50 颗星星。(给我星星)

```
brew tap k-nasa/homebrew-rmemo
brew install rmemo 
```

### 来源于

```
cargo install rmemo 
```

## 用法举例

创建新便笺

```
$rmemo new
Title: 
```

如果您已经设置了一个模板，则基于该模板创建一个便笺

```
$rmemo new -t
Title: 
```

删除与参数模式
匹配的文件名的注释

```
rmemo delete hoge
hoge
2018-10-15hoge.md
2018-10-12hoge.md
2018-10-08hoge.md
Will delete those entry. Are you sure?
Are you sure?(y/n) : 
```

如果你现在想写下想法，你可以使用快捷命令

```
$rmemo quick idea 
```

```
$rmemo list
/Users/hoge/.config/rmemo/memos
│  hoge
│  └── hoge.md
│  test
│  └── test.md
├── 2018-10-16indicatif_memo.md
├── hgoehgoe.md
├── 2018-10-12iii.md 
```

## 配置

```
memos_dir = "/Users/asan/.config/rmemo/memos"  # Directory where note is stored
editor = "nvim"                                # The editor you want to use. I recommend nvim for no particular reason
selector = "fzf"                               # Selector you want to use. Please choose your favorite one
grep_command = "grep"                          # Set your favorite grep
template_file_path = ""                        # Set the template you want to use
enter_time_in_filename = true                  # Set it to false if timestamp is not required for file name 
```