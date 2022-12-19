# 获取节点中的当前文件夹

> 原文：<https://dev.to/flaviocopes/get-the-current-folder-in-node-39h2>

在 Node.js 脚本中引用当前文件夹基本上有两种方法:

*   `./`
*   `__dirname`

> 和`./`一起的还有`../`，指向父文件夹。他们的行为方式是一样的。

这两者有很大的区别。

在节点脚本中使用`__dirname`将返回当前 JavaScript 文件所在的文件夹**的路径。**

使用`./`会给你**当前工作目录**。它将返回与调用 [`process.cwd()`](https://nodejs.org/api/process.html#process_process_cwd) 相同的结果。

最初，当前工作目录是运行 node 命令的文件夹的路径，但是在脚本执行过程中可以通过使用 [`process.chdir()`](https://nodejs.org/api/process.html#process_process_chdir_directory) API 进行更改。

只有一个地方`./`引用当前文件路径，并且是在一个`require()`调用中。在那里，`./`(为了方便起见)将总是引用 JavaScript 文件路径，让您基于文件夹结构导入其他模块。