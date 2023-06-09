# Instalar 谷歌字体没有 meu MacOs

> 原文：<https://dev.to/andersonbraz/instalar-google-fonts-no-meu-macos-4bp9>

在此文章中，我将演示如何将 Google 提供的所有字体安装到您的 MAC 计算机上，以便以后在您个人计算机上使用的任何应用程序中使用这些字体。

## 导言

每当我需要更引人注目的排版或强调我的作品时，只要是在网路环境中，且更容易存取且更便宜，我都会使用 Google Fonts。随着时间的推移，我们需要在 Keynote、Powerpoint 或 Word 中使用这些字体。所以我们发现了一个非常简单的方法来解决这个小问题。

## 科莫解析器？

您只需在操作系统的字体目录(在本例中为 MacOs)中创建 Google Fonts 存储库的克隆。

### 谷歌字体

在终端上运行以下命令:

```
 cd ~/Library/Fonts/
 git clone https://github.com/google/fonts.git google-fonts 
```

Enter fullscreen mode Exit fullscreen mode

### 更新 Google 字体

```
cd ~/Library/Fonts/google-fonts/
git pull 
```

Enter fullscreen mode Exit fullscreen mode

### 删除 o 谷歌字体

```
rm -rf ~/Library/Fonts/google-fonts/ 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

如何轻松解决此小问题，并充分利用个人计算机上的 Google 字体。

开心点！

## 参考

[Github -谷歌字体](https://github.com/google/fonts)