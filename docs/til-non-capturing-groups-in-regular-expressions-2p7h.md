# TIL:正则表达式中的非捕获组

> 原文：<https://dev.to/stefanjudis/til-non-capturing-groups-in-regular-expressions-2p7h>

又到正则表达式时间了。🎉下面这个发现我不记得是在哪里看到的，但是用了这么多年正则表达式，我很惊讶我以前没见过。

让我们看一个显示捕获组的示例。

```
const regex = /(Jane|John|Alison)\s(.*?)\s(Smith|Smuth)/; 
```

Enter fullscreen mode Exit fullscreen mode

正则表达式定义我正在寻找一个非常特殊的名称组合。名字要以`Jane`、`John`或`Alison`开头，以`Smith`或`Smuth`结尾，还要有中间名。

```
const result = regex.exec('Jane Isabell Smith');
console.log(result[0]); // 'Jane Isabell Smith'
console.log(result[1]); // 'Jane'
console.log(result[2]); // 'Isabell'
console.log(result[3]); // 'Smith' 
```

Enter fullscreen mode Exit fullscreen mode

by `exec`返回的数组保存匹配的完整字符串，后跟定义的组。现在，要得到中间的名字，我必须查看正则表达式，找出它是正则表达式中的第二个组，并将在`result[2]`可用。

这没有什么特别的问题，但是我不感兴趣的组包含在结果中，这让我处理返回值有点困难。

**原来可以定义不被捕获的组！**

```
const regex = /(?:Jane|John|Alison)\s(.*?)\s(?:Smith|Smuth)/;
const result = regex.exec('Jane Isabell Smith');
console.log(result[0]); // 'Jane Isabell Smith'
console.log(result[1]); // 'Isabell' 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`(?:)`来不捕获组并从结果中删除它们。当您处理复杂的正则表达式时，这确实非常有用！🎉

正如戴夫·纽森指出的，还有[名为](https://mathiasbynens.be/notes/es-regexp-proposals#named-capture-groups)的抓捕小组正在赶来！“集团未来”看起来一片光明！