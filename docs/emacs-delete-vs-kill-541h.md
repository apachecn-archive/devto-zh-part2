# Emacs 删除与删除

> 原文：<https://dev.to/gonsie/emacs-delete-vs-kill-541h>

一直困扰我的一件事是 Emacs 的夺命环。它列出了你杀死的东西，这很好，但我经常只是想快速删除东西，而不是保存它。因此，我需要更改默认设置，以同时启用删除和删除单词和行。

基本术语(至少对于这篇博文)；

*   kill 意味着删除文本并将其放入 kill 环
*   删除意味着删除文本并且不把它放入 kill ring

## 了解按键绑定

Emacs 的默认行为取决于底层操作系统。这是因为按键首先由操作系统翻译，然后传递(如果它们不触发操作系统功能)给应用程序，例如 Emacs。键盘设置以及终端内部运行的 Emacs 实例会使这变得更加复杂。

要找出 Emacs 本身对每个键的名称，使用`C-h k KEY`或`M-x describe-key KEY`。在重新绑定这些键时，这个内部名称将会派上用场。

对于扩展键盘，通常有两个“删除”键，一个执行退格，一个执行向前删除。向前删除通常出现在“小键盘”或数字键盘上，因此被 Emacs 称为`kp-delete`。

### Mac 键盘

Mac 键盘有以下修饰键:Shift、Control、Option (Alt)和 Command。虽然已经为 Emacs(分别称为`S`和`C`)映射了 Shift 和 Control，但是没有映射 Option 和 Command 键。我使用 Option/Alt 作为元(`M`)键，Command 作为超级(`s`)键。

```
(setq mac-command-modifier 'super) ;; command -> s
(setq mac-option-modifier 'meta) ;; option/alt -> M 
```

Enter fullscreen mode Exit fullscreen mode

## 默认命令

默认情况下，`delete`键删除单个字母(向后)。在 Emacs 键绑定中，这有时被称为`backspace`(除了有时被称为`delete`)。`M-delete` ( `ALT-delete`在 Mac 键盘上)会把一个整字去掉，但是放在 kill 环里。杀一条线很简单:`C-k`，但这是向前杀一条线。

把最近被杀的字或行放回去的是`C-y`，叫猛拉。拉动环中的前一个项目是通过`M-y`实现的，它将在环中循环。

## 组合

这里有一个三维问题要解决，有 16 种可能的组合(14 种独特的操作):

1.  移除方向(向后或向前)
2.  移除类型(杀死或删除)
3.  删除大小(字符、单词、可视行、整行)

### 方向

我使用 Mac internal 来标记方向是向前还是向后。即使在笔记本电脑键盘上，我也可以使用`Fn-delete`来删除转发。

整行移除没有方向，必须同时绑定`delete`和`kp-delete`。

### 清除类型

未修改的删除键应该触发字符删除。使用 meta ( `M`)修饰符触发单词移除。使用 control ( `C`)修饰符触发行删除。

### 保存物品到夺命环

我使用 Shift ( `S`)修饰符显式地触发将删除的文本添加到 kill ring 中。

## 绑定按键

下表跟踪了必须实施的所有组合。实现看起来像:

```
(global-set-key (kbd "<<keybinding>>") '<<function>>) 
```

Enter fullscreen mode Exit fullscreen mode

| 方向 | 类型 | 救援 | 按键绑定 | 功能 |
| --- | --- | --- | --- | --- |
| 倒 | 茶 | 不 | `<delete>` | 不需要 |
|  | ~~字符~~ | ~~是~~ |  | 未实施 |
|  | 单词 | 不 | `<M-delete>` | `backward-delete-word` * |
|  | 单词 | 是 | `<M-S-backspace>` | `backward-kill-word` |
|  | 线条 | 不 | `<C-backspace>` | `backward-delete-line` * |
|  | 线条 | 是 | `<C-S-backspace>` | `backward-kill-line` * |
| 向前 | 茶 | 不 | `<kp-delete>` | `delete-forward-char` |
|  | ~~字符~~ | ~~是~~ |  | 未实施 |
|  | 单词 | 不 | `<M-kp-delete>` | `delete-word` * |
|  | 单词 | 是 | `<M-S-kp-delete>` | `kill-word` |
|  | 线条 | 不 | `<C-kp-delete>` | `delete-line` * |
|  | 线条 | 是 | `<C-S-kp-delete>` | `kill-line` |
| 两者 | 线条 | 不 | `<s-backspace>`和`<s-kp-delete>` | `delete-current-line` * |
| 两者 | 线条 | 是 | `<s-S-backspace>`和`<s-S-kp-delete>` | `kill-whole-line` |

标有*的函数必须执行，如下所示:

```
;; from https://stackoverflow.com/a/12990359/1160876
(defun backward-delete-word (arg)
  "Delete characters backward until encountering the beginning of a word.
With argument ARG, do this that many times."
  (interactive "p")
  (delete-region (point) (progn (backward-word arg) (point))))

(defun delete-word (arg)
  "Delete characters forwards until encountering the beginning of a word.
With argument ARG, do this that many times."
  (interactive "p")
  (delete-region (point) (progn (forward-word arg) (point))))

(defun backward-delete-line (arg)
  "Delete (not kill) the current line, backwards from cursor.
With argument ARG, do this that many times."
  (interactive "p")
  (delete-region (point) (progn (beginning-of-visual-line arg) (point))))

(defun delete-line (arg)
  "Delete (not kill) the current line, forwards from cursor.
With argument ARG, do this that many times."
  (interactive "p")
  (delete-region (point) (progn (end-of-visual-line arg) (point))))

(defun backward-kill-line (arg)
  "Kill the current line, backwards from cursor.
With argument ARG, do this that many times."
  (interactive "p")
  (kill-region (point) (progn (beginning-of-visual-line arg) (point))))

;; from https://stackoverflow.com/a/35711240/1160876
(defun delete-current-line (arg)
  "Delete (not kill) the current line."
  (interactive "p")
  (save-excursion
    (delete-region
     (progn (forward-visible-line 0) (point))
     (progn (forward-visible-line arg) (point)))))
```

## 总结

我对这些按键绑定映射到功能的清晰程度非常满意。让我们看看我是否真的能把这些用上！