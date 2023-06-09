# 7zip 存档受到攻击！

> 原文：<https://dev.to/dskuratovich/7zip-archives-under-attack-3jb6>

*注意！本文是为稍微熟悉 BASH-console 的人准备的。如果你觉得你没有意识到它是如何工作的，请在评论中告诉我！*

> 我认为我心目中的那个是正确的，我只需要把那 200 个都试一试！

你想起什么了吗？

有时人们会面临这样的情况，他们不记得档案上的密码，但同时他们记得那是一种简单的密码。

有时我们知道密码是我们脑海中的一对情侣，但我们不记得到底是哪一对！对于这种情况，最好有某种机制或实践来找出正确的答案，这意味着我们需要思考并提出解决方案！

在本文中，我将向您展示如何使用 7zip 命令行界面不费吹灰之力就能攻击一个归档文件。

我们首先需要考虑的是我们记住了多少密码。假设我们记住了其中的 10 个，这意味着我们需要列出一个清单。

> 密码
> makemoneymonkey
> ...

好吧，让我们说，我们需要的文件，它已经包含所有需要的密码，你可以记住。
然后我们要结合 7zip 命令行界面使用这个文件。

在这种情况下，我们要做的只是从一个文件夹中运行下面的脚本，该文件夹中有一个将要使用该字典进行攻击的存档。好吧，让我们看看它在现实生活中是如何工作的！

我已经准备了一个脚本，允许我们针对每个密码运行 7zip 命令行界面，在这里查看:

```
#!/bin/bash

passwords_list="passwords-list"
archive=$1

echo "Starting attacking"

while IFS='' read -r line_data; do result=$(7z t $archive -p"$line_data" 2> /dev/null | grep -o "is Ok")

    if [[ $result ]]; then echo "Excellent! Password is $line_data"
        exit fi
done < $passwords_list

echo "Sorry we didn't find any appropriate password :-(" 
```

Enter fullscreen mode Exit fullscreen mode

*请告诉我这篇文章是否有帮助*