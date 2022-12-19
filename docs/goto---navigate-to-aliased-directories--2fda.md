# goto -导航到别名目录

> 原文：<https://dev.to/iridakos/goto---navigate-to-aliased-directories--2fda>

我创建了一个名为 [goto](https://github.com/iridakos/goto) 的 bash 实用程序，它允许用户导航到别名目录。

[![goto](img/34de45672590e2a3e92c6bc38d58eb74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0BXdG5Vj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/iridakos/goto/master/doc/goto.gif)

它附带了一个很好的自动完成脚本，这样每当你在`goto`命令后按下`tab`键，bash 就会提示可用别名的建议:

```
$ goto <tab>
bc /etc/bash_completion.d                     
dev /home/iridakos/development
rubies /home/iridakos/.rvm/rubies 
```

Enter fullscreen mode Exit fullscreen mode

### 举例

以下命令将为长路径注册一个别名:

```
goto --register workdir /mnt/development/projects/go/workspace 
```

Enter fullscreen mode Exit fullscreen mode

之后，用户只需:
即可导航至该文件夹

```
goto workdir 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 [GitHub](https://github.com/iridakos/goto) 上找到代码以及安装和使用指南。

## 更新 2018-03-25

该工具也可以在 zsh 中使用，macOS 用户也可以使用 brew 公式(`brew install goto`)