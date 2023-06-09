# Rust 中嵌套循环的命名

> 原文：<https://dev.to/stevensonmt/naming-nested-for-loops-in-rust-cfp>

我已经意识到 rust 中命名循环的能力有一段时间了，但是最近的一个项目让我考虑如何使代码更可读。它几乎可以代替单独的注释。我的项目采用了一个可变长度的字符串，并寻找关键字集，试图返回最相关的匹配。一些关键词和位置在确定相关性时会具有更大的权重。

```
'clauses: for clause in text.split(";") {
    'phrases: for phrase in clause.split(",") {
        'words: for word in phrase.split_whitespace() {
            'mainkeywords: for mainkey in MAIN_KEYS.iter() {
                match word.find(mainkey) {
                    Some(_) => { dostuff(word);
                                 break 'phrases 
                               },
                    _ => 'secondarykeys: for key in SECONDARY_KEYS.iter() {
                             match key.find(word) {
                                                   Some(_) => do_other_stuff(word);
                                                   break 'words 
                                                  }
                 }
              }
           }
       }
    } 
```

Enter fullscreen mode Exit fullscreen mode

命名循环可以产生与注释相同的效果，同时提供一些有用的功能。我以前从未见过将命名循环作为一种代码组织/可读性工具，尽管我愿意分享。如果有人对这种方法或者上面的代码有任何评论，我很乐意听听。