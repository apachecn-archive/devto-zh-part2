# 用 JS、Python 和 Java 学习算法 1:字符串反转

> 原文：<https://dev.to/tommy3/learning-algorighm-with-js-python-and-java-1-string-reversal-33lf>

当我学习自然语言时，我总是喜欢比较它们。有趣的是，例如，英语和德语前缀“un-”、拉丁语前缀“in-”和希腊语前缀“ἀ-”都被认为来自同一个原始印欧语前缀[“*n̥-”](https://en.wiktionary.org/wiki/Reconstruction:Proto-Indo-European/n%CC%A5-)。作为一个(半)编程初学者，我认为尝试以比较的方式学习它们可能也很有趣。

我会跟着 Stephen Grider 的 Udemy 课程[学习编码面试训练营:算法+数据结构](https://www.udemy.com/coding-interview-bootcamp-algorithms-and-data-structure/)，完全用 JavaScript，用 JavaScript、Python 和 Java 解决同样的问题，这只是我碰巧知道基础的三种语言。

课程中的第一个任务是反转给定的字符串。Stephen 介绍了三个 JavaScript 答案。

## 1。简单溶体

JavaScript:

```
function reverse(str) {
    return str.split('').reverse().join('');
} 
```

Enter fullscreen mode Exit fullscreen mode

它在 Python 中的翻译应该是:

```
def reverse(str):
    chars = [char for char in str]
    chars.reverse()
    return ''.join(chars) 
```

Enter fullscreen mode Exit fullscreen mode

在惯用的 Python 中:

```
def reverse(str):
    return str[::-1] 
```

Enter fullscreen mode Exit fullscreen mode

第一个 JS 代码的 Java 等价物应该是:

```
import org.apache.commons.lang3.ArrayUtils;

static String reverse(String str) {
    char[] chars = str.toCharArray();
    ArrayUtils.reverse(chars);
    return String.valueOf(chars);
} 
```

Enter fullscreen mode Exit fullscreen mode

实现它的标准 Java 方式似乎是:

```
static String reverse(String str) {
    return new StringBuilder(str).reverse().toString();
} 
```

Enter fullscreen mode Exit fullscreen mode

## 2。使用 For 循环

JavaScript:

```
function reverse(str) {
    let reversed = '';

    for (let char of str) {
        reversed = char + reversed;
    }

    return reversed;
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
def reverse(str):
    reversed = ''

    for char in str:
        reversed = char + reversed

    return reversed 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
static String reverse(String str) {
    StringBuilder reversed = new StringBuilder();

    for (char character : str.toCharArray()) {
        reversed.insert(0, character);
    }

    return reversed.toString();
} 
```

Enter fullscreen mode Exit fullscreen mode

当给定一个长字符串时，这实际上非常慢，因为它每次都搜索第 0 个位置。

这个动作够快:

```
static String reverse(String str) {
    StringBuilder reversed = new StringBuilder();

    for (int i = str.length() - 1; i >= 0; i--) {
        reversed.append(str.charAt(i));
    }

    return reversed.toString();
} 
```

Enter fullscreen mode Exit fullscreen mode

# 3。使用 Reduce 方法

这不太实际或有效，只是为了探索这些语言提供了什么。

JavaScript:

```
function reverse(str) {
    return str.split('').reduce((reversed, char) => char + reversed, '');
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
from functools import reduce

def reverse(str):
    return reduce(lambda reversed, char: char + reversed, str) 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
static String reverse6(String str) {
    return str.chars()
            .mapToObj(c -> String.valueOf((char) c))
            .reduce("", (reversed, chr) -> chr + reversed);
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望有人会觉得这很有趣或有帮助，我会感谢任何建议或意见。