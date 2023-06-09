# Windows 10 重置管理员帐户密码

> 原文：<https://dev.to/rdumais/windows-10-reset-administrator-account-password-e9i>

# 简介

本文将使用已知的 Windows 后门来授权访问 Windows 登录屏幕上的 cmd 提示符。该过程临时替代“粘滞键”功能，而是触发 cmd 提示，因此允许在登录前更改用户帐户。

# 要求

-90 分钟
-USB 驱动器，使用前已格式化
-安装了 Windows 10 的额外计算机

# 程序

**A)** 在另一台 Windows 10 电脑上创建 Windows 10 修复驱动。使用 USB 作为恢复驱动器。这是指南中最长的部分。

1)开始菜单>“创建恢复驱动器”
2)按照向导进行操作。

**B)** 在电脑的 BIOS 设置中启用 USB 作为引导优先级。此功能因每个主板制造商而异。有关 BIOS 访问和设置的具体信息，请搜索您的主板型号。

**C)** 在电脑关机的情况下，用丢失的密码将之前创建的修复驱动器连接到电脑。打开电脑电源，启动修复驱动器(USB)。

**D)** 启动到修复驱动器时:

1)选择您的键盘布局。“美国”通常位于列表的第一位
2)选择“故障排除”选项
3)选择“高级选项”
4)选择“命令提示符”选项

**E)** 确定 Windows 10 操作系统安装的是哪个盘符。如果您知道 Windows 的位置，请跳到步骤 f。
如果不确定 Windows 操作系统安装在哪个驱动器上:

1)在 cmd 提示符下键入:

```
wmic logicaldisk get name 
```

获取正在使用的驱动器列表。把这些写下来。

2)从列表中的第一个字母开始，在 cmd 提示符下键入驱动器名称，例如“E:”并按回车键。命令提示符会将驱动器目录更改为 E:然后您会希望输入' dir '来查看驱动器的内容。如果内容列表不包含“Windows”文件夹，使用本段中的相同命令移动到列表中的下一个字母。

**F)** 一旦我们有了 Windows 10 操作系统的正确盘符，我们就可以开始在命令提示符下运行命令了。
注意:将“LETTERNAME”更改为实际驱动器的盘符。

1)类型:

```
copy LETTERNAME:\windows\system32\sethc.exe LETTERNAME:\ 
```

Cmd 提示符应该会通知您文件已被复制。

2)类型:

```
 copy /y LETTERNAME:\windows\system32\cmd.exe LETTERNAME:\windows\system32\sethc.exe 
```

Cmd 提示符应该会再次通知您文件发生了变化。

3)退出命令提示符，关闭计算机(通过 windows，在前面的“故障排除”屏幕上)，并在完全断电后取出 USB 修复驱动器。

启动电脑，进入登录界面。

1)在屏幕的右下角应该是“轻松访问”按钮。点击按钮并启用“粘滞键”。按五次 Shift 键。
3)应弹出一个 cmd 提示符。在命令提示符下运行最后一个命令:

```
 net user $USERNAME $PASSWORD 
```

其中$USERNAME 是需要恢复的用户帐户，$PASSWORD 是将要设置的新密码。
4)退出命令行。
5)使用创建的新密码登录用户帐户。

**H)** 启动后，重启电脑并登录以确保更改生效。建议运行 windows 更新和更新驱动程序。

*不要脸的塞了这篇文章原来贴的地方，在我的博客上@[https://ryanduma.is/blog](https://ryanduma.is/blog)T3】*