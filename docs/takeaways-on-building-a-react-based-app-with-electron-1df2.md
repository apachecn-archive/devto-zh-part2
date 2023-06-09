# 关于使用电子构建基于 React 的应用程序的几点建议

> 原文：<https://dev.to/nickparsons/takeaways-on-building-a-react-based-app-with-electron-1df2>

[![](img/81ebd793343324265e1e1fec54bdb722.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jFV0LDn2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n65l6kt5q5v9ad90t321.png)

今年早些时候， [Stream](https://getstream.io) 推出了 [Winds 2.0](https://getstream.io/winds) ，这是一款面向 macOS、Windows 和 Linux 的开源原生应用，提供了一种全新的消费 RSS 订阅和播客的方式。这是我们第一次构建原生应用，所以我们选择了使用[电子](https://electronjs.org/)，一个创建跨平台应用的框架。

除了 electronic，我们还利用了 React，因为它有一个非常大的社区，是开源的，并且易于开发。如果你想贡献或查看更多关于 Winds 的信息，请查看我们针对该项目的 [GitHub repo](https://github.com/getstream/winds) 。

> 如果你没有用过 Winds，你可以在 https://getstream.io/winds 报名。或者，如果你只是想要一个视觉效果，下面是 Winds 2.0 在 Electron 内部运行的截图:

[![](img/76d1d59115a63c0884b5ae611e004332.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---6KHiEUD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image5.png%3Fw%3D1024%26h%3D1024)

我们都知道如今开发工具的发展速度有多快。不幸的是，这种快速创新的副作用是网络上的内容过时了——有时会过时几个月或几年——即使是像 electronic 这样拥有强大追随者的流行工具。我们很快就意识到这个项目只能靠自己了。幸运的是，我们做了一些笔记，并在此分享，让您了解我们的发现。

### 为了确保你不会迷路，这里有一个我们将在这篇文章中讨论的组件的快速概要:

*   使用 Xcode 生成`.p12`文件来签署您的发行版
*   如何在[https://developer.apple.com](https://developer.apple.com)上创建配置文件(这将验证您的应用程序是由您和**您**发布的)
*   什么是授权文件以及如何 [entitlements.mas.plist](https://github.com/electron-userland/electron-osx-sign/wiki/3.-App-Sandbox-and-Entitlements) 说明您的应用程序需要哪些权限(例如网络、文件、设置等。)
*   使用电子生成器进行代码签名/分发
*   电子建造者如何工作并在幕后调用 Xcode 的协同设计工具
*   ASAR 文件和它们是什么
*   应用程序加载器以及如何使用它将您的发行版发送到 Apple
*   实际的商店列表在 iTunes Connect 中定义
*   苹果电脑的密钥是在苹果网站上生成的

安装了最新版本的 Node.js(目前是@ v 10 . 6 . 0)，让我们开始吧。

# 1。使用 React 启动并运行

对于 React，我们将使用 [Create React App](https://github.com/facebook/create-react-app) (CRA)，一个由脸书构建和维护的 React 搭建工具。CRA 的美妙之处在于，它不需要你进行任何配置(除非你从 CRA 被驱逐，这里的[概述了这一点](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#npm-run-eject)——请阅读，因为知道为什么以及何时应该以及不应该从 CRA 被驱逐是很重要的)。

### 全球安装 Create React App

`yarn global add create-react-app`

### 使用 Create React App CLI 创建示例应用程序

```
npx create-react-app example
cd example
yarn start 
```

> 注意: [npx](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b) 自带 npm 5.2+及更高版本

### 在浏览器中查看您的示例应用

然后打开 [http://localhost:3000/](http://localhost:3000/) ，你会看到我们的基本样板 React 应用。

[![](img/3bfcf8d1940dcf85f06412f45b6dd9ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--akOf44SU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image8.png%3Fw%3D1024%26h%3D1024)

很简单，对吧？现在，您已经用几个命令引导了 React 应用程序，并准备进入下一步！

# 2。为电子做准备

接下来，让我们开始准备 React 应用程序，以便与 electronic 一起使用。我们发现最好的设置是执行以下操作(确保您位于示例目录中):

### 安装电子

`yarn add electron --dev`

### 移动到`public`目录下，创建一个名为`electron.js`的新文件:

`cd public && touch electron.js`

### 用以下内容填充`electron.js`文件的内容:

```
const { app, BrowserWindow, shell, ipcMain, Menu, TouchBar } = require('electron');
const { TouchBarButton, TouchBarLabel, TouchBarSpacer } = TouchBar;

const path = require('path');
const isDev = require('electron-is-dev');

let mainWindow;

createWindow = () => {
    mainWindow = new BrowserWindow({
        backgroundColor: '#F7F7F7',
        minWidth: 880,
        show: false,
        titleBarStyle: 'hidden',
        webPreferences: {
            nodeIntegration: false,
            preload: __dirname + '/preload.js',
        },
        height: 860,
        width: 1280,
    });

    mainWindow.loadURL(
        isDev
            ? 'http://localhost:3000'
            : `file://${path.join(__dirname, '../build/index.html')}`,
    );

    if (isDev) {
        const {
            default: installExtension,
            REACT_DEVELOPER_TOOLS,
            REDUX_DEVTOOLS,
        } = require('electron-devtools-installer');

        installExtension(REACT_DEVELOPER_TOOLS)
            .then(name => {
                console.log(`Added Extension: ${name}`);
            })
            .catch(err => {
                console.log('An error occurred: ', err);
            });

        installExtension(REDUX_DEVTOOLS)
            .then(name => {
                console.log(`Added Extension: ${name}`);
            })
            .catch(err => {
                console.log('An error occurred: ', err);
            });
    }

    mainWindow.once('ready-to-show', () => {
        mainWindow.show();

        ipcMain.on('open-external-window', (event, arg) => {
            shell.openExternal(arg);
        });
    });
};

generateMenu = () => {
    const template = [
        {
            label: 'File',
            submenu: [{ role: 'about' }, { role: 'quit' }],
        },
        {
            label: 'Edit',
            submenu: [
                { role: 'undo' },
                { role: 'redo' },
                { type: 'separator' },
                { role: 'cut' },
                { role: 'copy' },
                { role: 'paste' },
                { role: 'pasteandmatchstyle' },
                { role: 'delete' },
                { role: 'selectall' },
            ],
        },
        {
            label: 'View',
            submenu: [
                { role: 'reload' },
                { role: 'forcereload' },
                { role: 'toggledevtools' },
                { type: 'separator' },
                { role: 'resetzoom' },
                { role: 'zoomin' },
                { role: 'zoomout' },
                { type: 'separator' },
                { role: 'togglefullscreen' },
            ],
        },
        {
            role: 'window',
            submenu: [{ role: 'minimize' }, { role: 'close' }],
        },
        {
            role: 'help',
            submenu: [
                {
                    click() {
                        require('electron').shell.openExternal(
                            'https://getstream.io/winds',
                        );
                    },
                    label: 'Learn More',
                },
                {
                    click() {
                        require('electron').shell.openExternal(
                            'https://github.com/GetStream/Winds/issues',
                        );
                    },
                    label: 'File Issue on GitHub',
                },
            ],
        },
    ];

    Menu.setApplicationMenu(Menu.buildFromTemplate(template));
};

app.on('ready', () => {
    createWindow();
    generateMenu();
});

app.on('window-all-closed', () => {
    app.quit();
});

app.on('activate', () => {
    if (mainWindow === null) {
        createWindow();
    }
});

ipcMain.on('load-page', (event, arg) => {
    mainWindow.loadURL(arg);
}); 
```

# 3。修改 package.json 文件

一旦创建了`electron.js`文件，我们需要继续修改我们的`package.json`文件，以便指向并执行正确的文件和命令。您的整个文件应该如下所示:

```
{
    "name": "example",
    "version": "1.0.0",
    "description": "Building and Publishing a React Based Electron App From Scratch",
    "private": false,
    "author": "Nick Parsons <nparsons08@gmail.com>",
    "license": "MIT",
    "homepage": "./",
    "main": "public/electron.js",
    "keywords": [
        "Example",
        "React",
        "Electron"
    ],
    "scripts": {
        "dev": "yarn react-scripts start",
        "build": "react-scripts build",
        "start": "concurrently \"cross-env BROWSER=none yarn react-scripts start\" \"wait-on http://localhost:3000 && electron .\"",
        "pack": "electron-builder --dir",
        "dist": "npx build --x64 --macos --win --linux --c.extraMetadata.main=build/electron.js -p always"
    },
    "dependencies": {
        "electron-is-dev": "^0.3.0",
        "electron-publisher-s3": "^20.17.2",
        "react": "^16.4.1",
        "react-dev-utils": "^5.0.1"
    },
    "devDependencies": {
        "react-scripts": "1.1.4",
        "concurrently": "^3.6.0",
        "cross-env": "^5.2.0",
        "electron": "^2.0.3",
        "electron-builder": "^20.18.0",
        "version-bump-prompt": "^4.1.0"
    },
    "build": {
        "appId": "com.your-domain",
        "compression": "normal",
        "productName": "Example",
        "directories": {
            "buildResources": "build",
            "output": "dist"
        },
        "mac": {
            "icon": "assets/icon.icns",
            "type": "distribution",
            "target": [
                "pkg",
                "dmg",
                "mas"
            ],
            "publish": {
                "provider": "s3",
                "bucket": "example-releases",
                "path": "releases"
            },
            "category": "public.app-category.news"
        },
        "mas": {
            "entitlements": "assets/entitlements.mas.plist",
            "entitlementsInherit": "assets/entitlements.mas.inherit.plist",
            "provisioningProfile": "assets/embedded.provisionprofile"
        },
        "win": {
            "target": "nsis",
            "icon": "assets/icon.ico",
            "publish": {
                "provider": "s3",
                "bucket": "example-releases",
                "path": "releases"
            }
        },
        "linux": {
            "icon": "assets/icon.png",
            "target": [
                "snap",
                "AppImage"
            ],
            "description": "Example",
            "category": "Network;Feed",
            "publish": {
                "provider": "s3",
                "bucket": "example-releases",
                "path": "releases"
            }
        }
    }
} 
```

> 注意:运行`yarn install`来安装 package.json 文件中的包。

现在您可以简单地运行`yarn start`并...

[![](img/d5396042bb324585a7e89728217b28d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KpD8-EiA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image1-1.png%3Fw%3D1024%26h%3D1024)

您的应用程序现在运行在一个电子包装器中！

# 4。准备分发

在这一节中，我们不打算深入研究如何构建应用程序；然而，我们将根据你如何开始打包你的应用程序来分发到各种商店，如 macOS 和 [Snapcraft](https://snapcraft.io/) (Linux)商店。

### 添加徽标

您还需要在公共目录中创建一个资产目录。创建完成后，您需要将以下文件放入目录中(我们稍后会引用它们)。

*   图标. ico

*   icon.png(256 x256 像素)

*   icon.icns

下面是创建目录的快速命令:

`cd ../ && mkdir assets`

> 注意:如果你没有图片，你可以在这里使用 Winds 图标[中的图标](https://s3.amazonaws.com/winds-hosted/icons/icons.zip)。

### 生成密钥

要启动并运行 macOS，您将需要苹果公司在开发者控制台中提供的约 6 个证书——请遵循以下说明:

1.  前往 https://developer.apple.com 并登录
2.  转到“[证书、标识符&配置文件](https://developer.apple.com/support/certificates/)部分
3.  选择下拉菜单并选择 macOS
4.  单击+按钮并生成下面的证书类型

[![](img/1be8a2fa4a9ea638695e5c542270d2e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cd1ujJig--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image4-1.png%3Fw%3D1024%26h%3D1024)

完成后，下载证书。当您打开它们时，它们会自动储存在您的钥匙串中。

### 添加权利文件

现在我们已经将图像添加到了`assets`目录中，让我们继续添加我们的`entitlements`文件。这些在签署释放申请时很重要。

**在资产目录中，运行以下命令:**

```
cd assets && touch entitlements.mas.plist && touch entitlements.mas.inherit.plist 
```

然后，用以下内容填充`entitlements.mas.plist`:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.application-identifier</key>
    <string>XXXXXXXXXX.com.your-domain</string>
    <key>com.apple.security.network.client</key>
    <true/>
    <key>com.apple.security.files.user-selected.read-write</key>
    <true/>
  </dict>
</plist> 
```

此授权文件指定除了文件访问(用于拖放)之外，您还需要访问网络。

> 注:您需要使用 Apple 提供的 ID 填充“XXXXXXXXXX”值，并使用软件包命名约定中的域填充字符串的其余部分(例如 XXXXXXXXXX.com.example)。你需要在 https://developers.apple.com 从苹果那里获得这个(每年大约花费 99 美元。成为苹果开发者)。

和`entitlements.mas.inherit.plist`:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.inherit</key>
    <true/>
  </dict>
</plist> 
```

最后，我们需要为 macOS 创建我们的`embedded.provisionprofile`,并将其保存在`assets`目录中。Apple 使用该文件来验证应用程序是否合法。按照以下步骤为您的应用程序生成预配配置文件:

1.  前往 https://developer.apple.com 并登录
2.  转到“证书、标识符和配置文件”部分
3.  选择下拉菜单并选择 macOS
4.  单击右上角的+按钮
5.  在“生产”部分选择“Mac App Store”
6.  单击继续
7.  选择“Mac 应用程序分发”
8.  遵循生成“CSR”的说明

一旦完成，你就会有一个正式的`embedded.provisionprofile`来签署你的申请！下面是各种屏幕的样子供参考:

[![](img/1f31fdadf2b434db6c659e545caf73ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7_O19gFO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image3-1.png%3Fw%3D1024%26h%3D1024)

[![](img/1af030b21badad8b70af1b69760df1aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bkAB1pXX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image7.png%3Fw%3D1024%26h%3D1024)

现在是时候仔细检查我们的`package.json`文件中的构建设置了。该文件包含 Linux、Windows 和 macOS 的构建配置。我们不会使用所有的设置，所以如果你想知道所有的设置，请访问[https://www.electron.build/configuration/configuration](https://www.electron.build/configuration/configuration)。

这是我们针对风的构建配置:

```
"build": {
        "appId": "com.your-domain",
        "compression": "normal",
        "productName": "Example",
        "directories": {
            "buildResources": "build",
            "output": "dist"
        },
        "mac": {
            "icon": "assets/icon.icns",
            "type": "distribution",
            "target": [
                "pkg",
                "dmg",
                "mas"
            ],
            "publish": {
                "provider": "s3",
                "bucket": "example-releases",
                "path": "releases"
            },
            "category": "public.app-category.news"
        },
        "mas": {
            "entitlements": "assets/entitlements.mas.plist",
            "entitlementsInherit": "assets/entitlements.mas.inherit.plist",
            "provisioningProfile": "assets/embedded.provisionprofile"
        },
        "win": {
            "target": "nsis",
            "icon": "assets/icon.ico",
            "publish": {
                "provider": "s3",
                "bucket": "example-releases",
                "path": "releases"
            }
        },
        "linux": {
            "icon": "assets/icon.png",
            "target": [
                "snap",
                "AppImage"
            ],
            "description": "Example",
            "category": "Network;Feed",
            "publish": {
                "provider": "s3",
                "bucket": "example-releases",
                "path": "releases"
            }
        }
    } 
```

# 5。调试&资源

电子是一项相当新的技术，尽管它支持数百甚至数千个应用程序(T1)——开发社区中最著名的是 T2 的 Atom 和 T4 的 Slack，但它仍然存在缺陷。围绕这个项目有一个活跃的生态系统，创造出有用的工具，如[电子生成器](https://github.com/electron-userland/electron-builder)，但是这些工具也有它们自己的一套缺陷。我们遇到了无数的错误信息、空白屏幕、被拒绝的应用程序商店提交等等。，但这从未使我们停止探索电子能提供什么。

在这个过程中，我们发现了很多很棒的调试工具和其他阅读材料，我们觉得有必要写下来在这篇文章中分享。如果您遇到问题，您可能会在以下资源中找到答案:

*   [电子生成器](https://github.com/electron-userland/electron-builder)
*   [在 Mac 应用商店发布应用](https://medium.com/@flaqueEau/releasing-an-electron-app-on-the-mac-app-store-c32dfcd9c2bd)
*   [Mac App Store 提交指南](https://electronjs.org/docs/tutorial/mac-app-store-submission-guide)
*   [iOS 预置描述文件初学者指南](https://blog.theodo.fr/2017/02/a-beginners-guide-to-ios-provisioning-profiles/)
*   [iOS 代码签名&简而言之就是配置](https://medium.com/ios-os-x-development/ios-code-signing-provisioning-in-a-nutshell-d5b247760bef)
*   [验证包已签名](https://www.jamf.com/jamf-nation/articles/299/verifying-that-a-package-is-signed)
*   如何打开并重新包装 OSX？pkg 文件
*   [包装质量错误](https://github.com/electron-userland/electron-builder/issues/2719)
*   [类别列表](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/LaunchServicesKeys.html#//apple_ref/doc/uid/TP40009250-SW8)
*   [RB 应用检查灯](https://itunes.apple.com/us/app/rb-app-checker-lite/id519421117?mt=12)

# 6。ASAR 文件&它做什么

我们在使用电子构建器时遇到的一个问题是 ASAR 文件有什么作用，为什么它被打包在我们的部署中。经过深入研究，我们发现 ASAR 文件，或者更确切地说是档案，是一种简单的类似 tar 的格式，它将文件连接成一个文件，允许 Electron 从其中读取任意文件，而无需打开整个文件。

说到底，它实际上只是一个只读地图，显示了电子构建中的文件，让电子本身知道里面有什么。这有时会触发各种反病毒扫描程序。也就是说，你可以通过`--unpack`选项，一些文件将不会被打包。这样做将创建两个文件；`app.asar`和`app.asar.unpacked`。

如果你对 ASAR 文件的技术深度感兴趣，请点击这里进入应用程序打包的电子生成器页面。

# 7。部署给用户

> 注:本节要求您已经启动并运行了与您的帐户相关联的[亚马逊 S3](https://aws.amazon.com/s3/) 和 [awscli](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) ( `pip install awscli && aws configure`)。我们的 bucket 的名称将是`example-releases`，文件将被放在这个 bucket 中的一个名为`releases`的目录中。

一旦完成并准备就绪，您现在就可以部署给用户了！简单地运行`yarn build`和[电子构建器](https://github.com/electron-userland/electron-builder)将运行所有必要的命令，以便为每个操作系统打包正确的包。一旦完成，运行`yarn dist`，它将开始上传(使用来自`aws configure`的凭证)包到亚马逊 S3，然后你可以链接用户下载。

下面先睹为快，看看我们的 AWS S3 铲斗是什么样子的:

[![](img/0377d3a1ea138bb8db3cfe74cfabebb4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bNf3CwHU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image2-1.png%3Fw%3D1024%26h%3D1024)

将应用程序上传到 macOS 商店最简单的方法是通过 Xcode 内置的应用程序加载器。只需进入 **Xcode** > **打开开发者工具** > **应用加载器**

[![](img/373548e58651acfc6314475e6eb42c14.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wm31Jl2h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image9.png%3Fw%3D1024%26h%3D1024)

打开后，将要求您登录:

[![](img/02f53ec5e991632d5294ac4999847f02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4vyBupZm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image10.png%3Fw%3D300%26h%3D300)

> 注:您必须拥有一个与有效的 Apple Developer 帐户关联的 Apple ID。此外，您必须暂时禁用 2FA，因为它不能很好地与应用程序加载器一起运行。

登录后，系统会提示您一个选择器，您可以从中选择要上传的正确文件。

[![](img/428f0344568a9f3387d5c48218102e79.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WJj6p1eN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2018/07/image6.png%3Fw%3D1024%26h%3D1024)

> 注意:选择文件时，请确保导航到 mas 目录。mas 中的应用程序是适用于 macOS 商店的唯一软件包。

当上传到 macOS 商店时，你可能需要与苹果公司进行多次迭代才能输入细节。苹果非常挑剔，有一个很好的理由——他们不希望应用程序商店里充斥着错误。这只是学习过程的一部分，所以不要因此而沮丧。

> 额外收获:如果你想部署到 Snapcraft 商店，可以在他们的网站上查看文档，网址是[https://docs.snapcraft.io/build-snaps/](https://docs.snapcraft.io/build-snaps/)。这是一个相当简单的过程，您已经有了准备上传的快照文件(在您的`dist`目录中)!

# 包装完毕

希望你已经学到了一些东西。如果你有任何问题或意见，请写在下面的评论里。如果你想直接和我联系，我随时都可以在 Twitter 上找到你。请继续关注更多关于风的帖子。祝你在未来的反应和电子努力中好运！