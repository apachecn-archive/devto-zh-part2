# 让我们重构-第 1 部分

> 原文：<https://dev.to/chinchang/lets-refactor---part-1-4ffk>

从重构代码可以使代码无 bug 且更健壮的小例子开始这个系列的文章。这是第一部分。尽情享受吧！

```
document.querySelector('input').addEventListener('input', e => {
 if (e.target.value.indexOf('>') === 0) {
  isCommandMode = true;
 }
} 
```

我希望当有人键入`>`作为输入的第一个字符时，`isCommandMode`应该被设置为`true`。但是如果条件不满足，它应该变成`false`

你可能会说我们可以添加缺少的 else 条件，就像这样:

```
document.querySelector('input').addEventListener('input', e => {
 if (e.target.value.indexOf('>') === 0) {
  isCommandMode = true;
 }
 else {
  isCommandMode = false;
 }
} 
```

这解决了我们的目的，但是我们可以用一种更好的方式来写它，这将使代码更加简洁，可读性更好，并且它也可以从一开始就防止我们的错误:

```
document.querySelector('input').addEventListener('input', e => {
 isCommandMode = e.target.value.indexOf('>') === 0;
} 
```

现在一条线可以自己处理这两种情况。很甜蜜，不是吗？希望您在经历这一重构过程中感到愉快。直到下一次。
你也可以[在推特](https://twitter.com/chinchang457)上关注我，了解未来的帖子。