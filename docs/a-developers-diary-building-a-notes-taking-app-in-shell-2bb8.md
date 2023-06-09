# 开发者日记:在 Shell 中构建笔记应用

> 原文：<https://dev.to/alexisjanvier/a-developers-diary-building-a-notes-taking-app-in-shell-2bb8>

***注:本帖原帖于 marmelab.com 的[。](https://marmelab.com/blog/2018/11/08/a-developers-diary.html)***

作为一名开发人员，我真正喜欢的一点是你一直在学习:一个模式、一个库、一个晦涩的配置技巧...在激烈的行动中，你很高兴，但几天后，你经常会忘记。正是在这些时候，你认为记笔记是个好主意。

我已经试过一些记事本: [jrnl](http://jrnl.sh/) ，但是我从来都记不住命令， [boostnote](https://boostnote.io/) ，我编码的时候不用，因为它是一个额外的窗口，或者 [gist](https://gist.github.com/) ，但是我不能让它有条理...

而今年夏天，我在我的 [changelog](https://changelog.com/) 简讯中收到了这个链接[“did . txt 文件”](https://theptrk.com/2018/07/11/did-txt-file/)文件。

## did.txt

帕特里克是这样介绍他的博文的:

> 目标:创建一个终端可访问的极其简单的“did”文件

而且没错，很简单(就是在你的`.bash_profile`或者`.zshrc`里加个别名而已)而且血腥有效:

```
alias did="vim +'normal Go' +'r!date' ~/did.txt" 
```

Enter fullscreen mode Exit fullscreen mode

一个`did`命令在终端打开一个文件——这样你就不会离开你的工作环境——上面有当前的日期。你要做的就是把你刚刚学到的这个小东西写下来。

[![did : the original](img/7accae7297a44ae373e393e959ff544e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IszZUKO9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c87itq826fcqq2ahxe1a.gif)

我真的很喜欢这样一个想法，即只用系统中已有的东西来构建一个新工具。但其实有点太简单了。例如，如果您在同一天使用 did 两次，会发生以下情况:

[![Maybe too simple](img/42f1f80914e2aae841167935245a686a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eD252Acf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ks874lacezl2lumxz2fe.gif)

有两个问题让我认为我不会将这个命令集成到我的日常工作中:

*   **所有笔记都在一个文件**中，由于 did 是一个日常笔记工具，这个文件可能会变得太长而不可用。做笔记的意义在于能够再读一遍！
*   **文件采用`.txt`格式**，这严重限制了格式化的可能性，比如代码摘录。

这篇文章记录了我如何根据自己的需要定制这个好主意。我试图保持和原来一样的简单性，继续只使用终端中已经可用的东西。

## 每周一份日志

我的工作时间是两周(sprint ),所以将单个文件分成几个每周日志是显而易见的。

我不会深入讨论实现的细节，而是向您展示(几乎)最终的结果。`--help`选项、`man`和谷歌是我的朋友得到这个结果。

```
export DID_PATH=~/.did

function did(){
    export LC_ALL=C
    if [ ! -f ${DID_PATH}/$(date +%Y-%V).txt ]; then echo "Week $(date +"%V (%B %Y)")  \n\n$(date +"%A %Y-%m-%d")" > ${DID_PATH}/$(date +%Y-%V).txt
    fi FILE_EDITION_DATE="$(stat -c "%y" ${DID_PATH}/$(date +%Y-%V).txt)"
    NOW="$(date +"%Y-%m-%d")"
    if [ ${FILE_EDITION_DATE:0:10} != ${NOW} ]; then echo "\n$(date +"%A %Y-%m-%d")\n" >> ${DID_PATH}/$(date +%Y-%V).txt
    fi unset LC_ALL
    vim +'normal Go' ${DID_PATH}/$(date +%Y-%V).txt
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是我认为重要的几点。

*   **函数而不是别名**:随着逻辑`if the log exists, then, else`的引入，有必要用 shell 函数来代替简单的别名。`if [ ! -f ${DID_PATH}/$(date +%Y-%V).txt ]; then`

*   **命令**:这是我测试最多的命令。这里它只是用来格式化当前日期。比如`date +%Y-%V`。

*   **`stat`命令**:允许检索一个文件的很多信息，比如最后修改的日期`stat -c "%y" ${DID_PATH}/$(date +%Y-%V).txt`。这让我知道该文件是否在当天被编辑过，以决定是否在日志文件打开时添加这个日期。

*   **终端区域设置**:`date`命令对终端区域设置敏感。所以我有好几个月都在学法语。是的，我的系统是法语的！为了能够用英语记录我的笔记，有必要在使用`LC_ALL=C`执行命令的过程中更改终端的语言环境。

*   **环境变量`DID_PATH`** :这个变量很有逻辑。它简化了脚本编写，并允许轻松地更改存储文件夹。但是它有一个很大的副作用:通过使用 [direnv](https://direnv.net/) ，它将允许你为每个项目创建一个日志！

[![the new did](img/236c0a7d77beae19f72be1f23ae564e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dGnQMKD3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nvj5gvy3d6nys7wohg3b.gif)

这个新命令完成了这项工作，因为现在它每周创建一个文件，而不是一个文件。但是这种改进也可以作为 David Kadavy 的文章[“复杂性令人毛骨悚然:它从来不只是多一件事”](https://medium.com/@kadavy/complexity-is-creepy-its-never-just-one-more-thing-79a6a89192db)的一个很好的例子。

的确，我的*还有一件事*带来了它的问题:

*   用原来的`did`，总是打开同一个文件。但是现在`did`打开了本周的日志。**我如何查看上周的笔记？**
*   如果我想打开过去的日志，**我如何知道哪些日志存在**？
*   使用原始的`did`，我可以在我的单个文件中使用`vim`进行搜索。但是现在，**我怎么才能在所有的航海日志中找到一张纸条呢？**

## 查看具体日志:didv(查看)

```
function didv(){
    if [ $1 ]
    then cat ${DID_PATH}/${1}.txt
    else
        if [ ! -f ${DID_PATH}/$(date +%Y-%V).txt ]; then LC_ALL=C echo "# Week $(date +"%V (%B %Y)")  \n\n## $(date +"%A %Y-%m-%d")" > ${DID_PATH}/$(date +%Y-%V).txt
        fi cat ${DID_PATH}/$(date +%Y-%V).txt
    fi
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数比`did`的简单，但是它引入了命令参数的使用:`if [ $1 ]`。`didv`打开当前日志，`didv 2018-32`打开第 32 周的日志。

`cat`负责显示文件。

[![Display logbooks with didv](img/0da2a399575db7dc1d21f00470569112.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qoaZLCeC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o5fy2e4wzsi2anozozmv.gif)

## 列出每周日志:didl(列表)

我认为设置日志列表是最快的功能。我实际测试了`ls`和`tree`命令:

[![list logs with ls and tree](img/6ebcf9ddca2c6cf104d6cf820e8e1267.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LYi4McDI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/77uqm60wt45s4godp224.gif)

但是有两件事困扰着我:

*   我不想显示文件扩展名(例如我想用`2018-32`代替`2018-32.txt`)，
*   我想显示对应于周数的月份，使列表更易读。

[用`date`显示从第](https://en.wikipedia.org/wiki/ISO_week_date#Calculating_the_week_number_of_a_given_date)周开始的月份是`did`改进日最复杂的部分！

```
function week2Month(){
    export LC_ALL=C
    year=$(echo $1 | cut -f1 -d-)
    week=$(echo $1 | cut -f2 -d-)
    local dayofweek=1 # 1 for monday
    date -d "$year-01-01 +$(( $week * 7 + 1 - $(date -d "$year-01-04" +%w ) - 3 )) days -2 days + $dayofweek days" +"%B %Y"
    unset LC_ALL
}

function didl(){
    for file in `ls ${DID_PATH}/*.txt | sort -Vr`; do filenameRaw="$(basename ${file})"
        filename="${filenameRaw%.*}"
        echo "${filename} ($(week2Month ${filename}))"
    done
} 
```

Enter fullscreen mode Exit fullscreen mode

[![didl](img/11c17b69becf904b601d0991cff25ddb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7lJtVJM1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qdslsss80fysyjm1cn5d.gif)

## 搜索每周日志:dids(搜索)

这里是我们要实现的最后一个特性:搜索日志。涉及到的是`grep`。

```
function dids(){
    export LC_ALL=C
    if [ $1 ]
    then
        for file in `ls ${DID_PATH}/*.txt | sort -Vr`; do NB_OCCURENCE="$(grep -c @${1} ${file})"
            if [ ${NB_OCCURENCE} != "0" ]
            then filenameRaw="$(basename ${file})"
                filename="${filenameRaw%.*}"
                echo -e "\n\e[32m=> ${filename} ($(week2Month ${filename}), ${NB_OCCURENCE} results) \e[0m" && grep -n -B 1 ${1} ${file}
            fi
        done
    else echo "You must add a something to search..."
    fi export LC_ALL=C
} 
```

Enter fullscreen mode Exit fullscreen mode

为了能够标记笔记并将搜索限制在这些标签上，我决定使用标签的前缀`@`，允许做`NB_OCCURENCE="$(grep -c @${1} ${file})"`。第二次使用`grep`不再使用这个前缀，允许显示与被搜索单词对应的所有行。

[![dids](img/6ce5669ac07b7151a0fbda0488a9417a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h6kzaHPB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9vygyzclki8j0y1mzjmg.gif)

## 格式化注释

我离目标很近了！我不再有一个，而是四个命令:

*   `did`打开当前日期的当前日志；
*   `didv`要查看包括以前日志的日志，
*   为了以可读的方式列出所有可用的日志，
*   在所有日志中进行搜索。

只有一点仍然悬而未决:

> 该文件是. txt 格式，这严重限制了格式化的可能性，如代码摘录。

一种标记语言非常适合这种情况:**。**

 **[![Markdown everywhere](img/64318cd6805ff1352aecb4f66a934960.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pk_SfsVW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ojyi4j0nizkt4ols9mxb.jpg)

不幸的是，终端中没有处理和显示`.md`文件的基本工具。然而，我给自己定了一个规则:

> *...，并继续仅使用终端中已经可用的内容*

没关系，我是个小混混。

所以我找到了一些符合需求的项目:

*   [潘多克和猞猁](https://tosbourn.com/view-markdown-files-terminal/)
*   [mdv](https://github.com/axiros/terminal_markdown_viewer)
*   [vmd](https://github.com/cpascoe95/vmd)

我更喜欢`vmd`的渲染。剩下的就是将所有的`.txt`修改为`.md`，添加一些`#`，并将`didv`函数中的`cat`替换为`vmd`。

[![didv in markdown](img/cda2e0bd7dc7140ada0de431b7337dcd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZCWsmGzV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5n36sf7a63aj72dms3ck.gif)

## 最终脚本

```
# What did i do today?
# from https://theptrk.com/2018/07/11/did-txt-file/
export MDV_THEME=729.8953
export DID_PATH=~/.did

function did(){
    export LC_ALL=C
    mkdir -p ${DID_PATH}
    if [ ! -f ${DID_PATH}/$(date +%Y-%V).md ]; then echo "# Week $(date +"%V (%B %Y)")  \n\n## $(date +"%A %Y-%m-%d")" > ${DID_PATH}/$(date +%Y-%V).md
    fi FILE_EDITION_DATE="$(stat -c "%y" ${DID_PATH}/$(date +%Y-%V).md)"
    NOW="$(date +"%Y-%m-%d")"
    if [ ${FILE_EDITION_DATE:0:10} != ${NOW} ]
    then echo "\n## $(date +"%A %Y-%m-%d")\n" >> ${DID_PATH}/$(date +%Y-%V).md
    fi unset LC_ALL
    vim +'normal Go' ${DID_PATH}/$(date +%Y-%V).md
}

function didv(){
    if [ $1 ]
    then vmd ${DID_PATH}/${1}.md
    else mkdir -p ${DID_PATH}
        if [ ! -f ${DID_PATH}/$(date +%Y-%V).md ]; then LC_ALL=C echo "# Week $(date +"%V (%B %Y)")  \n\n## $(date +"%A %Y-%m-%d")" > ${DID_PATH}/$(date +%Y-%V).md
        fi vmd ${DID_PATH}/$(date +%Y-%V).md
    fi
}

function week2Month(){
    export LC_ALL=C
    year=$(echo $1 | cut -f1 -d-)
    week=$(echo $1 | cut -f2 -d-)
    local dayofweek=1 # 1 for monday
    date -d "$year-01-01 +$(( $week * 7 + 1 - $(date -d "$year-01-04" +%w ) - 3 )) days -2 days + $dayofweek days" +"%B %Y"
    unset LC_ALL
}

function didl(){
    for file in `ls ${DID_PATH}/*.md | sort -Vr`; do filenameRaw="$(basename ${file})"
        filename="${filenameRaw%.*}"
        echo "${filename} ($(week2Month ${filename}))"
    done
}

function dids(){
    export LC_ALL=C
    if [ $1 ]
    then
        for file in `ls ${DID_PATH}/*.md | sort -Vr`; do NB_OCCURENCE="$(grep -c ${1} ${file})"
            if [ ${NB_OCCURENCE} != "0" ]
            then filenameRaw="$(basename ${file})"
                filename="${filenameRaw%.*}"
                echo -e "\n\e[32m=> ${filename} ($(week2Month ${filename}), ${NB_OCCURENCE} results) \e[0m" && grep -n -B 1 ${1} ${file}
            fi
        done
    else echo "You must add a something to search..."
    fi unset LC_ALL
} 
```

Enter fullscreen mode Exit fullscreen mode

[在 GitHubGist 上获取](https://gist.github.com/alexisjanvier/bfe71d18f68434e29c08637e4d837c74)

## 结论

我不知道我的剧本对你是否有用。如果是的话，我很乐意。否则，无论如何我也会快乐。

因为剧本在这里并不重要。我想在这篇文章中分享的是从你的系统中构建你自己的小工具的乐趣。真的很好玩！在修改最初的 did.txt 的那一天，我学到了很多，测试了很多，得出了一个正是我需要的结果。不多不少。

有点**低发展**的意思。这几天我对 [**低科技**](https://www.arte.tv/fr/videos/RC-016865/les-escales-de-l-innovation/RC-014864/nomade-des-mers-les-tutos/) 非常敏感。

所以我希望这篇文章能给你一些启发。就我而言，我想我要快速添加一个`didp`命令。

你猜到了吗？`p`求发布！现在我在 markdown 中有了日志，在服务器上发布它们，然后添加一个像 [Algolia](https://www.algolia.com/products/search) 这样的搜索引擎来索引它们，应该不会很复杂。**