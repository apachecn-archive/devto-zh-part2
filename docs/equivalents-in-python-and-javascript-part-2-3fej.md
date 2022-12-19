# Python 和 JavaScript 中的等价物。第二部分

> 原文：<https://dev.to/djangotricks/equivalents-in-python-and-javascript-part-2-3fej>

[![](../Images/9161b6f41e6bb923aa072dd841f18992.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y9C9sJw3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://4.bp.blogspot.com/-ZEZgva-20Og/WzrCcYKB-NI/AAAAAAAAB5w/8Pv9p7uP_kYXxEL-Y_wkFxvTtU_ZXJK6gCLcBGAs/s1600/equivalents-in-python-and-javascript-part-2.png)

[上次](https://dev.to/djangotricks/equivalents-in-python-and-javascript-part-1-3317)我们开始了一系列关于 **Python** 和 **JavaScript** 类比的新文章。我们看了列表、数组、字典、对象和字符串、条件赋值和解析整数。这一次我们将经历更有趣和更复杂的事情，比如将字典和列表序列化到 JSON，正则表达式操作，以及引发和捕捉错误。

## JSON

使用 API 时，将对象序列化为 JSON 格式并能够解析 JSON 字符串是非常常见的。

在 **Python** 中，它是通过`json`模块完成的，就像这样:

```
import json
json_data = json.dumps(dictionary, indent=4)
dictionary = json.loads(json_data) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们将 JSON 字符串中的嵌套元素缩进 4 个空格。

在 **JavaScript** 中有一个`JSON`对象，它有创建和解析 JSON 字符串的方法:

```
json_data = JSON.stringify(dictionary, null, 4);
dictionary = JSON.parse(json_data); 
```

Enter fullscreen mode Exit fullscreen mode

## 通过正则表达式拆分字符串

正则表达式是多用途工具，一旦你掌握了它，你就可以完成很多事情。

在上一篇文章中，我们看到了如何将字符串列表连接成一个单独的字符串。但是如何将一个长字符串分割成一系列字符串呢？如果分隔符不是逗号那样的单个字符，而是一系列可能的变体，那会怎么样呢？这可以通过正则表达式和`split()`方法来完成。

在 **Python** 中，`split()`方法属于正则表达式模式对象。这就是你如何通过标点符号将一个文本串分割成句子:

```
import re

# One or more characters of "!?." followed by whitespace delimiter = re.compile(r'[!?\.]+\s*')

text = "Hello!!! What's new? Follow me."
sentences = delimiter.split(text)
# sentences == ['Hello', "What's new", 'Follow me', ''] 
```

Enter fullscreen mode Exit fullscreen mode

在 **JavaScript** 中，`split()`方法属于字符串:

```
// One or more characters of "!?." followed by whitespace
delimiter = /[!?\.]+\s*/;

text = "Hello!!! What's new? Follow me.";
sentences = text.split(delimiter)
// sentences === ["Hello", "What's new", "Follow me", ""] 
```

Enter fullscreen mode Exit fullscreen mode

## 匹配字符串中的正则表达式模式

正则表达式通常用于验证表单中的数据。

例如，要验证输入的电子邮件地址是否正确，您需要将其与正则表达式模式进行匹配。在 **Python** 中，看起来应该是这样的:

```
import re

# name, "@", and domain pattern = re.compile(r'([\w.+\-]+)@([\w\-]+\.[\w\-.]+)')

match = pattern.match('hi@example.com')
# match.group(0) == 'hi@example.com'
# match.group(1) == 'hi'
# match.group(2) == 'example.com' 
```

Enter fullscreen mode Exit fullscreen mode

如果文本与模式匹配，它将使用`group()`方法返回一个 match 对象，以读取整个匹配的字符串，或者单独捕获用括号定义的模式。0 表示得到整个字符串，1 表示得到第一组的匹配，2 表示得到第二组的匹配，依此类推。如果文本与模式不匹配，将返回`None`值。

在 **JavaScript** 中，`match()`方法属于字符串，它返回一个匹配对象或者`null`。非常相似:

```
// name, "@", and domain
pattern = /([\w.+\-]+)@([\w\-]+\.[\w\-.]+)/;

match = 'hi@example.com'.match(pattern);
// match[0] === 'hi@example.com'
// match[1] === 'hi'
// match[2] === 'example.com' 
```

Enter fullscreen mode Exit fullscreen mode

**JavaScript** 中的 match 对象充当数组。它在第零个位置的值是整个匹配的字符串。其他索引对应于用括号定义的模式的捕获。

* * *

此外，有时您需要搜索字符串中是否存在某个特定的值，以及将在哪个字母位置找到它。这可以用`search()`方法来完成。

在 **Python** 中，这个方法属于正则表达式模式，它返回匹配对象。match 对象有一个`start()`方法，告诉匹配从哪个字母位置开始:

```
text = 'Say hi at hi@example.com'
first_match = pattern.search(text)
if first_match:
    start = first_match.start()  # start == 10 
```

Enter fullscreen mode Exit fullscreen mode

在 **JavaScript** 中，`search()`方法属于字符串，它只返回一个整数，告诉匹配从哪个字母位置开始。如果没有找到，则返回`-1`:

```
text = 'Say hi at hi@example.com';
first_match = text.search(pattern);
if (first_match > -1) {
    start = first_match;  // start === 10
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用正则表达式替换字符串中的模式

用正则表达式替换通常发生在清理数据或添加附加功能时。例如，我们可以获取一些文本，使所有的电子邮件地址都可以点击。

**Python** 开发者会使用正则表达式模式的`sub()`方法:

```
html = pattern.sub(
    r'<a href="mailto:\g<0>">\g<0></a>',
    'Say hi at hi@example.com',
)
# html == 'Say hi at <a href="mailto:hi@example.com">hi@example.com</a>' 
```

Enter fullscreen mode Exit fullscreen mode

**JavaScript** 开发者会使用字符串的`replace()`方法:

```
html = 'Say hi at hi@example.com'.replace(
    pattern, 
    '<a href="mailto:$&">$&</a>',
);
// html === 'Say hi at <a href="mailto:hi@example.com">hi@example.com</a>' 
```

Enter fullscreen mode Exit fullscreen mode

在 **Python** 中，捕获也称为“反向引用”，可以在替换字符串中访问为`\g<0>`、`\g<1>`、`\g<2>`等。在 **JavaScript** 中同样可以访问`$&`、`$1`、`$2`等。反向引用通常用于包装一些字符串或交换不同文本的位置。

* * *

也可以用函数调用替换匹配。这可以用来在替换中进行替换，或者统计或收集文本的某些特征。例如，在 **JavaScript** 中使用函数调用的替换，我曾经编写了一个全功能的 HTML 语法荧光笔。

这里，让我们将文本中的所有电子邮件地址都改为大写。

在 **Python** 中，替换函数接收匹配对象。我们可以使用它的`group()`方法对匹配的文本做一些事情，并返回一个文本作为替换:

```
text = pattern.sub(
    lambda match: match.group(0).upper(), 
    'Say hi at hi@example.com',
)
# text == 'Say hi at HI@EXAMPLE.COM' 
```

Enter fullscreen mode Exit fullscreen mode

在 **JavaScript** 中，替换函数接收整个匹配字符串、第一次捕获、第二次捕获等等。我们可以用这些值做我们需要做的事情，然后返回一些字符串作为替换:

```
text = 'Say hi at hi@example.com'.replace(
    pattern,
    function(match, p1, p2) {
        return match.toUpperCase();
    }
);
// text === 'Say hi at HI@EXAMPLE.COM' 
```

Enter fullscreen mode Exit fullscreen mode

## 错误处理

与 **Python** 相反，客户端 **JavaScript** 通常不用于保存或读取文件或连接远程数据库。所以与 **Python** 中的`try..except`类比相比， **JavaScript** 中的`try..catch`块相当罕见。

无论如何，错误处理可以用于在 **JavaScript** 库中实现和引发的自定义用户错误，并在主代码中捕获。

下面的例子在 **Python** 中展示了如何定义一个自定义异常类`MyException`，如何在函数中引发它，以及如何在`try..except..finally`块中捕捉和处理它:

```
class MyException(Exception):
    def __init__(self, message):
        self.message = message

    def __str__(self):
        return self.message

def proceed():
    raise MyException('Error happened!')

try:
    proceed()
except MyException as err:
    print('Sorry! {}'.format(err))
finally:
    print('Finishing') 
```

Enter fullscreen mode Exit fullscreen mode

下面的例子在 **JavaScript** 中做了完全相同的事情:这里我们定义了一个`MyException`类，在函数中抛出，在`try..catch..finally`块中捕获并处理它。

```
function MyException(message) {
   this.message = message;
   this.toString = function() {
       return this.message;
   }
}

function proceed() {
    throw new MyException('Error happened!');
}

try {
    proceed();
} catch (err) {
    if (err instanceof MyException) {
        console.log('Sorry! ' + err);
    }
} finally {
    console.log('Finishing');
} 
```

Enter fullscreen mode Exit fullscreen mode

两种语言中的`MyException`类都有一个参数`message`和一个使用`message`的值将自身表示为字符串的方法。

当然，只有在出错的情况下才会引发/抛出异常。并且定义模块设计中的错误。

## 外卖

*   JSON 的序列化在 **Python** 和 **JavaScript** 中非常简单。
*   在处理文本数据时，正则表达式可以用作多种工具。
*   您可以用两种语言中的函数调用进行替换。
*   对于更复杂的软件设计，您可以使用自定义错误类。

正如我上次提到的，你可以抓取我为你(和未来的自己)编译的 **Python** 和 **JavaScript** 的并排对比。并排你会看到从**传统的**列表、数组、字典、对象和字符串处理到**现代的**字符串插值、lambdas、生成器、集合、类和其他所有的特性。好好利用它。

<center>✨✨✨
[Get the Ultimate Cheat Sheet of
Equivalents in Python and JavaScript
✨✨✨](https://www.djangotricks.com/goodies/YbnpiLKBmAZi/)</center>

在本系列的下一部分中，我们将看看文本模板、列表解包、lambda 函数、无索引迭代、生成器和集合。敬请期待！

* * *

本杰明·洪的封面照片。

[![](../Images/66ee32021d7b15044ec2062a2fcde02c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mtVy3kJF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/djangotricks/%257E4/QSH4HQuebtY)