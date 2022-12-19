# 在 VSCode 中运行、调试和获取 IntelliSense for C C++

> 原文：<https://dev.to/gorvgoyl/run-debug-get-intellisense-for-c-c-in-vscode-4e0o>

[![](../Images/b6c5d2bc519a8cbea437942b54e33c54.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EqVitqhI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aam2R0KHSgYdw8KOTPk37Hg.png)

**你可以在我的博客[这里](https://gourav.io/blog/setup-vscode-to-run-debug-c-cpp-code)看更新的文章**

* * *

> 学完本简短指南后，您将能够在 VSCode 中运行、调试和获得 intelliSense for C/C++文件。尽管本指南主要针对 Windows 平台，但稍加修改也可以扩展到 Mac 和 Linux。

在我的竞争编程生涯中，我广泛使用了 C & C++并且想要一个更好的调试智能感知的支持。唯一可用的选项是 [Dev-C++](//orwelldevcpp.blogspot.com) (过时)和最初的“庞大”Visual Studio。
最近，我发现了 VScode，并爱上了它(Atom 也只是直到我发现 VScode)。我调整了一下，将它设置为小型 C、C++项目的完整 IDE，特别是面向竞争性编程。

### 创建一个样本 C/C++项目

1.  在 [VSCode](https://code.visualstudio.com/) 中打开/创建一个空文件夹。
2.  在其中创建一个新的. cpp 文件，如下所示:

```
#include \<iostream\>
using namespace std;
int main()
{
 cout \<\< "Hello World" \<\< endl;
 // uncomment below line to stop cmd from exiting immediately in case of "externalConsole": true
 //system("pause");
 return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  在 VSCode 中安装*推荐的* C/C++扩展，重新加载。

<figure>[![](../Images/cc3931eb95dc4fc52f487514f28ae0ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1gsuKdM2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2A4fKHWJcmJmdeG66ai2E9bQ.png) 

<figcaption>官方 C/C++扩展为 VSCode</figcaption>

</figure>

### 安装 C/C++编译器

C/C++扩展**不包括**一个 C++编译器。因此，您将需要安装一个或使用已经安装在您的计算机上。

**Windows:** 下载 [MinGW64.zip](https://github.com/JerryGoyal/MinGW64/releases) (最新发布)解压到 c 盘。

**Mac:** [XCode](https://developer.apple.com/xcode/)

**Linux:**T2】GCC

此外，请确保将 C++编译器路径添加到您平台的环境变量中。对于 Windows MinGW64，请添加:C:\MinGW64\bin

### 启用智能感知

> 更新:最新版本不需要配置，默认情况下启用智能感知:)

### 运行并调试 C/C++代码

你会注意到还有一个**。示例项目中的 vscode** 文件夹。要配置“调试配置”,需要两个文件，其中包括 launch.json 和 tasks.json。vscode 文件夹。

如果我们第一次尝试调试，VSCode 可以创建并自动配置这些文件。为此，在 VSCode 中打开 C++文件，或者点击 **F5** 或者转到*调试- >开始调试*，选择 C++ (GDB/LLDB)，然后选择 g++。exe 生成并调试活动文件。

<figure>[![](../Images/ca53f64504ea1a4d3763aaff58800945.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nIackaej--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/936/1%2An7-rARwPBxEwtLF3GW1Wvw.png) 

<figcaption>选择 C++ (GDB/LLDB)</figcaption>

</figure>

<figure>[![](../Images/f6df1e4c3f9acd8726bd5ba96bc51b0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wPMe79mo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/914/1%2AqBxW08EA-FbfCpptmibcNQ.png) 

<figcaption>选择 g++。exe 构建并调试活动文件</figcaption>

</figure>

这会在中创建两个文件 launch.json 和 tasks.json。vscode 文件夹，如下所示(如果不正确，更新 MinGW64 路径)

请注意，我在 launch.json 中添加了一个可选配置 g++ build & run 活动文件，在 tasks.json 文件中添加了 g++ build & run，目的也是为了**在不调试**的情况下运行 C/C++代码。现在，您可以选择在开始调试时选择哪个配置。您可以删除不需要的配置。

<figure>[![](../Images/315a2b54bf18c1939a1b8e9255d14b2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iiEaQdww--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/559/1%2AzVnhLFwqnNg-kuaLhqDgow.png) 

<figcaption>运行&调试或者只运行代码</figcaption>

</figure>

#### [T1】launch . JSON](#launchjson)

```
{
 "version": "0.2.0",
 "configurations": [
 {
 "name": "g++.exe build and debug active file",
 "type": "cppdbg",
 "request": "launch",
 "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
 "args": [],
 "stopAtEntry": false,
 "cwd": "${workspaceFolder}",
 "environment": [],
 "externalConsole": false, //set to true to see output in cmd instead
 "MIMode": "gdb",
 "miDebuggerPath": "C:\\MinGW64\\bin\\gdb.exe",
 "setupCommands": [
 {
 "description": "Enable pretty-printing for gdb",
 "text": "-enable-pretty-printing",
 "ignoreFailures": true
 }
 ],
 "preLaunchTask": "g++.exe build active file"
 },
 {
 "name": "g++ build & run active file",
 "type": "cppdbg",
 "request": "launch",
 "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
 "args": [],
 "stopAtEntry": false,
 "cwd": "${workspaceFolder}",
 "environment": [],
 "externalConsole": false, //set to true to see output in cmd instead
 "MIMode": "gdb",
 "miDebuggerPath": "C:\\MinGW64\\bin\\gdb.exe",
 "setupCommands": [
 {
 "description": "Enable pretty-printing for gdb",
 "text": "-enable-pretty-printing",
 "ignoreFailures": true
 }
 ],
 "preLaunchTask": "g++ build & run active file"
 }
 ]
} 
```

Enter fullscreen mode Exit fullscreen mode

#### tasks.json

```
{
 "tasks": [
 {
 "type": "shell",
 "label": "g++.exe build active file",
 "command": "C:\\MinGW64\\bin\\g++.exe",
 "args": [
 "-g",
 "${file}",
 "-o",
 "${fileDirname}\\${fileBasenameNoExtension}.exe"
 ],
 "options": {
 "cwd": "C:\\MinGW64\\bin"
 }
 },
 {
 "type": "shell",
 "label": "g++ build & run active file",
 "command": "C:\\MinGW64\\bin\\g++.exe",
 "args": [
 "${file}",
 "-o",
 "${fileDirname}\\${fileBasenameNoExtension}.exe"
 ],
 "options": {
 "cwd": "C:\\MinGW64\\bin"
 }
 }
 ],
 "version": "2.0.0"
} 
```

Enter fullscreen mode Exit fullscreen mode

> 可以将 launch.json 中的 externalConsole 设置为 true，以查看 cmd 中的代码输出。

**重启 VSCode** 以使新添加的编译器路径生效。

打开任何一个 C/C++文件，设置一些断点(或者不设置)，点击绿色的播放按钮。

> *(调试快捷键:_ **_F5* ** *)*

[![](../Images/7ccda51283931025011e7af9263f5b39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ugJa1JWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9cwk-zFyRakslR73wVVTJg.png)

**提示**:隐藏*。在 VSCode 的侧面资源管理器中打开 settings 并粘贴下面的 config:

```
"files.exclude": {
 "\*.exe": true
 } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 更喜欢在 JAVA 工作？

我还在这里添加了一个关于如何在 VSCode 中运行和调试 Java 代码的答案[。](https://stackoverflow.com/questions/39582491/debug-run-standard-java-in-visual-studio-code-ide-and-os-x/46128218#46128218)

* * *

感谢阅读，你也可以在 Twitter 上关注我 [@GorvGoyl](https://twitter.com/@GorvGoyl)