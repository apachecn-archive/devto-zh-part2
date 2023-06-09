# 组织导出挂钩

> 原文：<https://dev.to/gonsie/org-export-hooks-2eog>

在我寻求一个简化的、基于组织的发布方案时，我想在导出文件时添加一些信息。也就是说，我想在文件中添加一些元数据，比如:

```
#+property: exported: 2018-01-30 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看我是否可以通过 Org-mode 原型这个功能！

## 钩子和有用的函数

我可以想到一些我需要的函数和/或变量:

*   [stack overflow:org-export-preprocess-final-hook](https://stackoverflow.com/questions/34397046/org-export-hook-does-not-trigger#34397363)
*   [组织模式时间戳格式](http://www.howtobuildsoftware.com/index.php/how-do/c0Og/datetime-emacs-org-mode-org-mode-timestamp-format-when-exported)
*   来自`C-h f org-insert-TAB`的有趣函数:
    *   `org-insert-comment`
    *   `org-insert-time-stamp`
    *   `end-of-buffer`哪些引用了`(goto-char (point-max))`

让我们将它们放入一个 elisp 片段中。

```
(add-hook 'org-export-preprocess-final-hook
      (save-excursion
        (goto-char (point-max))
        (insert "\n#+property: exported: ")
        (org-insert-time-stamp (current-time))
        (insert "\n"))) 
```

Enter fullscreen mode Exit fullscreen mode

我想这正是我想要的。理想情况下，属性应该靠近(但不是在)我的文档的顶部，但目前这似乎太难了。注意，执行这个块会将`#+property`放在文档的末尾。这是我们想要的行为，但是它不应该在执行块时发生，而应该在导出时发生。此外，该函数将不断插入更多的文本，而不是更新现有的属性。

这看起来像是一种相当粗糙的设置属性的方式。org 中必须包含一些内容。我们试试`org-set-property`功能。

```
(save-excursion
      (goto-char (point-min))
      (org-set-property "exported:" (format-time-string (car org-time-stamp-formats) (current-time)))) 
```

Enter fullscreen mode Exit fullscreen mode

恶心。它没有转到文件的开头，只是在当前子树中添加了一个属性抽屉。转到文件的开头只是插入带有错误的时间戳:“在位置 1 的第一个标题之前”。

## StackExchange 来救援

[这个 StackExchange 问题](https://emacs.stackexchange.com/questions/21459/programmatically-read-and-set-buffer-wide-org-mode-property)把我引向了强大的方向`org-element-parse-buffer`。实际上，有一个解决方案包含了一组相当优雅的`org-global-prop`函数，我想把它们打包并归入 <sup>[1](#fn.1)</sup> 。让我们试试吧:

```
(org-global-prop-set "exported:" (format-time-string (car org-time-stamp-formats) (current-time))) 
```

Enter fullscreen mode Exit fullscreen mode

那个功能管用！作为一个钩子呢？

```
(add-hook 'org-export-before-parsing-hook
      '(org-global-prop-set "exported:" (format-time-string (car org-time-stamp-formats) (current-time)))) 
```

Enter fullscreen mode Exit fullscreen mode

啊，胡克死了。通读官方 org 文档，似乎每个钩子都在当前缓冲区的副本上操作，而不是我想要的。

## 钩住某物

没关系，我可以用另一种方式侵入。让我们将执行导出的键重新绑定到一个新函数，该函数将执行导出属性更新。

```
;; add exported: property with date to org files when exporting
    (defun my/org-export-dispatch ()
      "updateds the exported: property before opening the dispatch"
      (org-global-prop-set "exported:" (format-time-string (car org-time-stamp-formats) (current-time)))
      (org-export-dispatch))

(add-hook 'org
              '(local-set-key (kbd "C-c C-e") 'my/org-export-dispatch)) 
```

Enter fullscreen mode Exit fullscreen mode

不完美(属性后的空格/换行符是 globbed ),但现在足够好了。有一天我将不得不把这个功能集成到发布过程中(而不是这个黑客)，但是，嘿，它工作了。

# 脚注

<sup>[1](#fnr.1)</sup> 我找到了作者托拜厄斯·扎瓦达，把[的代码放到了 GitHub](https://github.com/gonsie/org-global-prop.el) 上。