# 一个自动互动的承诺前清单，在电视购物的风格

> 原文：<https://dev.to/victoria/an-automatic-interactive-pre-commit-checklist-in-the-style-of-infomercials-14i7>

你说那是什么？你已经厌倦了定期的旧的无聊的纸质清单？好吧，我的朋友，今天是你的幸运日！你，是的，*你，*可以成为一个全新的*自动交互预提交钩子清单的骄傲拥有者！*你会喜欢这个的！你的生活会轻松很多！只要等到你的朋友看到你。

# 什么是预提交挂钩？

你知道吗，近五分之一的编码员都不好意思问这个问题。别担心，这很正常。在接下来的 60 秒中，我们将告诉你所有你需要知道的事情，让你充满自信地做出承诺。

一个 [Git 钩子](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)是 Git 的一个特性，它在有用的时候触发定制脚本。它们可以用于各种原因来帮助你自动化你的工作，最好的是，你已经有了它们！在您用`git init`初始化的每个存储库中，您将拥有一组驻留在`.git/hooks`中的示例脚本。它们都以`.sample`结尾，激活它们就像重命名文件删除`.sample`部分一样简单。

当一个存储库被克隆时，Git 挂钩不会被复制，所以你可以根据你的喜好将它们个性化。

我们今天要讨论的特别有用的时刻是*预提交*。这个钩子在你做`git commit`之后，写提交消息之前运行。以非零状态退出这个钩子将中止提交，这对于最后一分钟的质量检查非常有用。或者，一点乐趣。为什么不能两者兼得！

# 如何获得预提交清单？

我只想给我的家人和我的承诺最好的，这就是为什么我选择了一个交互式的承诺前清单。它不仅使用起来有趣，而且有助于保护我的项目安全，避免意外的错误！

太简单了！我只是写了一个 bash 脚本，可以读取用户输入，并把它作为一个名为`pre-commit`的文件放入`.git/hooks`中。然后我做`chmod +x .git/hooks/pre-commit`使它可执行，我就完成了！

哦，看，现在来了一个 bash 脚本示例！

```
#!/bin/sh

echo "Would you like to play a game?"

# Read user input, assign stdin to keyboard
exec < /dev/tty

while read -p "Have you double checked that only relevant files were added? (Y/n) " yn; do
    case $yn in
        [Yy] ) break;;
        [Nn] ) echo "Please ensure the right files were added!"; exit 1;;
        * ) echo "Please answer y (yes) or n (no):" && continue;
    esac
done
while read -p "Has the documentation been updated? (Y/n) " yn; do
    case $yn in
        [Yy] ) break;;
        [Nn] ) echo "Please add or update the docs!"; exit 1;;
        * ) echo "Please answer y (yes) or n (no):" && continue;
    esac
done
while read -p "Do you know which issue or PR numbers to reference? (Y/n) " yn; do
    case $yn in
        [Yy] ) break;;
        [Nn] ) echo "Better go check those tracking numbers!"; exit 1;;
        * ) echo "Please answer y (yes) or n (no):" && continue;
    esac
done exec <&- 
```

Enter fullscreen mode Exit fullscreen mode

# 拿我的钱！

不要拖延！利用*现在*这个慷慨的*一次性优惠！*现在，您就可以以非常低的价格获得一份交互式提交前挂钩清单...免费？等等，这个剧本是谁写的？