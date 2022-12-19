# 调试时增加测试超时

> 原文：<https://dev.to/marzelin/increasing-test-timeout-in-jest-while-debugging-2p9l>

当调试代码时，脚本执行会花费更多的时间，这有时会导致非常奇怪的错误，当 asyc 测试由于超过`jest.setTimeout`中指定的时间而失败时，会导致下一个测试执行，而前一个测试仍在后台运行。

幸运的是，我们可以使用`--setupTestFrameworkScriptFile`选项更改默认时间，该选项应该指向一个文件，我们在其中更改超时:

```
jasmine.DEFAULT_TIMEOUT_INTERVAL = 10 * 60 * 1000; 
```

我们可以把这个文件放在主目录中，然后在任何地方用一点点命令行魔法运行 jest，改变超时时间:

```
node --inspect-brk node_modules/.bin/jest --runInBand --setupTestFrameworkScriptFile="${HOME}/test-setup.js" 
```

另一件很酷的事情是，如果我们的项目中也有一个安装文件，那么两个文件都会被使用(但是通过命令行选项给出的文件中的值优先)。