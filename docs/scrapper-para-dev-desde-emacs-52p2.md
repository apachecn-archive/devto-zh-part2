# 刮削器

> 原文：<https://dev.to/yorodm/scrapper-para-dev-desde-emacs-52p2>

# 刮削器。

刚在《t0》dev 上发表了一篇关于如何制作 [scraper](https://es.wikipedia.org/wiki/Web_scraping) 以获得[主饲料](https://dev.to)的称号的文章。文章源于对 Common Lisp 作为当今技术的相关性的友好讨论，结果令我非常满意。

我注意到使用过的图书馆后面很简单，所以我决定看看是否有 Emacs Lisp 和……TL 的图书馆；医生，这是刮胡刀

```
;;; Eval this in the scratch buffer, first make sure to get
;;; `elquery' from MELPA.
;;; Eval this in a buffer and get the headlines in *another* buffer
;;; named *dev*
(require 'elquery)
(let ((dev-buffer (generate-new-buffer "*dev*")))
  (with-current-buffer (url-retrieve-synchronously "https://dev.to")
    ;; Feel free to hate this
    (dolist (elt (mapcar 'elquery-text (elquery-$ "div.single-article h3"
                                                  (elquery-read-string (buffer-string)))))
      (with-current-buffer dev-buffer (insert (concat elt "\n")))))) 
```

Enter fullscreen mode Exit fullscreen mode