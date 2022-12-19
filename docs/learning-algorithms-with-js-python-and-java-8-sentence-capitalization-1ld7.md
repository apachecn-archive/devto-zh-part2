# 用 JS、Python 和 Java 学习算法 8:句子大写

> 原文：<https://dev.to/tommy3/learning-algorithms-with-js-python-and-java-8-sentence-capitalization-1ld7>

距离我上一篇文章已经有一段时间了。今天是一个美好而简单的康复日。

这一系列文章以三种不同的语言讲述了斯蒂芬·格里德的 Udemy 课程。JavaScript 解决方案是由斯蒂芬的[。我试着把它们“翻译”成 Python 和 Java。](https://github.com/StephenGrider/AlgoCasts/blob/master/completed_exercises/)

今天的问题是:

> -说明
> 写一个接受字符串的函数。该函数应该
> 大写字符串中每个单词的第一个字母，然后
> 返回大写的字符串。
> -例句
> 大写('一个短句')- >'一个短句'
> 大写('一只懒狐狸')- >'一只懒狐狸'
> 大写('看，它在工作！')->‘看，起作用了！’

# 1:使用内置库的简洁解决方案

JavaScript:

```
function capitalize(sentence) {
    const words = [];

    for (let word of sentence.split(' ')) {
        words.push(word[0].toUpperCase() + word.slice(1));
    }

    return words.join(' ');
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
def capitalize1(sentence: str) -> str:
    words = [word[0].upper() + word[1:] for word in sentence.split()]
    return ' '.join(words) 
```

Enter fullscreen mode Exit fullscreen mode

```
def capitalize2(sentence: str) -> str:
    return ' '.join([word.capitalize() for word in sentence.split()]) 
```

Enter fullscreen mode Exit fullscreen mode

```
def capitalize3(sentence: str) -> str:
    return sentence.title() 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
import java.util.LinkedList;
import java.util.List;

public static String capitalize(String sentence) {
    List<String> words = new LinkedList<>();

    for (String word : sentence.split(" ")) {
        words.add(String.valueOf(word.charAt(0)).toUpperCase() + word.substring(1));
    }

    return String.join(" ", words);
} 
```

Enter fullscreen mode Exit fullscreen mode

# 2:使用循环的强力求解

JavaScript:

```
function capitalize(sentence) {
    let result = sentence[0].toUpperCase();

    for (let i = 1; i < sentence.length; i++) {
        if (sentence[i - 1] === ' ') {
            result += sentence[i].toUpperCase();
        } else {
            result += sentence[i];
        }
    }

    return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
def capitalize(sentence: str) -> str:
    result = ''

    for i, char in enumerate(sentence):
        if i == 0 or sentence[i - 1] == ' ':
            result += char.upper()
        else:
            result += char

    return result 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
public static String capitalize(String sentence) {
    StringBuilder result = new StringBuilder(String.valueOf(sentence.charAt(0)).toUpperCase());

    for (int i = 1; i < sentence.length(); i++) {
        if (sentence.charAt(i - 1) == ' ') {
            result.append(String.valueOf(sentence.charAt(i)).toUpperCase());
        } else {
            result.append(sentence.charAt(i));
        }
    }

    return result.toString();
} 
```

Enter fullscreen mode Exit fullscreen mode

感谢您阅读至此。我希望很快再次见到你！