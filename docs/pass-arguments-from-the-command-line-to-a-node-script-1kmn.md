# 从命令行向节点脚本传递参数

> 原文：<https://dev.to/flaviocopes/pass-arguments-from-the-command-line-to-a-node-script-1kmn>

使用
调用 Node.js 应用程序时，可以传递任意数量的参数

```
node app.js 
```

Enter fullscreen mode Exit fullscreen mode

参数可以是独立的，也可以有一个键和值。

例如:

```
node app.js flavio 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
node app.js name=flavio 
```

Enter fullscreen mode Exit fullscreen mode

这将改变您在节点代码中检索该值的方式。

检索它的方法是使用 Node 内置的`process`对象。

它公开了一个`argv`属性，这是一个包含所有命令行调用参数的数组。

第一个参数是`node`命令的完整路径。

第二个元素是正在执行的文件的完整路径。

所有的附加参数都是从第三个位置开始的。

您可以使用一个循环遍历所有参数(包括节点路径和文件路径:

```
process.argv.forEach((val, index) => {
  console.log(`${index}: ${val}`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过创建一个新数组来获得额外的参数，该数组排除了前两个参数:

```
const args = process.argv.slice(2) 
```

Enter fullscreen mode Exit fullscreen mode

如果你有一个没有索引名的参数，比如:

```
node app.js flavio 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用
来访问它

```
const args = process.argv.slice(2)
args[0] 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下:

```
node app.js name=flavio 
```

Enter fullscreen mode Exit fullscreen mode

`args[0]`是`name=flavio`，你需要把它反过来。最好的方法是使用 [`minimist`](https://www.npmjs.com/package/minimist) 库，它有助于处理参数:

```
const args = require('minimist')(process.argv.slice(2))
args['name'] //flavio 
```

Enter fullscreen mode Exit fullscreen mode