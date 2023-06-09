# 如何在 Mac 上真正建立一个 React 原生环境

> 原文：<https://dev.to/rob117/how-to-really-set-up-a-react-native-environment-on-mac-248h>

这基本上是从我使用的私人自述文件中摘录的，并经过清理以供公众使用。它没有我试图带给这些事情的幽默，我试图让它变得简单，当日本人需要帮助时，我可以在这里指出。又快又脏，以后可能会清理。

这只适用于 Mac。如果你没有使用 Mac 来进行 React Native dev - do，这是非常好的。

# 设置 react 原生环境

## 步骤

### 软件安装和文件设置

从 app store 安装 Xcode——Xcode 是一个为你运行 iOS 应用程序的程序，让你为你的应用程序启动一个模拟器，这样你就可以在不需要物理 iPhone 的情况下进行开发。

安装一个 Java 开发工具包——你需要这个来让 Android 应用在 React Native
[http://www . Oracle . com/tech network/Java/javase/downloads/JDK 8-downloads-2133151 . html](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
接受许可协议，Mac OS X x64

安装 Android Studio (google this)。这就是我们将如何运行 android 模拟器，所以我们不需要打开 Android 手机来检查我们的工作。现在，我们需要设置它。

打开 Android Studio。选择自定义安装。检查以下复选框

*   Android SDK
*   Android SDK 平台
*   性能(Intel@HAXM) -这加快了模拟器的速度。
*   Android 虚拟设备

开始下载。

安装节点和节点程序包管理器(NPM)。如果你不知道那是什么，字面意思是谷歌-在 MacOS 上安装 Node。非常无痛

这需要很长时间，所以在您的终端中，输入以下内容(不带$符号)。

这将安装 watchman，这是一个监视文件并在文件更改时更新您的应用程序的程序。
`$brew install node watchman`

这将安装 react native，这样我们就可以做一些很酷的事情，比如制作应用程序
`npm install -g react-native-cli`

### 回到 Xcode

安装 xcode 命令行工具。在终端中:

`$xcode-select --install`

将下面一行添加到您的~。/bash_profile

`alias android-studio="open -a /Applications/Android\ Studio.app/"`

这(在重新启动终端后)让您通过键入以下命令打开目录

`android-studio .`

在那个目录里。

### Android SDK 和 AVD 安装

下载完成后打开 android studio，点击“配置”，进入 SDK 管理器。

点击“显示套餐详情”

启用 Android 6.0(棉花糖)，在棉花糖内部，启用

*   谷歌应用编程接口
*   Android SDK 平台 23
*   英特尔 x86 Atom_64 系统映像
*   Google APIs 英特尔 x86 Atom_64 系统映像

在 Android 7 和 8 上也这样做(不是必须的，但是对未来有好处)

接下来，在 SDK 工具选项卡上，单击“显示包细节”，查找“Android SDK Build-Tools”，并确保选择了 23 . 0 . 1
单击应用。

追加到~/。bash_profile:

```
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/platform-tools 
```

终端:`source $HOME/.bash_profile`

用 echo $PATH 确认 sdk 位置

我们这么做是因为我们别无选择。这是制作 Android 应用程序所必需的工作。

### 新建项目

设置一个项目真正快速地配置模拟器。

在终端中，用
`$npx react-native init <APP_NAME>`开始一个新项目

`$cd <APP_NAME>`

`$npm install`

注意，你也可以使用 Yarn(谷歌安装 Yarn)

#### 运行在 iOS 上

在终端中，从 react 本地项目文件夹中键入`$react-native run-ios`

确认应用程序运行。

* * *

**模拟器未发现错误**

如果它告诉你没有找到模拟器，重启你的电脑。使用 react-native init 命令的常见问题。

* * *

按下 command+d

启用热重装。

更改 ios 下 app.js 中的文本，保存，在模拟器中观察即时变化。

关闭模拟器。

庆祝一下。

#### 运行在安卓系统上

在 Android Studio 中打开项目。(转到 react-native 目录，转到/android，然后运行`android-studio .`

打开 Android studio，打开其中的示例 App 目录
当显示“检测到 Android 框架”时，配置- >同意

单击右上角的 AVD 管理器图标(Waaaaay 右上角)

获取您想要测试的任何设备，然后在 x86 images 选项卡上单击 Next
，然后查找 Marshmallow API Level 23，x86_64 API 映像，目标为 Android 6.0(Google API)。

单击下一步，然后单击完成。

单击特定 android 虚拟设备上的播放按钮来启动它。当主屏幕出现时，您就准备好了！

项目根中的终端:

`react-native run-android`

按 cmd + m

启用热重装

**如果你的屏幕是白色的:**

进入更多设置(侧边栏)，进入设置，点击高级，然后设置 OpenGL ES 渲染器到一个新的选项(随机)，然后退出设置，退出模拟器，重启模拟器，重新运行`react-native run-android`

更改 app.js 中的文本

观察变化。

庆祝一下！

### 编辑

我是铁杆核素(Atom)。现在我只关心 visual studio 代码。

## 设置 ESLint

这个项目使用 [ESLint](https://eslint.org/) 。

来源:
[https://github.com/expo/eslint-config-universe](https://github.com/expo/eslint-config-universe)
[https://github.com/Intellicode/eslint-plugin-react-native](https://github.com/Intellicode/eslint-plugin-react-native)

在根目录下运行以下命令:

`yarn add --dev eslint prettier eslint-config-universe eslint-plugin-react eslint-plugin-react-native`

将此文件移动到您的项目根目录[https://gist . github . com/rob 117/118443 a 2610 af 3 ABC bb 3d 8 DDB 8 a 213d 1](https://gist.github.com/Rob117/118443a2610af3abcbb3d8ddb8a213d1)

在您的 package.json 中，在" scripts"
`"lint": "./node_modules/.bin/eslint --fix *.js **/*.js"`下添加下面一行

然后运行`npm run lint`来 lint 你的代码。

### 安装测试工具

Jest 已经安装

运行 jest 测试:`npm test -- __tests__`

我意识到这篇文章写得非常快，可能有点难以理解——如果有任何部分不可用，请告诉我，我会更新它。我在时间紧迫的情况下写了这篇文章，作为对一个朋友的承诺。