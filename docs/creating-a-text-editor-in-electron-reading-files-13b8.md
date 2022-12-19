# 在 electronic 中创建文本编辑器:第 1 部分-读取文件

> 原文：<https://dev.to/aurelkurtula/creating-a-text-editor-in-electron-reading-files-13b8>

让我们从创建`package.json`文件开始，并安装所需的包

```
{  "name":  "intro-to-electron",  "version":  "1.0.0",  "description":  "",  "main":  "main.js",  "scripts":  {  "start":  "electromon main.js  --ignore static"  },  "author":  "Aurel Kurtula",  "license":  "ISC",  "dependencies":  {  "electromon":  "^1.0.10",  "electron":  "^2.0.8"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要创建`main.js`文件。这就是我们的电子应用将要开始的地方:

```
const { app, BrowserWindow } = require('electron')
const path = require('path')

app.on('ready', function(){
    let window = new BrowserWindow({width:800, height:600})
    window.loadURL(path.join('file://', __dirname, 'static/index.html'))
})
app.on('close', function() {
    window = null
}) 
```

Enter fullscreen mode Exit fullscreen mode

当应用程序准备就绪，我们创建一个窗口，加载一个静态文件进行渲染。当应用程序关闭时，我们确保浏览器窗口被删除。

让我们创建`static/index.html`文件。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  Hello World!
  <link rel="stylesheet" href="./styles/main.css">
</head>
<body class="index">
  <div class="container">
    <ul id="titles"></ul>
    <div id="content" contenteditable="true">
        <p>Select a title <br />  &#x2190</p>
    </div>
  </div>
  <script src="scripts/index.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

真的很简单，`#titles`会有文件标题，`#content`会显示它们的内容。正如你所看到的，这是常见的前端的东西。我们有 CSS 和 javascript。

## 读取系统文件

我创建了一堆 markdown 文件，将它们放在一个`./data`目录中，现在我们将读取它们并将标题添加到应用程序中。我们将在`static/scripts/index.js`
中这样做

```
const fs = require('fs')
const path = require('path')
const { readTitles } = require(path.resolve('actions/uiActions'))
readTitles('./data').map(({title, dir}) => {
    el = document.createElement("li");
    text = document.createTextNode(`${title.split('.md')[0]}`);
    el.appendChild(text)
    el.addEventListener('click', function(e){ // clicking on sidebar titles
        fs.readFile(dir, (err, data) => {
        if (err) throw err;
        fileDir = dir;
        document.getElementById('content').innerHTML = data;
        });
    })
    document.getElementById('titles').appendChild(el)
}) 
```

Enter fullscreen mode Exit fullscreen mode

在第三行中，我只需要我的模块`readTitles`,它只是读取标题并将它们作为数组对象返回。我浏览它们，创建一个列表元素，然后添加一个事件监听器，将文件内容注入到`#content`中

[![](../Images/75715ca96cc0c1e9528dd0c4b7c7a351.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PrXpZTU4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7cu9ru56acuath88r4m0.gif)

我认为这是结束本入门教程的一个好点。

注意，这个窗口只是一个 chrome 浏览器，你可以在上面打开开发工具。您可以像往常一样从视图菜单中这样做，或者您可以让 electron 在开始时打开 devtools。可以在`./main.js`页
中完成

```
window.webContents.setDevToolsWebContents(devtools.webContents)
window.webContents.openDevTools({mode: 'detach'}) 
```

Enter fullscreen mode Exit fullscreen mode

下次我们将开始编辑我们的数据文件，并创建我们自己的自定义菜单

在 [github 查看代码。分支:第一部分](https://github.com/aurelkurtula/Texty/tree/part1)

[![aurelkurtula image](../Images/e1a1b48b6a401b86d5a91c8729e26a35.png)](/aurelkurtula) [## 在 electronic 中创建文本编辑器:第 2 部分-编写文件

### aurel Kurt ula 9 月 9 日 187 分钟阅读

#electron #javascript #node](/aurelkurtula/creating-a-text-editor-in-electron-part-2---writing-files-l80)