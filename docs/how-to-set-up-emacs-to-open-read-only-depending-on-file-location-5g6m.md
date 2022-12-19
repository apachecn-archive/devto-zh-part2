# 如何设置 Emacs 根据文件位置以只读方式打开

> 原文：<https://dev.to/bravotan/how-to-set-up-emacs-to-open-read-only-depending-on-file-location-5g6m>

为了防止在打开 virtualenv 等虚拟环境的库时意外编辑文件，如果更改 Emacs 设置，可以在只读状态下自动打开它，而无需执行 C-x C-q。

设置目标 pyenv 目录的示例如下。

```
(add-hook 'find-file-hook
          (lambda ()
            (dolist (pattern '("~/.pyenv/versions/.*"
                               ;; add here
                               ))
              (if (string-match (expand-file-name pattern) buffer-file-name)
                  (read-only-mode)
                )))) 
```

Enter fullscreen mode Exit fullscreen mode