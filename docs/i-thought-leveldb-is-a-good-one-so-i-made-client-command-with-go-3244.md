# 我觉得 LevelDB 是个不错的，所以用 Go 做了客户端命令。

> 原文：<https://dev.to/yuuichifujioka/i-thought-leveldb-is-a-good-one-so-i-made-client-command-with-go-3244>

突然，我寻找可以像 SQLite 一样作为库使用的 KVS，在 LevelDB 中找到了。

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) [谷歌](https://github.com/google) / [ leveldb](https://github.com/google/leveldb)

### LevelDB 是 Google 编写的快速键值存储库，它提供了从字符串键到字符串值的有序映射。

<article class="markdown-body entry-content container-lg" itemprop="text">

LevelDB 是 Google 编写的一个快速键值存储库，它提供了从字符串键到字符串值的有序映射。

[![Build Status](../Images/cd003b864749046ef461c4fb96a1f99d.png) ](https://travis-ci.org/google/leveldb) [ ![Build status](../Images/a7b6a5fb260f4e8f8d0ad56d0d1ac36f.png)](https://ci.appveyor.com/project/pwnall/leveldb)

作者:桑杰·格玛瓦特([sanjay@google.com](https://raw.githubusercontent.com/google/leveldb/master/mailto:sanjay@google.com))和杰夫·迪恩([jeff@google.com](https://raw.githubusercontent.com/google/leveldb/master/mailto:jeff@google.com))

# 特征

*   键和值是任意的字节数组。
*   数据按关键字分类存储。
*   调用方可以提供自定义的比较函数来覆盖排序顺序。
*   基本操作有`Put(key,value)`、`Get(key)`、`Delete(key)`。
*   在一个原子批处理中可以进行多个更改。
*   用户可以创建瞬时快照来获得一致的数据视图。
*   数据支持向前和向后迭代。
*   使用 [Snappy 压缩库](https://google.github.io/snappy/)自动压缩数据。
*   外部活动(文件系统操作等。)通过虚拟界面传递，因此用户可以定制操作系统交互。

# 证明文件

[LevelDB 库文档](https://github.com/google/leveldb/blob/master/doc/index.md)在线，与源代码捆绑。

# 限制

*   这不是 SQL 数据库。它…

</article>

[View on GitHub](https://github.com/google/leveldb)

使用 Python 的 LevelDB 库 plyvel 是一种乐趣，但我决定创建一个客户端命令，因为我不想每次都不编写 Python 来检索一个或多个键的列表或删除不必要的键。

这是一个。

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png)[【yuuichi-fuji oka】](https://github.com/yuuichi-fujioka)/[【go-leveldbctl】](https://github.com/yuuichi-fujioka/go-leveldbctl)

### LevelDB 控制命令

<article class="markdown-body entry-content container-lg" itemprop="text">

# leveldbctl

[![Build Status](../Images/b5abb7591e931ae4f9170d4a0980aa24.png) ](https://travis-ci.org/yuuichi-fujioka/go-leveldbctl) [ ![Coverage Status](../Images/b813bc1896dd6de88cd1b44416d86f45.png)](https://coveralls.io/github/yuuichi-fujioka/go-leveldbctl?branch=master)

LevelDB 控制命令。

该命令提供了在 LevelDB 上进行 CRUD 操作的简单方法。

```
$ leveldbctl put foo bar
put foo: bar into ./
$ leveldbctl get foo
bar
```

Enter fullscreen mode Exit fullscreen mode

## 特征

*   初始化 LevelDB
*   将键/值放入 LevelDB
*   用键获取值
*   删除键
*   转储 LevelDB 中的所有键/值
*   打印所有密钥

## 安装

```
$ export GO111MODULE=on
$ go get github.com/yuuichi-fujioka/go-leveldbctl/cmd/leveldbctl
```

Enter fullscreen mode Exit fullscreen mode

## 使用

```
NAME
   leveldbctl - A new cli application
USAGE
   leveldbctl [global options] command [command options] [arguments...]

VERSION:
   0.0.0

COMMANDS:
     init, i    Initialize a LevelDB
     walk, w    Walk in a LevelDB
     keys, k    Search all keys in a LevelDB
     put, p     Put a value into a LevelDB
     get, g     Gut a value from a LevelDB
     delete, d  Delete a value from a LevelDB
     help, h    Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --dbdir value, -d value  LevelDB Directory (default: "./") [$LEVELDB_DIR]
   --hexkey, --xk
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/yuuichi-fujioka/go-leveldbctl)

它可以通过安装 go get 来使用。

```
$ go get github.com/yuuichi-fujioka/go-leveldbctl/cmd/leveldbctl
$ leveldbctl -h
NAME:
   leveldbctl - A new cli application

USAGE:
   leveldbctl [global options] command [command options] [arguments...]

VERSION:
   0.0.0

COMMANDS:
     init, i    Initialize a LevelDB
     walk, w    Walk in a LevelDB
     keys, k    Search all keys in a LevelDB
     put, p     Put a value into a LevelDB
     get, g     Gut a value from a LevelDB
     delete, d  Delete a value from a LevelDB
     help, h    Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --dbdir value, -d value  LevelDB Directory (default: "./") [$LEVELDB_DIR]
   --help, -h               show help
   --version, -v            print the version 
```

Enter fullscreen mode Exit fullscreen mode

## 如何使用

它可以初始化和 CRUD 操作，假设当前目录是 LevelDB 的一个目录。(具体目录可通过选项`--dbdir`或`-d`设置为 LevelDB)

用`init`初始化，用`put`制作，用`get`获取，用`delete`删除。

```
$ leveldbctl init   #  Initialize. Several files are created.
./ is initialized as LevelDB
$ leveldbctl get aaa  # When a key is not exist in db, it is displayed that it cannot be found.
aaa is not found.
$ leveldbctl put aaa bbb  # Put bbb into aaa.
put aaa: bbb into ./.
$ leveldbctl get aaa  # Got bbb
bbb
$ leveldbctl delete aaa  # Delete it.
aaa is deleted 
```

Enter fullscreen mode Exit fullscreen mode

`walk`可以输出所有键值，`keys`可以输出所有键。

```
$ leveldbctl keys
egg
foo
hoge
$ leveldbctl walk
egg: spam
foo: bar
hoge: fuga 
```

Enter fullscreen mode Exit fullscreen mode

## 杂项

*   我在 Mac OS High Sierra 和 Ubuntu 16.04.4 LTS 上进行了测试。。
*   CLI 使用`github.com/urfave/cli`实现。
*   LevelDB 操作使用`github.com/syndtr/goleveldb/leveldb`。