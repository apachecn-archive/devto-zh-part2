# 用公共 Lisp 清理开发

> 原文：<https://dev.to/yorodm/scrapping-dev-with-common-lisp-6j0>

由于 scraper 似乎正在成为 [DEV](https://dev.to) 的趋势，一个朋友挑战我用 Common Lisp 做一个 web scraper(是的，我是一个 Lisper，你们应该`(with-it :deal)`)。我知道他认为“Lisp 已经过时了，让一切变得更加困难和冗长”，所以我决定接受挑战，写下这篇文章。我花了 1 分钟。我的网络很慢，Emacs 在 Windows 10 中要花很长时间才能打开。

```
;;; Just eval this in your favorite Lisp IDE.
;;; Get the libs
(ql:quickload '(:dexador :plump :lquery))
;;; Get the page
(defvar *front-page* (dex:get "https://dev.to"))
;;; Parse the page
(defvar *data* (plump:parse *front-page*))
;; Get the posts!
(lquery:$ *data* "div.single-article h3" (text)) 
```

Enter fullscreen mode Exit fullscreen mode

> 更新:感谢 [@RainerJoswig](https://twitter.com/RainerJoswig) 捕捉到代码中的一些错别字！！！