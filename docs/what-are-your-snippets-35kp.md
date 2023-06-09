# 你有哪些片段？

> 原文：<https://dev.to/codevault/what-are-your-snippets-35kp>

自从像 Atom、Sublime 和 Visual Studio Code 这样的极简 ide 出现以来，用户代码片段已经变得越来越受欢迎，因为它们有助于提高生产力，这使得一些样板代码感觉像是简单地填写在线表单的空白。

那么，你最有用的片段是什么，为什么(对于任何语言来说)？让我们互相帮助找到一些有用的片段或新想法！在下面评论你的吧！

我先说:

*$0 是放置代码片段后光标将停留的位置*

*   履行承诺的`then`和`fail`(或`catch`)片段:

```
// prefix: '.th'
.then(function(res) {
    $0
}) 
```

```
// prefix: '.fa'
.fail(function(err) {
    $0
}) 
```

```
// prefix: '.ca'
.catch(function(err) {
    $0
}) 
```

*   经常使用的 HTML 输入。这是上面有最多样板文件的一个:

```
<!-- prefix: 'idate' -->
<div class="input-group">
    <span class="input-group-addon">$1</span>
    <input class="form-control" datetimepicker options="dateOptions" ng-model="$2">
    <span class="input-group-addon">
        <span class="glyphicon glyphicon-calendar"></span>
    </span>
</div> 
```