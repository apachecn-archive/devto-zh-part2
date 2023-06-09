# 在 electronic 中创建文本编辑器:第 3 部分-设置首选项

> 原文：<https://dev.to/aurelkurtula/creating-a-text-editor-in-electron-part-3---setting-preferences-584h>

欢迎来到本系列的最后一部分，我们将通过构建一个文本编辑器来探索电子学的基础知识。如果你想全面了解我们正在做的事情，请务必阅读第一部分和第二部分的

我们在几乎所有应用中看到的一个惯例是允许用户制作他们自己的编辑器。这就是我们在这一部分要做的事情。我们将让用户设置应用程序的主题。最后，我们将推出一个新窗口，如下所示:

[![](img/9370d1267b895a520ef708e31187cccd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6gb3EdRQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mg6mi0d0wig6mbuf5s25.png)

## 自定义表头

在我们进入教程的实质内容之前，先改变一下枯燥的默认标题。

(这都是为了[让读者开心](https://dev.to/davidamunga/comment/5cko)🤪).

这很简单。当我们定义窗口(`new BrowserWindow`)时，我们可以给它框架的选项。如果我们将`frame`设置为 false，它将删除它——包括左侧的三个按钮。因此，我们希望删除默认样式，而不是按钮。在`./main.js`更改`BrowserWindow`定义以包含相关选项:

```
window = new BrowserWindow({ 
    width:800, 
    height:600, 
    titleBarStyle: 'hidden' 
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行应用程序，我们会看到三个按钮，但没有标题，这意味着我们必须创建自己的。所以在`./static/index.html`中，我们会这样做:

```
<header id="customtitle_wrap">
    <h1 id="customtitle">Texty</h1>
</header> 
```

Enter fullscreen mode Exit fullscreen mode

在第二部分中，我们增加了星号出现在标题标签中的功能，以指示文件何时需要保存。现在，我们需要将该功能添加到`#customtitle`中，而不是 title 标签中。

CSS 现在可以是你喜欢的任何东西，但是`-webkit-app-region: drag`应该被应用到`#customtitle`上，这样它就成为一个可以在屏幕上拖动窗口的句柄。阅读[无框窗口](https://github.com/electron/electron/blob/master/docs/api/frameless-window.md)的文档，查看所有选项。

[![](img/d5a930cb7537c01eafb0221da3fff156.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LMpZJmaY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qwxgkm99sm6zmwffc59f.png)

我从谷歌字体中抓取了一个字体；然而，在真正的应用程序中，我们至少会下载字体，这样用户就不需要连接到互联网。

## 初始化首选项窗口

正如我们在第一个教程中所做的一样，我们需要将一个 HTML 页面加载到一个新窗口中。让我们在`./static/preferences.html` :
创建页面

```
<body>
    <p class="notification">Here you're able to personalise the interface by picking the colors you'd like to see. The changes will be saved automatically upon window being closed</p>
    <div class="container">
    <div id="content" >
        <form action="">
            <button id="defaultValues">Reset</button>
            <p><span>Background</span> <label for="background"></label> <span>
                <input type="text" name="background" value="#FFFFFF"></span> 
            </p>
            <p><span>Border Color</span> <label for="border-color"></label> <span>
                <input type="text" name="border-color" value="#50C68A"></span> 
            </p>
            <p><span>Text Color</span> <label for="text-color"></label> <span>
                <input type="text" name="text-color" value="#232323"></span> 
            </p>
            <p><span>Sidebar Background</span> <label for="sidebar-color"></label> <span>
                <input type="text" name="sidebar-color" value="#2F3235"></span> 
            </p>
            <p><span>Sidebar Text</span> <label for="sidebar-text"></label> <span>
                <input type="text" name="sidebar-text" value="#939395"></span> 
            </p>
        </form>
    </div>
    </div>
    <script src="scripts/preferences.js"></script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

当菜单按钮被点击时，这个页面必须启动。让我们在`./components/Menu.js`
添加那个按钮

```
{
    label: app.getName(),
    submenu: [
        {
            label: 'Preferences',
            accelerator: 'cmd+,', // shortcut
            click: _ => {
                const htmlPath = path.join('file://', __dirname, '../static/preferences.html')
                let prefWindow = new BrowserWindow({ width: 500, height: 300, resizable: false })
                prefWindow.loadURL(htmlPath)
                prefWindow.show()
                // on window closed
            },
        },
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

当选择`Preferences`时，`./static/preferences.html`页面加载到新的浏览器窗口中。这一次，我们确保用户不能调整它的大小。

应用一些 CSS，我们得到这个:

[![](img/072ba098b5b9a66f07b30ed7a0f58b7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uK1mvMeF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qowqq4vmxy5w9oiw25v4.png)

正如上面的 HTML 中所指定的，默认颜色在表单中是硬编码的。对于 Javascript，我们希望将这些颜色值作为标签的背景色，当用户输入新的颜色值时，它们会在标签中得到反映。我们可以用颜色选择器来找乐子，但是我们将保持它的基本性，假设用户想要输入他们喜欢的颜色。在这种情况下，我们需要倾听输入的变化。

这个功能需要放入`./static/scripts/preferences.js`。

让我们记住 HTML:

```
<p>
    <span>Sidebar Text</span> 
    <label for="sidebar-text"></label> <span>
    <input type="text" name="sidebar-text" value="#939395"></span> 
</p> 
```

Enter fullscreen mode Exit fullscreen mode

因此，javascript 可以简单到遍历输入并改变标签:

```
var inputs = document.getElementsByTagName('input')
for(var i = 0 ; i < inputs.length; i++){
    document.querySelector(`label[for="${inputs[i].name}"]`).style.backgroundColor = inputs[i].value
    inputs[i].onkeyup = e => {
        document.querySelector(`label[for="${e.target.name}"]`).style.backgroundColor = e.target.value
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

代码遍历每个输入元素，将它们的值作为标签背景颜色，然后在输入改变时重新应用颜色。

[![](img/2900c49e08b3624bae0b7c2b8ca360e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--igB-0O-3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5nf5rn33neezads3ace2.gif)

### 保存颜色首选项

这个窗口的意义在于，当应用程序关闭时，这些颜色仍然存在，所以它们必须被存储在某个地方。电子给了我们一个存储用户数据的途径。[文档](https://electronjs.org/docs/api/app)声明我们通过`electron.app.getPath('userData')`访问它

> 用于存储应用程序配置文件的目录，默认情况下，该目录是附加了应用程序名称的 appData 目录。

在这个文件夹中，我们希望将我们的颜色存储为 JSON。我们使用与我们在第二部分中所做的一样的从渲染进程到主进程模式的消息传递来做到这一点。

首先，让我们收集所有的颜色，然后将它们发送到主流程。

```
let preferences = {};
for(var i = 0 ; i < inputs.length; i++){
    ...
    preferences[inputs[i].name] = inputs[i].value
    inputs[i].onkeyup = e => {
        preferences[e.target.name] = e.target.value
        ...
        ipcRenderer.send(PREFERENCE_SAVE_DATA_NEEDED, preferences)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

用所有默认颜色填充`preferences`对象。然后，每当其中一个输入改变时，相应的对象键也会改变。最后，我们用`preferences`对象作为消息体向主流程发送一条`PREFERENCE_SAVE_DATA_NEEDED`消息。

在`./components/Menu.js`的顶部，我们可以监听消息并收集其数据

```
let inputs;
ipcMain.on(PREFERENCE_SAVE_DATA_NEEDED, (event, preferences) => {
    inputs = preferences
}) 
```

Enter fullscreen mode Exit fullscreen mode

最后，对于菜单，我在几乎所有的 mac 应用中看到的一个模式是，不需要“保存”按钮就可以保存偏好设置。在这里，我们可以通过在窗口关闭时采取行动来做同样的事情。

在菜单页面中，我们可以在窗口`close`上编写逻辑。

```
{
    label: 'Preferences',
    accelerator: 'cmd+,', // shortcut
    click: _ => {
        ....
        prefWindow.on('close', function () {
            prefWindow = null 
            userDataPath = app.getPath('userData');
            filePath = path.join(userDataPath, 'preferences.json')
            inputs && fs.writeFileSync(filePath, JSON.stringify(inputs));
            window.webContents.send(PREFERENCE_SAVED, inputs); 
        })

    },
} 
```

Enter fullscreen mode Exit fullscreen mode

`userDataPath`位于`/Users/YourUserName/Library/Application Support/Electron/`处，在那里你会发现我们的`preferences.json`，它保存着颜色。

完成后，`PREFERENCE_SAVED`消息被发送到我们最初的`window`的渲染进程。

现在我们需要从`preferences.json`文件中读取颜色，并将它们应用到 UI 中。

首先，让我们在`./static/scripts/preferences.js`中做

```
const fs = require('fs')
let userDataPath = remote.app.getPath('userData');
let filePath = path.join(userDataPath, 'preferences.json')
let usersStyles =  JSON.parse( fs.readFileSync(filePath) )

for(let style in usersStyles) {
    document.querySelector(`input[name="${style}"]`).value = usersStyles[style]
    document.querySelector(`label[for="${style}"]`).style.backgroundColor = usersStyles[style]
} 
```

Enter fullscreen mode Exit fullscreen mode

这个过程是相反的。我们从`preferences.json`中读取保存的数据，遍历颜色，将它们作为输入值并标记背景颜色。

#### 重设颜色。

我们想在 HTML 表单中硬编码颜色的原因是，我们可以在任何时候用 javascript 中的`defaultValue`访问它们。我们将点击重置按钮:

```
<button id="defaultValues">Reset</button> 
```

Enter fullscreen mode Exit fullscreen mode

单击时，循环输入字段，并相应地应用默认值。

```
document.getElementById('defaultValues').addEventListener('click', function(e) { // reset
    e.preventDefault();
    preferences = {};
    for(var i = 0 ; i < inputs.length; i++){
        preferences[inputs[i].name] = inputs[i].defaultValue
        document.querySelector(`label[for="${inputs[i].name}"]`).style.backgroundColor = inputs[i].defaultValue
        inputs[i].value = inputs[i].defaultValue
    }
    ipcRenderer.send(PREFERENCE_SAVE_DATA_NEEDED, preferences)
} ) 
```

Enter fullscreen mode Exit fullscreen mode

上述代码执行以下操作

1.  重置`preferences`对象。
2.  用输入`defaultValue`重新填充`preferences`对象。
3.  通过输入`defaultValue`改变标签的背景颜色。
4.  用输入`defaultValue`改变输入值。
5.  向主进程发送消息。

## 将保存的颜色应用到主窗口

关闭偏好设置窗口时，会发送一条消息。

```
window.webContents.send(PREFERENCE_SAVED, inputs); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在主窗口中收听它，并使用与消息一起发送的内容。

在此之前，我们先来谈谈 CSS。

CSS 中最重要的部分是变量:

```
:root {
    --background: #FFFFFF;
    --border-color: #50C68A;
    --text-color: #232323;
    --sidebar-color: #2F3235;
    --sidebar-text: #939395;
} 
```

Enter fullscreen mode Exit fullscreen mode

每当我们用 javascript 改变这些变量时，应用这些变量的每个元素的外观都会改变。

我们可以在`./static/scripts/index.js`
做这件事

```
 let userDataPath = remote.app.getPath('userData');
    let filePath = path.join(userDataPath, 'preferences.json')

    let usersStyles  = JSON.parse( fs.readFileSync(filePath) )

    for(let style in usersStyles) {
        document.documentElement.style.setProperty(`--${style}`, usersStyles[style]);
    }
    ipcRenderer.on(PREFERENCE_SAVED, function (event, inputs) {
        for(let style in inputs) {
            document.documentElement.style.setProperty(`--${style}`, inputs[style]);
        }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

这就是了。现在，使用这些变量的每个元素都将被自动更改。

最终的结果是这样的

[![](img/02560c61613964047cc85227138daca7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w4zo7DaR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rrd1k2589ruszh5t7vxf.gif)

**你可以在 GitHub 的**克隆[库](https://github.com/aurelkurtula/Texty/tree/part3)

## 结论

这个系列就到这里。当我试图弄清楚有很多东西不见了。代码肯定可以重构，可以改进存储，并且不存在错误处理。

当我在做这个演示时，我想到了我当前的写作工作流程，它由作为 GitLab wiki 托管的[节点生成的 markdown 页面组成，我觉得添加一个电子界面可能会使记笔记的过程稍微顺畅一些。这可能是我的下一个个人项目。](https://dev.to/aurelkurtula/using-git-wiki-instead-of-evernote-to-organize-personal-content-e9d)

希望你也受到同样的启发💡

[![aurelkurtula image](img/e1a1b48b6a401b86d5a91c8729e26a35.png)](/aurelkurtula) [## 在 electronic 中创建文本编辑器:第 1 部分-读取文件

### aurel Kurt ula 9 月 5 日 182 分钟阅读

#electron #javascript #node](/aurelkurtula/creating-a-text-editor-in-electron-reading-files-13b8)[![aurelkurtula image](img/e1a1b48b6a401b86d5a91c8729e26a35.png)](/aurelkurtula) [## 在 electronic 中创建文本编辑器:第 2 部分-编写文件

### aurel Kurt ula 9 月 9 日 187 分钟阅读

#electron #javascript #node](/aurelkurtula/creating-a-text-editor-in-electron-part-2---writing-files-l80)