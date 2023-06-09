# 用 JS、Python 和 Java 学习算法 2:回文

> 原文：<https://dev.to/tommy3/learning-algorighm-with-js-python-and-java-2-palindrome-42jd>

这是我第二次尝试用三种不同的语言解决同一个问题:JavaScript、Python 和 Java。

[今天的问题](https://github.com/StephenGrider/AlgoCasts/tree/master/completed_exercises/palindrome)是判断给定的字符串是否是回文。

## 1 最直接的解决方法

JavaScript:

```
function isPalindrome(str) {
    const reversed = str.split('').reverse().join('');
    return str === reversed;
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
def is_palindrome(str):
    return str[::-1] == str 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
static boolean isPalindrome(String str) {
    String reversed = new StringBuilder(str).reverse().toString();
    return reversed.equals(str);
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，`return reversed == str;`不起作用，因为`reversed`和`str`指向不同的对象。

# 2。优雅(但多余)

JavaScript:

```
function isPalindrome(str) {
    return str.split('').every((char, i) => {
        return char === str[str.length - i - 1];
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

它的 Python 等价物，没有冗余，可能是:

```
def is_palindrome(str):
    return all([str[i] == str[-i-1] for i in range(len(str)//2)]) 
```

Enter fullscreen mode Exit fullscreen mode

还有 Java:

```
static boolean isPalindrome(String str) {
    int n = str.length();
    return IntStream.range(0, n/2)
            .allMatch(i -> str.charAt(i) == str.charAt(n - i - 1));
} 
```

Enter fullscreen mode Exit fullscreen mode

# 3。具有 For 循环的替代方法

JavaScript:

```
function isPalindrome(str) {
    const n = str.length;
    for (let i = 0; i < Math.floor(n/2); i++) {
        if (str[i] != str[n - i - 1]) {
            return false;
        }
    }
    return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
def is_palindrome(str):
   for i in range(len(str)//2):
         if str[i] != str[-i-1]:
                 return False
   return True 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
static boolean isPalindrome(String str) {
    int n = str.length();
    for (int i = 0; i < n / 2; i++)
        if (str.charAt(i) != str.charAt(n - i - 1)) {
            return false;
        }
    return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:
5 / 2 在 JavaScript 和 Python 3 中是 2.5。
5 / 2 在 Java 里是 2。
5 // 2 在 Python 3 中是 2。

参考资料:
[https://stack overflow . com/questions/11758791/python-3-2-回文](https://stackoverflow.com/questions/11758791/python-3-2-palindrome)
[https://stack overflow . com/questions/4138827/check-string-for-回文](https://stackoverflow.com/questions/4138827/check-string-for-palindrome)