# 为一致的开发环境引入 auto-nix-shell

> 原文：<https://dev.to/chrismwendt/introducing-auto-nix-shell-for-consistent-development-environments-5h59>

我创建了 [auto-nix-shell](https://github.com/chrismwendt/auto-nix-shell) 来在`cd`进入/我们的目录时自动进入/退出`nix-shell`，该目录有一个 nix 配置文件(`default.nix`)。它甚至在执行下一个 shell 命令之前检查配置更改(使用 [`fish_preexec`](https://github.com/fish-shell/fish-shell/pull/1666) 和 [bash-preexec](https://github.com/rcaloras/bash-preexec) )，并在您`git checkout <some other commit>`时重新加载配置