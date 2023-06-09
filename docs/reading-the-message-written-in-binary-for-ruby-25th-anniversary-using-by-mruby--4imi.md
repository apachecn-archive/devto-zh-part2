# 阅读 mruby 用二进制写的 Ruby 25th 周年纪念信息。

> 原文：<https://dev.to/kjunichi/reading-the-message-written-in-binary-for-ruby-25th-anniversary-using-by-mruby--4imi>

你好，我发现了这条推文。

> ![Mohsen Mostafa Jokar profile image](img/a7e7f6e88fc4cb6665336f8083a71678.png)

我想知道写的是什么，我试着用 mruby 来阅读。

## 代码

看起来二进制代码是 ascii 代码。

我用 mruby 写了解码二进制代码。

```
rawData = "01001001 00100000 01001100 01101111 01110110 01100101 00100000 01010010 01110101 01100010 01111001 00101110"
puts rawData.split(" ").map{|c| c.to_i(2)}.map{|c| c.chr("UTF-8")}.join 
```

Enter fullscreen mode Exit fullscreen mode

## 结果

```
bin/mirb 
mirb - Embeddable Interactive Ruby Shell

> rawData = "01001001 00100000 01001100 01101111 01110110 01100101 00100000 01010010 01110101 01100010 01111001 00101110"
 => "01001001 00100000 01001100 01101111 01110110 01100101 00100000 01010010 01110101 01100010 01111001 00101110"
> puts rawData.split(" ").map{|c| c.to_i(2)}.map{|c| c.chr("UTF-8")}.join
I Love Ruby.
 => nil
> 
```

Enter fullscreen mode Exit fullscreen mode

我注意到 Ruby 有 putc，而 mruby 没有。

## 后记

说实话，我第一次用 JavaScript 写解码代码。但是我认为这是一个学习 mruby 的机会，所以我用 mruby 写了这篇文章。

# 链接

*   [https://github.com/mruby/mruby](https://github.com/mruby/mruby)