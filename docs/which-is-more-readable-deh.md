# 哪个可读性更强？

> 原文：<https://dev.to/andy/which-is-more-readable-deh>

我目前正在重构/重写一些代码，我不能决定哪一个比另一个更可读。在 Ruby 中，It *是*,但是我认为在这种情况下可读性是与语言无关的。

```
json_data = { user: add_user_data(mentioner) }
json_data[:comment] = add_comment_data(mentionable) if mentionable_type == "Comment"

json_data = {}
json_data[:user] = add_user_data(mentioner)
json_data[:comment] = add_comment_data(mentionable) if mentionable_type == "Comment" 
```

Enter fullscreen mode Exit fullscreen mode

我其实也很喜欢这个语法，但不幸的是它无效:

```
json_data = {
  user: add_user_data(mentioner),
  comment: add_comment_data(mentionable) if mentionable_type == "Comment"
}

# guess you can't have conditionals when declaring hashes ☹️ 
```

Enter fullscreen mode Exit fullscreen mode