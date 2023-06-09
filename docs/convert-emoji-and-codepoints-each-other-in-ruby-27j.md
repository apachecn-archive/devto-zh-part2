# 在 Ruby 中相互转换表情符号和代码点

> 原文：<https://dev.to/ohbarye/convert-emoji-and-codepoints-each-other-in-ruby-27j>

下面的技巧是我在实现 gem 时学到的，github_reactions ，总结对 github 问题和拉请求的反应。

## 从表情符号中获取代码点

```
"👍".unpack("U*")
=> [128077]

"👍".codepoints
=> [128077]

# Convert to hexadecimal
"👍".each_codepoint.map {|n| n.to_s(16) }
=> ["1f44d"] 
```

Enter fullscreen mode Exit fullscreen mode

## 从代码点获取表情符号

```
[128077].pack("U*")
=> "👍"

0x1f44d.chr('UTF-8')
=> "👍"

"\u{1f44d}"
=> "👍" 
```

Enter fullscreen mode Exit fullscreen mode