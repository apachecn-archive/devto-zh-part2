# 新的排序算法:mkdir 排序

> 原文：<https://dev.to/o2sh/new-sorting-algorithm-mkdir-sort-dnm>

我为了好玩做的一个小排序算法。

```
#!/bin/bash
function f() {
    echo $(eval printf 'g/%.0s' {1.."$1"})"$1" | xargs mkdir -p
}

while [ -n "$1" ]
do f "$1"
    shift done find . -type d | sed 's#.*/##' | grep -v g

#./mkdirsort.sh 9 2 7 4 6 
```

Enter fullscreen mode Exit fullscreen mode

这就像使用 FS 的“b 树排序”

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)T1】o2sh/[mkdir-sort](https://github.com/o2sh/mkdir-sort)

### 使用 mkdir 的排序算法

<article class="markdown-body entry-content container-lg" itemprop="text">

# mkdir-排序

使用 mkdir 的排序算法

## 例子

```
$ mkdir wkdir
$ cd wkdir
$ ./mkdirsort.sh 9 2 7 4 6
$ rm -rf g/
```

Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/o2sh/mkdir-sort)