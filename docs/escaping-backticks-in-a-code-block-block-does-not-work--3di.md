# 在代码块中转义反斜线不起作用

> 原文：<https://dev.to/nektro/escaping-backticks-in-a-code-block-block-does-not-work--3di>

我发誓我没有试图在 markdown 解析器 XD 中寻找错误，但是如果你在一个代码块中对反斜线进行转义，那么它就不起作用了。

我想做的是向某人展示如何格式化代码块，这就是所发生的事情

*   如何繁殖
*   做了 3 次倒勾
*   新行，3 个反斜杠用`/`转义，但反斜杠
*   正电码
*   又有三个逃脱的反斜杠
*   3 个实际的倒勾来关闭区块

完成后的样子可以在下面看到

[![alt](img/d83e839954955d0270e37bf049d7a8c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hlLqeC-4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4g0myzzf0rzg04vspd32.png)

* * *

```
\`\`\`ruby
require "zlib"

def compress_file(file_name)
  zipped = "#{file_name}.gz"

  Zlib::GzipWriter.open(zipped) do |gz|
    gz.write IO.binread(file_name)
  end
end
\`\`\` 
```

Enter fullscreen mode Exit fullscreen mode

* * *

编辑:所以看起来这在 post 中是有效的，但是也包括了反勾号。我第一次遇到的乱码是来自一个评论，所以我也把它贴在那里