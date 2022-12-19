# 在 Fedora Linux 上修复 ThinkPad X1 Yoga(第三代)睡眠和触控板问题

> 原文：<https://dev.to/kylerconway/fixing-thinkpad-x1-yoga-3rd-gen-sleep-and-trackpad-issues-on-fedora-linux-703>

所以你的联想 X1 Yoga 第三代在 Linux 上有些问题(特别是 Fedora 28 工作站，尽管这可能在其他地方也能工作)。这些问题如下:

1.  当盖子合上时，你无法让它正常睡眠和解除睡眠，当它从非睡眠状态中醒来时，触控板表现不佳，无法使用。

2.  下面是我用来测试和纠正问题的两个步骤(感谢许多其他人的调查工作)。

## 测试参数

启动时，按“e”编辑内核启动参数。然后在以“Linux 16”([source](https://docs.fedoraproject.org/f28/system-administrators-guide/kernel-module-driver-configuration/Working_with_the_GRUB_2_Boot_Loader.html#sec-Making_Temporary_Changes_to_a_GRUB_2_Menu))开头的行后输入以下内容:

```
acpi.ec_no_wakeup=1 psmouse.synaptics_intertouch=1 
```

Enter fullscreen mode Exit fullscreen mode

## 启动并测试修改后的内核参数

1.  开机。
2.  登录。
3.  合上笔记本电脑的盖子。
4.  等到你进入睡眠状态。
5.  打开盖子。
6.  测试鼠标。

如果成功了――祝贺你！您应该进入下一步，以使更改跨引导保持不变！

## 使永久

我是手动完成的(如下所示)，但是向导( [source](https://docs.fedoraproject.org/f28/system-administrators-guide/kernel-module-driver-configuration/Working_with_the_GRUB_2_Boot_Loader.html#bh-Adding_and_Removing_Arguments_from_a_GRUB_Menu_Entry) )允许命令行输入。我所做的如下:

```
sudo gedit /etc/sysconfig/grub 
```

Enter fullscreen mode Exit fullscreen mode

找到以下列开头的行:

```
GRUB_CMDLINE_LINUX 
```

Enter fullscreen mode Exit fullscreen mode

然后在该行的最后一个双引号(")前插入以下内容:

```
acpi.ec_no_wakeup=1 psmouse.synaptics_intertouch=1 
```

Enter fullscreen mode Exit fullscreen mode

在终端中运行以下命令使永久:

```
sudo grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg 
```

Enter fullscreen mode Exit fullscreen mode

你应该可以走了！

我注意到只剩下一个奇怪的地方――当我从睡眠中打开笔记本电脑时，键盘不会开始工作，直到我按下键盘上的“左 ALT”键(我知道这很奇怪)。小小的让步。我很高兴这个工作得很好！