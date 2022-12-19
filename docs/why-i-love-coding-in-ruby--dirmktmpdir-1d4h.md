# 为什么我喜欢用 Ruby 编码:Dir.mktmpdir

> 原文：<https://dev.to/burdettelamar/why-i-love-coding-in-ruby--dirmktmpdir-1d4h>

因为街区！

这段代码创建一个临时目录和一个文件`foo.txt`，其中:

```
require 'tmpdir'

Dir.mktmpdir do |dir|
  open("#{dir}/foo.txt", 'w') do |file|
    # Do something with the file.
  end
end 
```

当程序块退出时，目录及其内容*被自动删除*。

很简单！