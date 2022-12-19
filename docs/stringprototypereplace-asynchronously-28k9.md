# String.prototype .异步替换？

> 原文：<https://dev.to/ycmjason/stringprototypereplace-asynchronously-28k9>

*原帖:[https://www.ycmjason.com/blog/2018/04/28.html](https://www.ycmjason.com/blog/2018/04/28.html)T3】*

> 本文假设您对 RegExp 有基本的了解。

## 背景

上周我和 T2 的 vuepress 一起工作，我意识到我想把我的长篇大论分成几部分。所以我提出了这个[问题](https://github.com/vuejs/vuepress/issues/222)。传说中，尤雨溪建议使用`<!-- include ./sectionA.md -->`。然后我接受了他的建议，开始钻研 vuepress 的代码。

### 

在我解释我如何解决这个问题之前，我想确定我们都在同一页上。我的解决方案是基于`String.prototype.replace`函数，我将非常简要地解释这个函数是如何工作的。该函数接受两个参数:

1.  要替换的内容(RegExp | String)
2.  替换为什么(字符串|函数)

#### String . prototype . replace(*字符串*，*字符串*)

```
const str = 'I am very happy, happy, happy.';
str.replace('happy', 'sad'); // I am very sad, happy, happy. 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子显示了我们如何替换字符串中的单词。请注意，只有第一次出现的`happy`被替换为`sad`。这种行为类似于在没有全局标志的情况下传入 RegExp。

#### String . prototype . replace(*字符串*，*函数*)

```
const str = 'I am very happy, happy, happy.';
str.replace('happy', word => 'not ' + word);
// ^ I am very not happy, happy, happy. 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过传递一个 replacer 函数来检索匹配的单词。replacer 函数返回的值将用于替换`word`。

这种用例很少见，可能不是很有用，因为你已经知道了目标词。你可以简单地做`str.replace('happy', 'not happy')`来达到同样的效果。

#### String . prototype . replace(*RegExp*， *String* )

```
const str = 'I am very happyyyyy, happy, happy.';
str.replace(/happ(y+)/, 'sleep$1'); // I am very sleepyyyyy, happy, happy.
str.replace(/happ(y+)/g, 'sleep$1'); // I am very sleepyyyyy, sleepy, sleepy. 
```

Enter fullscreen mode Exit fullscreen mode

应该很简单。需要注意两件事:

1.  匹配“happy”和它后面所有的“y”。
2.  `$1`将被 RegExp 的组`()`中匹配的内容替换。您可以有多个组，只需使用`$2`、`$3`、`$4`作为它们的占位符。

#### string . prototype . replace(*RegExp*，*函数*)

```
const str = 'I am very happyyyyy, happy, happyy.';

str.replace(/happ(y+)/, (match, ys) => {
    // match: 'happyyyyy'; ys: 'yyyyy'
    return 'sleep' + ys;
}); // I am very sleepyyyyy, happy, happyy.

str.replace(/happ(y+)/g, (match, ys) => {
    // This function is called 3 times:
    //     1\. match: 'happyyyyy'; ys: 'yyyyy'
    //     2\. match: 'happy'; ys: 'y'
    //     3\. match: 'happyy'; ys: 'yy'
    return 'sleep' + ys;
}); // I am very sleepyyyyy, sleepy, sleepyy. 
```

Enter fullscreen mode Exit fullscreen mode

注释应该是不言自明的。

### 同步方式

回到我们遇到的问题，用`./sectionA.md`的内容替换`<!-- include ./sectionA.md -->`。

任何一个正派的正则表达式开发者都可以想出一个正则表达式来匹配这个占位符，我们想出了这样的东西:

```
const placeholderRe = /<!--\s*include\s+([^\s]+)\s*-->/g 
```

Enter fullscreen mode Exit fullscreen mode

注意:`\s`匹配任何空格/制表符等。更多信息见[此处](https://www.w3schools.com/jsref/jsref_regexp_whitespace.asp)。

这个 RegExp 将匹配整个占位符，并将文件名分组在`include`之后。

所以我基本上使用`String.prototype.replace`来完成这项工作:

```
const { readFileSync, existsSync } = require('fs');

const replaceIncludePlaceholdersWithFileContents = str => {
    const placeholderRe = /<!--\s*include\s+([^\s]+)\s*-->/g;
    return str.replace(placeholderRe, (placeholder, filename) => {
        if (!existsSync(filename)) return placeholder;
        return readFileSync(filename, 'utf8');
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，我们只需要再处理一种情况，即当被包含的部分也包含`<!-- include file.md -->`时。显然，这变成了一个递归问题。处理这种情况的方法就是简单地进行*信念跳跃*。

简单地对当前文件包含的每个文件的内容递归地应用`replaceIncludePlaceholdersWithFileContents`就可以了！

所以我们有这样的东西:

```
const { readFileSync, existsSync } = require('fs');

const replaceIncludePlaceholdersWithFileContents = str => {
    const placeholderRe = /<!--\s*include\s+([^\s]+)\s*-->/g;
    return str.replace(placeholderRe, (placeholder, filename) => {
        if (!existsSync(filename)) return placeholder;
        return replaceIncludePlaceholdersWithFileContents(
            readFileSync(filename, 'utf8')
        );
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们的基本情况是，当包含的文件不包含占位符时，函数应该终止，因为 replacer 函数不会被调用。

### 异步方式

所以我提交了 pull 请求，一些反馈建议我使用`fs.readFile`，它是`fs.readFileSync`的异步版本。

我马上意识到，如果我有一个名为`asyncStringReplace(str, search, replacer)`的函数，它做`String.prototype.replace`所做的事情，但允许`replacer`返回一个`Promise`，那么我只需将我的代码改为下面的代码，它就会工作。

```
const { readFile, existsSync } = require('fs-extra');

const replaceIncludePlaceholdersWithFileContents = async str => {
    const placeholderRe = /<!--\s*include\s+([^\s]+)\s*-->/g;
    return await asyncStringReplace(str, placeholderRe, async (placeholder, filename) => {
        if (!existsSync(filename)) return placeholder;
        return await replaceIncludePlaceholdersWithFileContents(
            await readFile(filename, 'utf8')
        );
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

花了这么多时间考虑占位符的替换，我愿意尽可能地保留已经存在的逻辑。

所以现在我需要写的只是`asyncStringReplace`方法。

## asyncStringReplace

`asyncStringReplace`方法应该接受三个参数:

1.  `str` -原始字符串
2.  `regex` -表示要替换的`str`的子字符串的正则表达式
3.  `aReplacer` -接受每个匹配的异步函数，应该返回`Promise`。

我基本上是从 [mdn](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec) 复制了使用`RegExp.prototype.exec`循环匹配的“while-loop”。通过使用`RegExp.prototype.exec`,我们可以跟踪每场比赛的`RegExp.lastIndex`和`match.index`,我想不出用`String.prototype.match`来实现这一点的方法。

```
const asyncStringReplace = async (str, regex, aReplacer) => {
    const substrs = [];
    let match;
    let i = 0;
    while ((match = regex.exec(str)) !== null) {
        // put non matching string
        substrs.push(str.slice(i, match.index));
        // call the async replacer function with the matched array spreaded
        substrs.push(aReplacer(...match));
        i = regex.lastIndex;
    }
    // put the rest of str
    substrs.push(str.slice(i));
    // wait for aReplacer calls to finish and join them back into string
    return (await Promise.all(substrs)).join('');
}; 
```

Enter fullscreen mode Exit fullscreen mode

我的方法基本上将给定的`str`和给定的`regex`分割成子串，并将它们放入`substrs`。

`substrs`因此包含:

```
[
    /* first loop in while */
    NON_MATCHING_STRING,
    aReplacer(MATCHING_STRING),

    /* second loop in while */  
    NON_MATCHING_STRING,
    aReplacer(MATCHING_STRING),

    /* ... */,

    /* n-th loop in while */  
    NON_MATCHING_STRING,
    aReplacer(MATCHING_STRING),

    /* substrs.push(restStr) */
    REST_NON_MATCHING_STRING
] 
```

Enter fullscreen mode Exit fullscreen mode

例如
如果我们调用下面的

```
asyncStringReplace('i am happyy, happy === happyyy very!', /happ(y+)/g, someAsyncReplacer); 
```

Enter fullscreen mode Exit fullscreen mode

相应的`substrs`将是:

```
[
    /* first loop in while */
    'i am ',
    someAsyncReplacer('happyy', 'yy'),

    /* second loop in while */
    ', ',
    someAsyncReplacer('happy', 'y'),

    /* third loop in while */
    ' === ',
    someAsyncReplacer('happyyy', 'yyy'),

    /* substrs.push(restStr) */
    ' very!'
] 
```

Enter fullscreen mode Exit fullscreen mode

注意，由于`aReplacer`是一个异步函数，`aReplacer(MATCHING_STRING)`因此是一个`Promise`。`Promise.all`在这里可以用来构造一个`Promise`，当这个列表中的所有承诺都被解析时，这个【】就会被解析。

最后一行

```
 return (await Promise.all(substrs)).join('') 
```

Enter fullscreen mode Exit fullscreen mode

`await Promise.all(substrs)`会屈服于一个字符串数组，而`.join('')`会将所有字符串重新连接在一起。

如何应用的一个例子:

```
const { readFile, existsSync } = require('fs-extra');

const replaceIncludePlaceholdersWithFileContents = async str => {
    const placeholderRe = /<!--\s*include\s+([^\s]+)\s*-->/g;
    return await asyncStringReplace(str, placeholderRe, async (placeholder, filename) => {
        if (!existsSync(filename)) return placeholder;
        return await replaceIncludePlaceholdersWithFileContents(
            await readFile(filename, 'utf8')
        );
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode