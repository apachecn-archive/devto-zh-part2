# 使用“查找”进行文件搜索(及更多)

> 原文：<https://dev.to/andreanidouglas/file-search-and-more-with-find-3le3>

能够使用命令行是一个生产力助推器，所有可以用鼠标和键盘在漂亮的 GUI 应用程序上完成的系统操作都可以通过 CLI 完成。

find 命令的多功能性令人印象深刻，例如，您可以查找您在硬盘上“丢失”的文件。

`find / -name mylostfile`

对于 steps，file 程序的第一个参数是搜索的初始路径(recursevely，“/”表示根):

`-name`参数指定您正在寻找的文件名。

**奖励:**您可以在`-name`参数中使用通配符

`find / -name "*.jpg"`

这个基本的功能，find 已经价值连城，但是仅仅寻找一个文件只是这个泰坦尼克号杀手冰山的一角。

**执行参数**

这里是`find`的亮点，你可以在你找到的文件列表上执行任何命令，只需将文件名替换为{}

`find . -name "*.mp4" -exec ffmpeg -i {} -r 30 {.}.avi \;`

再一步一步:

`-name "*.mp4"`搜索任何扩展名为 mp4 的文件

`-exec ffmpeg -i {} -r 30 {.}.avi \;`调用 ffpmeg 执行一个操作，对一个视频文件保持 30fps，转换成 avi。

`{}`当前文件。

`{.}`没有扩展名的当前文件。

*PS:* :命令末尾的分号非常重要，你可以在: [`file` man file](https://linux.die.net/man/1/find) 找到更多细节

ffmpeg details: [ffmpeg](https://ffmpeg.org/ffmpeg.html)

* * *

**你喜欢使用终端吗？在评论中与`find`分享你的经历，我们可以一起学习！**