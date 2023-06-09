# 使用节点 JS 读取 CSV 文件

> 原文：<https://dev.to/maheshkay/read-csv-file-using-node-js-29oc>

我目前正在做一个商业项目，将处理后的数据导出为 CSV 文件。我想把它处理到仪表板上。我可以选择 python 或任何其他后端语言来读取 CSV 文件。然而，我发现节点 js 可能是更好的选择。我已经有了前端的节点。所以我想如果我能用 node 来处理这个问题。并使其在仪表板中可用，这样我就不必在其他语言上浪费太多时间。

下面是让我开始的简单代码。你也可以查看 [Read CSV with Node JS](https://www.youtube.com/watch?v=ErfEKTrgq5Y&index=4&list=PLUY1lsOTtPeIFpuZEeQH2-5PDn4n3rpO7) 视频，获取替代解释。

查看下面的示例代码，让我开始学习。要扩展下面的代码，还有很多工作要做。比如说，如果我希望从 mysql 中获取数据并将其导出到 CSV，那么我必须将读操作改为写操作。

```
const fs = require('fs'); 
const csv = require('csv-parser');

fs.createReadStream(inputFilePath)
.pipe(csv())
.on('data', function(data){
    try {
        //perform the operation
    }
    catch(err) {
        //error handler
    }
})
.on('end',function(){
    //some final operation
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是我用过的 [CSV 模块](https://www.npmjs.com/package/csv-parser)。我认为有大量的 npm 模块你可以去看看。但是 CSV 解析器似乎很简单，可以完成任务。

对于 CSV 解析，您最喜欢的节点模块是什么？